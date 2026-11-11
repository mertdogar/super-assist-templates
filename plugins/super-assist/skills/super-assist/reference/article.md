# article [topic]

Drafts help center articles from the app's code: each a title, a description and a Markdown body in the form the help center stores, in a collection, attached to the pages of the app it explains, and optionally with a button that starts a journey. They are created as drafts. Readers see nothing of them until a member publishes them in the dashboard.

## 1. Connect and read

Follow [connect.md](connect.md). In one `execute`, `GET /api/v1/projects/{id}/help-center`, `GET /api/v1/projects/{id}/help-center/collections`, `GET /api/v1/projects/{id}/pages` and `GET /api/v1/projects/{id}/journeys`, and return all four. The collections answer lists every article by its `title`, with its `state` and `pages`; it is empty before the help center's first write. When the catalog is empty, or the scan has pages it lacks, run [sync.md](sync.md) first.

## 2. Propose

With a topic, draft one article about it. Without one, propose up to five an end user of this app would look for, unlike the existing articles: how to do what its main pages and journeys are for, what a setting changes, what to do about an error the app shows. Let the user pick.

For each, read the code it explains: the route, the page's `instructions`, the labels of its buttons and fields, its validation and its error messages. An article names what the app shows, in the app's own words, and says what to press and what happens next.

Show each draft's title, description, collection, outline and pages before creating it.

## 3. Write the body

- A `title` of up to 120 characters and a `description` of up to 300, both plain text. A `body` of up to 100,000 characters of Markdown. The public page shows the title as the page's heading, so the body's headings start at `##`.
- Markdown as GitHub writes it: headings, bold, italic, strikethrough, lists, task lists, links, tables, code blocks with a language, quotes and dividers.
- A callout is a GitHub alert of one of five kinds, `NOTE`, `TIP`, `IMPORTANT`, `WARNING` or `CAUTION`:

  ```md
  > [!WARNING]
  > A refund can't be undone.
  ```

- A link to another article of the help center is `[text](article:<id>)`, with the article's `id` from the collections answer. Readers get its current address, or the text alone while it isn't published.
- An image is `![What it shows](images/<id>.<ext>)`, the `src` an upload to this article answered; see [step 5](#5-images-and-videos).
- A video plays when a link to it stands alone on a line: a YouTube or Vimeo address, or the `videos/<id>.mp4` an upload answered. The same link inside a sentence stays a link.

A body can't hold raw HTML of any kind (`<br>`, `<details>`, `<img>`, `<kbd>`, an HTML comment), a link or image to a `javascript:` or `data:` address, or an image from anywhere but an upload to the article. The save answers `400 body_refused` with each one in `details.refused`.

Every save stores the body in one normal form, and the answer's `body` is what is stored. Text after a code block's language, such as `title="x"`, is dropped, and reference-style links are written inline.

## 4. Create

In one `execute`:

1. each new collection the user picked, `POST /api/v1/projects/{id}/help-center/collections` with a `name` of up to 100 characters and a `description` of up to 300, which readers see under it. It answers `201` with its `id`. The help center's first write also creates the collection named General;
2. each article, `POST /api/v1/projects/{id}/help-center/articles` with `title`, `description`, `body` and `collectionId`, the first collection when left out. It answers `201` with the article, its `id`, and its `state` `draft`;
3. each article's pages, `PUT /api/v1/projects/{id}/help-center/articles/{articleId}/pages` with `{ "pages": ["/orders", "/orders/:id"] }`, paths from the page catalog. The list replaces the article's;

and return what they answered. Link articles to each other once both have an `id`: `PATCH /api/v1/projects/{id}/help-center/articles/{articleId}` with the whole `body`. A `PATCH` changes the draft only, a published article's too: its readers keep the published copy until a member publishes again.

An article goes at the end of its collection. `POST /api/v1/projects/{id}/help-center/articles/{articleId}/move` with a `position` from 0, and a `collectionId` to move it to another, reorders it; `POST /api/v1/projects/{id}/help-center/collections/{collectionId}/move` with a `position` reorders a collection.

### The journey button

When an article explains what a published journey walks through, offer it the button: `PUT /api/v1/projects/{id}/help-center/articles/{articleId}/journey` with the journey's `id` as `journeyId`, and a `label` of up to 40 characters when "Show me in the app" doesn't fit. Only a journey with `publishedSteps` whose first step's path has no parameter fits.

The button opens the help center's `appUrl` at that step's path with `?journey=<name>`, so it needs `appUrl`. When the help center has none, ask the user for the address their end users open the app at, never a `localhost` one, and on their word `PATCH /api/v1/projects/{id}/help-center` with `{ "appUrl": "https://app.example.com" }` first.

## 5. Images and videos

`execute`'s `request()` sends JSON only, so uploads go through curl with an organization key, as [connect.md](connect.md)'s curl fallback says. Without a key, leave images out of the body and list in the hand-off where each goes and what it shows: a member adds them in the editor with **Image**.

Upload each image to its article, with its type, then put the answer's `src` in that article's body. A `src` works only in the article it was uploaded to.

```sh
key=$(sed -n 's/^SUPER_ASSIST_API_KEY=//p' .env.local)
curl -s -X POST https://superassist.app/api/v1/projects/<project-id>/help-center/articles/<article-id>/images \
  -H "Authorization: Bearer $key" -F "file=@docs/orders.png;type=image/png"
```

It answers `201` with `{ "src": "images/<id>.png", "contentType": "image/png", "size": 48213 }`. Then `PATCH` the article's `body` with `![The Orders page, its Ship button pressed](images/<id>.png)` where the image goes.

A video's request body is the file itself, sent with its type:

```sh
key=$(sed -n 's/^SUPER_ASSIST_API_KEY=//p' .env.local)
curl -s -X POST https://superassist.app/api/v1/projects/<project-id>/help-center/articles/<article-id>/videos \
  -H "Authorization: Bearer $key" -H "Content-Type: video/mp4" --data-binary @docs/ship-an-order.mp4
```

It answers `201` with `{ "src": "videos/<id>.mp4", … }`. Put a link to it alone on a line. Uploading video is on the Pro plan; a YouTube or Vimeo link the user already has works on every plan.

## What is refused, and what to do

| Answer                                                                     | Why                                                                                                            | Do                                                                                                                                                                                      |
| -------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `400 body_refused`                                                         | Raw HTML, a `javascript:` or `data:` address, or an image from elsewhere, each in `details.refused`            | Write it in Markdown: separate paragraphs or a list for `<br>`, a `###` heading for `<details>`, bold for `<kbd>`. Upload an outside image, or leave it out and name it in the hand-off |
| `400 invalid_request`                                                      | A title over 120 characters, a description over 300 or a body over 100,000, as its message says                | Shorten it, or split the article in two linked with `article:`                                                                                                                          |
| `400 unsupported_image_type`                                               | Not a PNG, JPEG, GIF or WebP, SVG included, or the file isn't the type sent                                    | Convert it to PNG, and send a `type=` that matches                                                                                                                                      |
| `413 image_too_large`                                                      | Over 5 MB                                                                                                      | Resize or compress it, such as to WebP                                                                                                                                                  |
| `409 too_many_images`                                                      | 50 images uploaded to the article, those since removed from its body included                                  | Split the article                                                                                                                                                                       |
| `400 unsupported_video_type`, `411 length_required`, `413 video_too_large` | Not an MP4 or WebM, sent without its length, or over 100 MB                                                    | Send it with `--data-binary` and its `Content-Type`, encode a smaller MP4, or link a YouTube or Vimeo video                                                                             |
| `402 plan_limit_reached`                                                   | A video upload on the Free plan, or past the 10 GB of video Pro includes                                       | Link a YouTube or Vimeo video                                                                                                                                                           |
| `404 journey_not_found`, `409 journey_not_eligible`                        | The journey is deleted, unpublished, or starts on a page with a parameter                                      | Leave the button off, and say why in the hand-off                                                                                                                                       |
| `409 app_url_required`                                                     | The help center has no link to the app                                                                         | Ask for the app's address, as in [the journey button](#the-journey-button)                                                                                                              |
| `403 forbidden`                                                            | Publishing, unpublishing, turning the help center on or off, or its handle or domain, with an organization key | Leave it to a member; see [Done](#done)                                                                                                                                                 |

## Done

Done when every article the user picked answered `201`, with its pages and journey button set. This command drafts only. Publishing an article, turning the help center on, and choosing its handle or its own domain are a member's job in the dashboard: the API refuses an organization key all of them, and on the MCP server, which acts as the member, leave them to the member too.

The hand-off names each draft with its collection and pages, lists any image a member still has to add, and says where to publish: on the project's **Help center** tab, each article's **Publish**; then, while the help center is off, on **Settings**, a **Handle** under **Address** and **Help center on** under **Public**. The Free plan publishes up to 25 articles per help center.

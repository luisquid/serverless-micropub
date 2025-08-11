# serverless-micropub

This is an example for setting up a serverless micropub endpoint. It has been tested
and is actively used to publish posts to my site [benji.dog](https://benji.dog) by
committing code to my repo [benjifs/benji](https://github.com/benjifs/benji) using
[benjifs/micropub](https://github.com/benjifs/micropub) and [benjifs/github-store](https://github.com/benjifs/github-store).

[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/benjifs/serverless-micropub)

Clicking the "Deploy to Netlify" button will fork this repository and create a new
[Netlify](https://www.netlify.com/) site which, after configuration, can be used
with any [Micropub Client](https://indieweb.org/Micropub/Clients) to add content to
your site.

## Setup

When setting this project up on Netlify, you should be asked to configure the following
**environment variables**:

| Environment Variable | Description |
| --- | --- |
| `ME` | Your website url (https://example.com/) |
| `TOKEN_ENDPOINT` | Endpoint to validate token (https://tokens.indieauth.com/token) |
| `GITHUB_TOKEN` | GitHub [Personal access token](https://github.com/benjifs/github-store#setup) |
| `GITHUB_USER` | Username for repo where posts are added to |
| `GITHUB_REPO` | Name of repo where posts are added to |

By default, posts will be created under `/src/${postType}/${slug}.md` where
`postType` matches one of the [post types](#post-types) supported by the
endpoint. Files will be uploaded to the media endpoint under `/uploads`. You can
change this behavior by changing the value of `contentDir` and `mediaDir` in [/netlify/config.js](https://github.com/benjifs/serverless-micropub/blog/main/netlify/config.js).

For additional configuration options, checkout the [main project repo](https://github.com/benjifs/micropub/tree/v2#configuration).

## Usage

### **POST** `/micropub`
Send a `application/json`, `x-www-form-urlencoded`, or `multipart/form-data` request
to create a post. See [3.6.3 Request](https://micropub.spec.indieweb.org/#request).

Can also handle [update](https://micropub.spec.indieweb.org/#update), [delete](https://micropub.spec.indieweb.org/#delete), and [undelete](https://micropub.spec.indieweb.org/#example-15) actions.

#### Response
* `201` on success with a `Location` header for the URL of the created post.
* `400` an error occurred with approriate error message.
* `401` or `403` if unauthorized or invalid token was provided.

### **GET** `/micropub`
* `q=config`: Get micropub configuration. See [3.7.1 Configuration](https://micropub.spec.indieweb.org/#configuration).
* `q=media-endpoint`: Get media endpoint if configured. See [3.6.1 Discovery](https://micropub.spec.indieweb.org/#discovery)
* `q=post-types`: Get supported post types. See [discussion about proposed extension](https://indieweb.org/Micropub-extensions#Query_for_Supported_Vocabulary).
* `q=syndicate-to`: Get a list of supported syndication targets. See [3.7.3 Syndication Targets](https://micropub.spec.indieweb.org/#syndication-targets)
* `q=source&url=`: Get properties for post in `url`. Optionally can also add
`properties=` to only get those properties as the response. See [3.7.2 Source Content](https://micropub.spec.indieweb.org/#source-content).

### **POST** `/media`
Send a `multipart/form-data` request to upload a file. See [3.6.3 Request](https://micropub.spec.indieweb.org/#request).

#### Response
* `201` on success with a `Location` header for the URL of the uploaded item.
* `400` if no file was uploaded with approriate error message.
* `401` or `403` if unauthorized or invalid token was provided.

### **GET** `/media`
* `q=source`: Get list of most recently uploaded files. Optionally can use `offset`
and `limit` to implement pagination. See [discussion about proposed extension](https://github.com/indieweb/micropub-extensions/issues/14).

## Post Types

The current supported post types are:
* [article](https://indieweb.org/article)
* [bookmark](https://indieweb.org/bookmark)
* [like](https://indieweb.org/like)
* [listen](https://indieweb.org/listen)
* [note](https://indieweb.org/note)
* [photo](https://indieweb.org/photo)
* [play](https://indieweb.org/play)
* [read](https://indieweb.org/read)
* [repost](https://indieweb.org/repost)
* [rsvp](https://indieweb.org/rsvp)
* [watch](https://indieweb.org/watch)
* [reply](https://indieweb.org/reply)

You can modify which post types are supported by setting the value for `post-types`
in [/netlify/config.js](https://github.com/benjifs/serverless-micropub/blog/main/netlify/config.js).

> **Note:** If a post does not fit under a specific type, it will default to be
of type `note`.

## Troubleshooting
* `ME` should have a trailing slash
* If you make a change to the environment variables in Netlify, you must redeploy
otherwise it will continue using the old variables.

## References
* [Micropub spec](https://www.w3.org/TR/micropub)
* [Micropub media endpoint](https://www.w3.org/TR/micropub/#media-endpoint)
* [Handling a micropub request](https://indieweb.org/Micropub#Handling_a_micropub_request)
* [Micropub extensions](https://indieweb.org/Micropub-extensions)

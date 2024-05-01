# Tutorial

Use Pipedown to make a CRUD blog app. You should be familiar with Pipedown before reading this tutorial. Checkout the [[Pipedown Cheatsheet]] for a reference.

## Post Store
[[postStore]]
A conditional pipe that can create, read, update, and delete posts using `Deno.Kv`. Try it out in the repl!
`./bash repl.sh`

```js
await postStore.process({post: {create: {title: "New Blog Post", body: "# New Blog Post \n Some copy"}}})

await postStore.process({post: {read: "new-blog-post"}})

await postStore.process({post: {list: true}})

await postStore.process({post: {update: {slug: "new-blog-post", title: "New Blog Post", body: "# New Blog Post \n Some updated copy"}}})

await postStore.process({post: {delete: "new-blog-post"}})
```



## Start Server
[[start]]
A pipe that only executes functions based on a request path.

Pipedown can check a request path, using [URLPatterns](https://developer.mozilla.org/en-US/docs/Web/API/URLPattern). Just prefix a list item with `route:` followed by the pattern to conditionally execute that function.

You can run a pipe as a server like this: `deno run -A --unstable-kv run ./.pd/start/server.ts`. Pipedown automatically generated the server file for you.

```md
## Post page <-- function name (optional)
- route: /post/* <-- URLPattern
- ```ts <-- codeblock becomes a function
    const post = await Deno.readTextFile(`./html/${post}.html`)
    input.body = post;
    input.responseOptions.headers['content-type'] = 'text/html; charset=utf-8'
    ```
```

## API
[[pdcrudblog/start#API]] reads the request path and decides which action in [[pdcrudblog/postStore]] to trigger.

API requests can be made to endpoints like `/api/create`, or, in this case, a form can be submitted to that path.

## Pages
[[pdcrudblog/start#Index Page]], [[pdcrudblog/start#Post page]] and [[pdcrudblog/start#Form]] serve different pages and pull HTML from the `./html` directory.

[[pdcrudblog/start#Form]] is the most interesting example as it shows how you can conveniently piece together HTML pages using template literals.


# postStore
There's no need to over complicate things. Posts will have a "key", `title`, `slug`, and a `body`.

I'm not even sure how Deno.KV works! This is my first time trying it out. So let's spin up a REPL and try.

`bash ./pdrepl.sh`

For starters we need to init the store:
```ts
input.kv = await Deno.openKv();
```

## postCreate
To build our handlers we can create separate pipe functions that only get triggered when the right message is received. 

In this case, `postCreate` will only be called if the global `input` object looks like this:

```js
{
    post: {
        create: {
            title: "New Blog Post",
            body: "# New Blog Post \n Some copy"
        }
    }
}
```

Pipedown uses the new JSON Pointer standard and provides the `$p` helper to help you conveniently fetch information from objects.

To test this out, we can use the REPL and run the following command:
`await postStore.process({post: {create: {title: "New Blog Post", body: "# New Blog Post \n Some copy"}}})`

- if: /post/create
- ```ts
    const post = $p.get(input, '/post/create')
    post.slug = post.title.toLowerCase().replace(' ', '-');
    input.post = input.kv.set(["blog", "posts", post.slug], post);
    ```

## postRead
We follow the same process for the remaining functions, don't forget to read up on [Deno KV](https://deno.land/api@v1.42.4?s=Deno.Kv&unstable=) to learn more!

To test this out, we can use the REPL and run the following command:
`await postStore.process({post: {read: "new-blog-post"}})`

- if: /post/read
- ```ts
    const slug = $p.get(input, '/post/read')
    input.post = await input.kv.get(["blog", "posts", slug]);
    ```

## postList
To test this out, we can use the REPL and run the following command:
`await postStore.process({post: {list: true}})`

- if: /post/list
- ```ts
    input.posts = [];
    for await (const entry of input.kv.list({prefix: ['blog', 'posts']})){
        input.posts.push(entry);
    }
    ```

## postUpdate
To test this out, we can use the REPL and run the following command:
`await postStore.process({post: {update: {slug: "new-blog-post", title: "New Blog Post", body: "# New Blog Post \n Some updated copy"}}})`

- if: /post/update
- ```ts
    const post = $p.get(input, '/post/update')
    post.slug = post.title.toLowerCase().replace(' ', '-');
    input.post = await input.kv.set(["blog", "posts", post.slug], post);
    ```

## postDelete
To test this out, we can use the REPL and run the following command:
`await postStore.process({post: {delete: "new-blog-post"}})`

- if: /post/delete
- ```ts
    const post = $p.get(input, '/post/delete')
    input.post = await input.kv.set(["blog", "posts", post.slug]);
    ```
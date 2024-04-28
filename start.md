# Start
Any pipe can be executed as a server. However, you will want to create an "entry" pipe that delegates the request to other pipes, much a like a route file in a traditional framework.

## Index Page
Pipedown can check a request path, using [URLPatterns](https://developer.mozilla.org/en-US/docs/Web/API/URLPattern). Just prefix a list item with `route:` followed by the pattern to conditionally execute that function.

We will use a [blog template](https://github.com/davidgrzyb/tailwind-blog-template/tree/master) to make our lives easier!

- route: /
- ```ts
    const page = await Deno.readTextFile('./html/index.html')
    input.body = page;
    input.responseOptions.headers['content-type'] = 'text/html; charset=utf-8'
    ```

## Post page
- route: /post/*
- ```ts
    const page = await Deno.readTextFile('./html/post.html')
    input.body = page;
    input.responseOptions.headers['content-type'] = 'text/html; charset=utf-8'
    ```

## API
The [[pdcrudblog/postStore]] is ready, so we just need to map from a request to `input` structure the pipe is expecting.

- route: /api/:action
- ```ts
    import postStore from 'postStore'

    input.payload = {};
    const action = $p.get(input, '/route/pathname/groups/action')

    try {
        const formData = await input.request.formData()
        input.payload =  {
            title: formData.get('title'),
            body: formData.get('body'),
        }
        const url = new URL(input.request.url)
        input.response = Response.redirect(url.origin)
    } catch(e) {
        // not form data
    } finally {
        const output = await postStore.process({post: {[action]: input.payload}})
        console.log(input);
        console.log(output);
        input.body = $p.get(output, '/posts') || $p.get(output, '/post')
    }
    ```

## Form
This is just a [form component from TailwindUI](https://tailwindui.com/components/application-ui/forms/form-layouts) wrapped in the blog layout, take a look inside!

- route: /form/*
- ```ts
    import formPage from 'formPage'
    const output = await formPage.process();
    input.body = output.layout;
    input.responseOptions.headers['content-type'] = 'text/html; charset=utf-8'
    ```

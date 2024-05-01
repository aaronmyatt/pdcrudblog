# Pipedown Cheatsheet

Pipedown is a markdown based framework for building anything! Servers, CLIs, browser scripts, and more. It is built on top of Deno and uses markdown files as pipelines. Each typescript codeblock in a markdown file is a function that takes an input object and returns an output object.

## Getting Started
Install and run the Pipedown CLI. The CLI will watch for changes and build pipes (Typescript modules) from each markdown file in your project directory and add them to the `.od` directory.

### Built on Deno
Pipedown is built on top of [Deno](https://docs.deno.com/runtime/manual), so you have access to [Deno](https://docs.deno.com/runtime/manual) and all the tools that come with it.

## Pipes
Each pipe can be called the same way:
- From a script: `await pipeName.process(input)`
- From another pipe: 
- ```
    import pipeName from 'pipeName'
    const output = await pipeName.process(input)`
    ```
- From the REPL: `await pipeName.process(input)`
- From the Pipedown CLI: `PD run pipeName --input='{"some": "data}'`
- As a server: `deno run -A .pd/pipeName/server.ts`
- As a CLI app: `deno run -A .pd/pipeName/cli.ts`

## Input
The input object is passed to each pipe. It is a shared object that can be modified by each pipe. The input object is passed to each code block in a markdown file.

Access the input with [JSON Pointers](https://datatracker.ietf.org/doc/html/rfc6901) like this: `$p.get(input, '/path/to/data')`

## Functions
Each codeblock in a markdown file is a function that takes an input object and returns an output object. Functions are async by default, use `await` to resolve promises.

Use markdown lists to conditionally execute code blocks. Use `if: /path/to/data` to check the input object for something truthy.

```md
## Post page <-- function name (optional)
- if: /post/create <-- JSON Pointer checks input object
- ```ts <-- codeblock becomes a function
    import pipeName from 'pipeName' <-- Pipedown builds an import map from your project

    const post = $p.get(input, '/post/create')
    post.slug = post.title.toLowerCase().replace(' ', '-');
    input.post = input.kv.set(["blog", "posts", post.slug], post);
    ```
```

## Config
Add a JSON codeblock for pipe specific config. Add a `config.json` file for global project config. Each JSON block is merged with `config.json` . Use `$p.get(opts, '/config/key')` to access config data.

```json
{
    "key": "value"
}
```

## Server routing
Execute any pipe as a Deno server `deno run -A .pd/pipeName/server.ts`. 

Use markdown lists to route requests to code blocks. Use a URLPattern, like, `route: /blog/:post`, to check the `input.request` path and conditionally execute the function. Use `input.responseOptions`, `input.body` or `input.response` to set the server response.

## Build
Add a `build` array to the JSON config block to export pipes as bundled esm or iife scripts.

```json
{ "build": ["esm"] }
```

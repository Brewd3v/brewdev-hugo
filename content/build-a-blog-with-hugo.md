---
title: "How to build a simple blog with Hugo and Tailwind CSS"
draft: false
---

Hello there! In this post, I will show you how to build a simple blog with Hugo and Tailwind CSS. And how to deploy it to Netlify.

1. [Create a new Hugo site](#create-a-new-hugo-site)
2. [Add a theme or create your own ](#add-a-theme-or-create-your-own)
3. Add Tailwind CSS
4. Add a blog post
5. Deploy to Netlify

I won't go into too much detail about Hugo and Tailwind CSS, but I will link to some resources if you want to learn more about them.

- https://gohugo.io/getting-started/quick-start/
- https://tailwindcss.com/docs/installation


### Create a new Hugo site


```bash
hugo new site new-blog
cd new-blog
git init
```

Unlike other Static Site Generators, Hugo doesn't come with a default theme. So you have to choose one or create your own. We'll creating a super simple "theme" in this post.

### Add a theme or create your own 

Add a theme as a submodule like this (feel free to do this and skip the "create your own" part):

```bash
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke themes/ananke
```

then set the theme in your `config.toml` file to activate it:

```toml
theme = "ananke"
```

For the sake of simplicity, we'll create templates similar to how you would do in a theme. It's basically the same as a "theme" we just won't be using the `themes` folder.

Create the following folders and files:

```bash
-- layouts 
    -- _default
        -- baseof.html
        -- list.html
        -- single.html
```

in `baseof.html` add the following:

you can think of `{{ block "main" . }}{{ end }}` as a slot. It's where the content of the page will be rendered.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{{ .Title }}</title>
</head>
<body>
    {{ block "main" . }}{{ end }}
</body>
</html>
```

in `list.html` add the following:

```html
{{ define "main" }}
    <h1>{{ .Title }}</h1>
    {{ range .Data.Pages }}
        <a href="{{ .Permalink }}">{{ .Title }}</a>
    {{ end }}
{{ end }}
```

in `single.html` add the following:

```html
{{ define "main" }}
    <h1>{{ .Title }}</h1>
    {{ .Content }}
{{ end }}
```

Now we can add some content to our site. 

**Create a new post with:**

```bash
hugo new my-first-post.md
```

this will create a new file in `content/my-first-post.md` and `archetypes/default.md` file. You can edit the `archetypes/default.md` file to add some default frontmatter to your posts.

**and add some content to the new post:**

```md
---
title: "My first post"
draft: false
---

Hello there! This is my first post!
```

**Now you can run `hugo server` to start a local server and see your site.**

```bash
hugo server -D
```

behold the glorious build speed of Hugo! ⚡⚡⚡

### Add Tailwind CSS

Now let's add Tailwind CSS to our site. We'll be doing this manually. This can be done as part of a theme if prefer to go that route.

**Install Tailwind CSS:**

Initialize a new npm project and install Tailwind CSS:

```bash
npm init -y
npm install tailwindcss
npx tailwindcss init
```

Replace the content options in `tailwind.config.js`:

```js
..
content: ["./content/**/*.{html,js}", "./layouts/**/*.{html,js}"]
..
```

create a `assets/styles.css` file and add the following:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

**Add a a dev and build script to `package.json`:**

```json
"scripts": {
"dev": "npx tailwindcss -i assets/tailwind.css -o static/style.css --jit --watch",
"build": "npx tailwindcss -i assets/tailwind.css -o static/style.css --jit  --minify"
},
```

Add some tailwind classes to any of you layouts, run the dev script and start up hugo and viola! 🎉

```bash
npm run dev
hugo server -D --noHTTPCache
```

### Deploy to Netlify

There are many ways to deploy a Hugo site to Netlify. I'll show you how to do it using the Netlify CLI.

**Install the Netlify CLI:**

```bash
npm install netlify-cli -g
```

**Login to Netlify:**

```bash
netlify login
```

**Create a new Netlify site:**

```bash
netlify init
```

**Add a `netlify.toml` file:**

```toml
[build]
  publish = "public"
  command = "npm run build"
```

**Deploy to Netlify:**

```bash
git add .
git commit -m "initial commit"
git push origin main
netlify deploy
```
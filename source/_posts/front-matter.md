---
title: Front Matter
author: Hexo, NexT
img_src: ./img/front-matter.png
categories: Theme Settings
tag:
  - theme
  - markdown
---

> Front-matter is a block of YAML or JSON at the beginning of the file that is used to configure settings for your writings. Front-matter is terminated by three dashes when written in YAML or three semicolons when written in JSON.
>
> — from [Hexo site](https://hexo.io/docs/front-matter).

#### YAML

```yml
---
title: Hello World
date: 2013/7/13 20:46:25
---
```

#### JSON

```json
"title": "Hello World",
"date": "2013/7/13 20:46:25"
```

<br>

### Settings & Their Default Values

---

| Setting           | Type      | Description                                                  | Default                                       |
| :---------------- | --------- | ------------------------------------------------------------ | --------------------------------------------- |
| `author`          | `string`  | Author name for post copyright                               | `author` in Hexo `_config.yml`                |
| `title`           | `string`  | Title                                                        | Filename (post only)                          |
| `date`            | `string`  | Pulished date                                                | File created date                             |
| `updated`         | `string`  | Updated date                                                 | File updated date                             |
| `comments`        | `boolean` | Enables comment feature for the post                         | true                                          |
| `tags`            | `object`  |                                                              | None                                          |
| `categories`      | `string`  |                                                              | None                                          |
| `permalink`       | `string`  | Overrides the default permalink of the post. Permalink sould end with `/` or `.html` | null                                          |
| `excerpt`         | `string`  | Page excerpt in plain text. Use [this plugin](https://hexo.io/docs/tag-plugins#Post-Excerpt) to format the text | None                                          |
| `disableNunjucks` | `boolean` | Disable rendering of Nunjucks tag `{{ }}`/`{% %}` and [tag plugins](https://hexo.io/docs/tag-plugins) when enabled | false                                         |
| `lang`            | `string`  | Set the language to override [auto-detection](https://hexo.io/docs/internationalization#Path) | Inherited from `_config.yml`                  |
| `post_link`       | `string`  | Post link                                                    | None                                          |
| `description`     | `string`  | Docs [here](https://theme-next.js.org/docs/theme-settings/posts.html#Preamble-Text) | None                                          |
| `direction`       | `string`  | Avaliable value: `rtl`                                       | None                                          |
| `header`          | `boolean` | Whether to display the post header in index page             | `true`                                        |
| `mathjax`         | `boolean` | MathJax support                                              | `math.every_page` in `_config.yml`            |
| `sidebar`         | `boolean` | Whether the sidebar will be shown                            | Depends on `sidebar.display` in `_config.yml` |
| `copyright`       | `boolean` | Whether to display copyright notices below the post with `theme.creative_commons.license` and `theme.creative_commons.post` enabled | `true`                                        |
| `sticky`          | `number`  | Pin the post to the top of the index page. [hexo-generator-index](https://github.com/hexojs/hexo-generator-index) plugin required | 0                                             |

#### Example

```yml
categories:
  - Sports
  - Baseball
tags:
  - Injury
  - Fight
  - Shocking
toc:
  enable: true
  number: false
  max_depth: 3
reward_settings:
  enable: true
  comment: Buy me a coffee
quicklink:
  enable: true
  delay: true
  timeout: 3000
  priority: true
```

### Undocumented Front-matter Variables

---

| Setting  | Type     | Example                                                      |
| :------- | -------- | ------------------------------------------------------------ |
| `link`   | `string` | [link-post](https://github.com/hexojs/hexo-theme-unit-test/blob/master/source/_posts/link-post.md) |
| `photos` | `array`  | [gallery-post](https://github.com/hexojs/hexo-theme-unit-test/blob/master/source/_posts/gallery-post.md) |

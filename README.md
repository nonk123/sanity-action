# sanity-action

A GitHub composite-action that builds your website using [sanity](https://github.com/nonk123/sanity) fetched from the [latest stable release](https://github.com/nonk123/sanity/releases/latest).

Here's a complete `publish.yml` workflow example [from my GitHub Pages website](https://github.com/nonk123/nonk.dev):

```yml
name: Publish to GitHub Pages

on:
  workflow_dispatch:
  push:

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  publish:
    name: Render & publish
    runs-on: ubuntu-24.04
    environment:
      name: github-pages
      url: ${{ steps.publish.outputs.page_url }}
    permissions:
      contents: read
      pages: write
      id-token: write
    steps:
      - name: Checkout
        uses: actions/checkout@v6
      - name: Publish
        id: publish
        uses: nonk123/sanity-action@master # this action being used
        with: { push: true }
```

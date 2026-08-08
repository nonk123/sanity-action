# `sanity-action`

A GitHub composite-action that builds your website using [sanity](https://github.com/nonk123/sanity) (fetched from the [latest stable release](https://github.com/nonk123/sanity/releases/latest)) and optionally deploys it to [GitHub Pages](https://pages.github.com) and/or [Neocities](https://neocities.org).

## Examples

Let's dive right in with a few common usage examples.

### Push to [Neocities](https://neocities.org)

Specify your Neocities API key (see [this article](https://github.com/bcomnes/deploy-to-neocities#usage) to find out how you get one) by adding an actions-scoped secret to your repository and referencing it in the `neocities_secret` argument for this workflow. See the infographic below:

![GitHub infographic showing how to add an actions-scoped secret for a repository.](.github/assets/infographic.png)

Then, run your `sanity-action` step as follows to deploy to Neocities:

```yml
- name: Publish
  id: publish
  uses: nonk123/sanity-action@master
  with:
    neocities_secret: ${{ secrets.NEOCITIES_API_KEY }}
    neocities_supporter: false # set this to true if you are a Neocities Supporter
```

### Push to [GitHub Pages](https://pages.github.com)

You will need to set additional permissions for the GitHub token used in the CI workflow, or else deployment will fail. Here's a job example with those permissions set:

```yml
jobs:
  example:
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.publish.outputs.page_url }}
    permissions:
      contents: read
      pages: write
      id-token: write
    steps:
      - # ...
```

After getting the permissions, run with `push_to_github_pages` set to true to push to GitHub pages:

```yml
- name: Publish
  id: publish
  uses: nonk123/sanity-action@master
  with:
    push_to_github_pages: true
```

### Complete Workflow Example

For completeness' sake, here's a full [`publish.yml`](https://github.com/nonk123/nonk.dev/blob/fd8287454c469dd1685140a1d2db37a76244af70/.github/workflows/publish.yml) workflow example [from my GitHub Pages site](https://github.com/nonk123/nonk.dev):

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
        uses: nonk123/sanity-action@master
        with: { push_to_github_pages: true }
```

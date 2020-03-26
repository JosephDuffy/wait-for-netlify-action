# Wait for Netlify Deploy — A GitHub Action ⏱

Do you have other Github actions (Lighthouse, Cypress, etc) that depend on the Netlify Preview URL? This action will wait until the url is available before running the next task.

This is a fork of [JakePartusch/wait-for-netlify-action](https://github.com/JakePartusch/wait-for-netlify-action) that uses the deployment for the commit, rather than for the PR.

## Inputs

### `site_name`

**Required** The name of the Netlify site to reach `https://{site_name}.netlify.com`

### `max_timeout`

Optional — The amount of time to spend waiting on Netlify. Defaults to `60` seconds

## Outputs

### `url`

The netlify deploy preview url that was deployed.

## Example usage

Basic Usage

```yaml
steps:
  - name: Waiting for Netlify Preview
    uses: josephduffy/wait-for-netlify-action@v1
    id: wait-for-netlify-preview
    with:
      site_name: "YOUR_SITE_NAME"
      max_timeout: 60
```

<details>
<summary>Complete example with Lighthouse</summary>
<br />

```yaml
name: Lighthouse

on: [pull_request]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v1
      - name: Use Node.js 12.x
        uses: actions/setup-node@v1
        with:
          node-version: 12.x
      - name: Install
        run: |
          npm ci
      - name: Build
        run: |
          npm run build
      - name: Waiting for 200 from the Netlify Preview
        uses: josephduffy/wait-for-netlify-action@v1
        id: wait-for-netlify-preview
        with:
          site_name: "YOUR_SITE_NAME"
      - name: Lighthouse CI
        run: |
          npm install -g @lhci/cli@0.3.x
          lhci autorun --upload.target=temporary-public-storage --collect.url=${{ steps.wait-for-netlify-preview.outputs.url }} || echo "LHCI failed!"
        env:
          LHCI_GITHUB_APP_TOKEN: ${{ secrets.LHCI_GITHUB_APP_TOKEN }}
```

</details>

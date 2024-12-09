# Deploy Jekyll Site to GitHub Pages Workflow

This GitHub Actions workflow automates the process of building and deploying a Jekyll site to GitHub Pages. It is triggered on pushes to the `master` branch and can also be executed manually from the Actions tab. The workflow consists of two main jobs: `build` and `deploy`. The `build` job compiles the Jekyll site, while the `deploy` job handles the deployment to GitHub Pages. The workflow is designed to ensure that only one deployment occurs at a time, allowing in-progress deployments to complete without interruption.

## Table of Contents

  * [Workflow Triggers](#workflow-triggers)
  * [Permissions](#permissions)
  * [Concurrency Control](#concurrency-control)
  * [Jobs](#jobs)
    * [Build Job](#build-job)
    * [Deployment Job](#deployment-job)

## Workflow Triggers

```yaml
on:
  push:
    branches: ["master"]
  workflow_dispatch:
```

- **push**: This trigger activates the workflow whenever there is a push to the `master` branch. It ensures that the latest changes are built and deployed automatically.
- **workflow_dispatch**: This allows users to manually trigger the workflow from the GitHub Actions tab, providing flexibility for deployments outside of regular push events.

## Permissions

```yaml
permissions:
  contents: read
  pages: write
  id-token: write
```

- **contents: read**: Grants the workflow read access to the repository contents, necessary for checking out the code.
- **pages: write**: Allows the workflow to deploy content to GitHub Pages.
- **id-token: write**: Enables the workflow to generate an ID token, which may be required for certain authentication scenarios.

## Concurrency Control

```yaml
concurrency:
  group: "pages"
  cancel-in-progress: false
```

- **group**: This defines a concurrency group named "pages". It ensures that only one deployment job runs at a time.
- **cancel-in-progress**: Set to `false`, this option allows in-progress deployments to complete even if new deployment requests are made, ensuring that ongoing production deployments are not interrupted.

## Jobs

### Build Job

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Ruby
        uses: ruby/setup-ruby@8575951200e472d5f2d95c625da0c7bec8217c42 # v1.161.0
        with:
          ruby-version: '3.1' # Not needed with a .ruby-version file
          bundler-cache: true # runs 'bundle install' and caches installed gems automatically
          cache-version: 0 # Increment this number if you need to re-download cached gems
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Build with Jekyll
        run: bundle exec jekyll build --baseurl "${{ steps.pages.outputs.base_path }}"
        env:
          JEKYLL_ENV: production
          PAGES_REPO_NWO: ${{ github.repository }}
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
```

- **runs-on**: Specifies that the job will run on the latest version of Ubuntu.
- **steps**: A series of actions that the job will execute:
  - **Checkout**: Uses the `actions/checkout` action to pull the repository code into the workflow environment.
  - **Setup Ruby**: Utilizes the `ruby/setup-ruby` action to install Ruby and manage dependencies. It caches installed gems to speed up subsequent builds.
    - **ruby-version**: Specifies the Ruby version to use.
    - **bundler-cache**: Automatically runs `bundle install` and caches gems.
    - **cache-version**: A version number to manage cache invalidation.
  - **Setup Pages**: Configures the GitHub Pages environment using `actions/configure-pages`.
  - **Build with Jekyll**: Executes the Jekyll build command, setting the environment variable `JEKYLL_ENV` to `production` and using the base path from the previous step.
  - **Upload artifact**: Uses `actions/upload-pages-artifact` to upload the built site for deployment.

### Deployment Job

```yaml
deploy:
  environment:
    name: github-pages
    url: ${{ steps.deployment.outputs.page_url }}
  runs-on: ubuntu-latest
  needs: build
  steps:
    - name: Deploy to GitHub Pages
      id: deployment
      uses: actions/deploy-pages@v4
```

- **environment**: Specifies the deployment environment, in this case, `github-pages`, and captures the URL of the deployed site.
- **runs-on**: Indicates that this job will also run on the latest version of Ubuntu.
- **needs**: Establishes a dependency on the `build` job, ensuring that the deployment only occurs after a successful build.
- **steps**: Contains the actions to perform during deployment:
  - **Deploy to GitHub Pages**: Uses `actions/deploy-pages` to handle the deployment of the built Jekyll site to GitHub Pages. The output from this step includes the URL of the deployed site, which can be referenced in the environment configuration.


# 📚 Examples

This directory contains example workflows for the Rust AUR Release Deploy GitHub Action.

## 🔍 Available Examples

### 1. Basic Usage (`basic-usage.yml`)

A simple workflow that demonstrates the basic usage of the action. This example shows how to:

- Trigger the workflow on release creation or manually
- Build and package a Rust application
- Create a GitHub release with the compiled binaries
- Deploy the package to the AUR

```yaml
name: Release and Deploy

on:
  release:
    types: [created]
  workflow_dispatch:
    inputs:
      version:
        description: 'Version to deploy (e.g. v1.0.0)'
        required: false
      rel:
        description: 'Release number (e.g. 1)'
        required: true
        default: '1'

jobs:
  release_and_deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Release and Deploy to AUR
        uses: Da4ndo/rust-aur-release-deploy@v1
        with:
          pkg_name: my-rust-app
          version: ${{ github.event.inputs.version }}
          rel: ${{ github.event.inputs.rel || '1' }}
          files: '["LICENSE", "README.md"]'
          aur: true
          pkgbuild: '.github/PKGBUILD'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          AUR_SSH_PRIVATE_KEY: ${{ secrets.AUR_SSH_PRIVATE_KEY }}
          AUR_USERNAME: ${{ secrets.AUR_USERNAME }}
          AUR_EMAIL: ${{ secrets.AUR_EMAIL }}
```

### 2. Complete Workflow (`complete-workflow.yml`)

A more comprehensive workflow that includes additional steps such as:

- Running tests before releasing
- Setting up Rust with multiple targets
- Notifying on success via Slack

This example is ideal for projects that require a more robust CI/CD pipeline.

### 3. Example PKGBUILD (`PKGBUILD`)

An example PKGBUILD file that can be used as a template for your own package. This file defines how your package should be built and installed on Arch Linux systems.

## 🚀 Getting Started

To use these examples:

1. Copy the desired example to your repository's `.github/workflows/` directory
2. Customize the workflow to match your project's needs
3. Set up the required secrets in your repository settings
4. Create a PKGBUILD file in your repository

For more detailed information, refer to the [main documentation](../README.md).

## 💡 Tips

- Always test your workflow with a manual trigger before relying on automatic releases
- Make sure your PKGBUILD file is properly formatted and validated
- Keep your AUR SSH key secure and never expose it in your code 
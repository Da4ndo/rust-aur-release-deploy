# 📚 Examples

This directory contains example workflows for the Rust AUR Release Deploy GitHub Action.

## 🔍 Available Examples

### 1. Basic Usage (`basic-usage.yml`)

A simple workflow that demonstrates the basic usage of the action. This example shows how to:

- Trigger the workflow on release creation or manually
- Build and package a Rust application for Linux
- Create a GitHub release with the compiled binaries
- Deploy the package to the AUR

```yaml
name: Basic Usage Example

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
        required: false
        default: '1'

jobs:
  release_and_deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Build and Prepare Release
        id: release
        uses: Da4ndo/rust-aur-release-deploy@v2
        with:
          package_name: your-package-name
          version: ${{ github.event.inputs.version }}
          rel: ${{ github.event.inputs.rel || '1' }}
          platform: linux
          pkgbuild: './PKGBUILD'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - name: Deploy to AUR
        uses: KSXGitHub/github-actions-deploy-aur@v4.1.1
        with:
          pkgname: your-package-name
          pkgbuild: ${{ steps.release.outputs.pkgbuild_path }}
          commit_username: ${{ secrets.AUR_USERNAME }}
          commit_email: ${{ secrets.AUR_EMAIL }}
          ssh_private_key: ${{ secrets.AUR_SSH_PRIVATE_KEY }}
          commit_message: "Update to version ${{ steps.release.outputs.version }}"
          ssh_keyscan_types: rsa,ecdsa,ed25519
```

### 2. Git Package (`git-workflow.yml`)

A workflow for maintaining -git packages that track your main branch. This example shows how to:

- Trigger the workflow on pushes to main
- Run tests before deployment
- Extract version information from Cargo.toml
- Deploy a -git package to AUR with proper versioning

```yaml
name: Git Package Release

on:
  push:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
        
      - name: Setup Rust
        uses: actions-rs/toolchain@v1
        with:
          profile: minimal
          toolchain: stable
          
      - name: Run tests
        uses: actions-rs/cargo@v1
        with:
          command: test
          
  release_and_deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Get version info
        id: version
        shell: bash
        run: |
          VERSION=$(grep -m 1 '^version = ' Cargo.toml | cut -d '"' -f 2)
          echo "version=$VERSION" >> $GITHUB_OUTPUT
          echo "commit_short=$(git rev-parse --short HEAD)" >> $GITHUB_OUTPUT
          echo "commit_count=$(git rev-list --count HEAD)" >> $GITHUB_OUTPUT

      - name: Build and Prepare Release
        id: release
        uses: Da4ndo/rust-aur-release-deploy@v2
        with:
          package_name: your-package-name-git
          version: ${{ steps.version.outputs.version }}.r${{ steps.version.outputs.commit_count }}.g${{ steps.version.outputs.commit_short }}
          rel: 1
          platform: linux
          linux_files: '["LICENSE", "README.md"]'
          pkgbuild: './PKGBUILD-git'
          is_git_package: true
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - name: Deploy to AUR
        uses: KSXGitHub/github-actions-deploy-aur@v4.1.1
        with:
          pkgname: your-package-name-git
          pkgbuild: ${{ steps.release.outputs.pkgbuild_path }}
          commit_username: ${{ secrets.AUR_USERNAME }}
          commit_email: ${{ secrets.AUR_EMAIL }}
          ssh_private_key: ${{ secrets.AUR_SSH_PRIVATE_KEY }}
          commit_message: "Update to ${{ steps.version.outputs.version }}.r${{ steps.version.outputs.commit_count }}.g${{ steps.version.outputs.commit_short }}"
          ssh_keyscan_types: rsa,ecdsa,ed25519
```

### 3. Complete Workflow (`complete-workflow.yml`)

A comprehensive workflow that demonstrates all features:

- Cross-platform builds (Linux and Windows)
- Additional file handling
- Release notes generation
- Documentation updates
- Proper error handling

See `complete-workflow.yml` for the full example.

### 4. PKGBUILD Templates

- `PKGBUILD`: Template for regular packages
- `PKGBUILD-git`: Template for -git packages

See the [PKGBUILD Guide](../docs/PKGBUILD-GUIDE.md) for detailed information about creating and customizing these files.

## 🚀 Getting Started

To use these examples:

1. Choose the appropriate example for your needs:
   - `basic-usage.yml` for simple releases
   - `git-workflow.yml` for -git packages
   - `complete-workflow.yml` for advanced usage

2. Copy the workflow to your repository's `.github/workflows/` directory

3. Create the appropriate PKGBUILD file:
   - Use `PKGBUILD` for regular packages
   - Use `PKGBUILD-git` for -git packages

4. Set up the required secrets in your repository settings:
   - `GITHUB_TOKEN`: Automatically provided by GitHub
   - `AUR_SSH_PRIVATE_KEY`: Your AUR SSH private key
   - `AUR_USERNAME`: Your AUR username
   - `AUR_EMAIL`: Your AUR email

## 💡 Tips

- Test your workflow with manual triggers before enabling automatic releases
- Validate your PKGBUILD files locally with `namcap`
- Keep your AUR SSH key secure
- Use proper version numbers in your Cargo.toml
- Consider running tests before deployment
- Use descriptive commit messages for AUR updates 
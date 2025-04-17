# 🚀 Rust AUR Release Deploy

<div align="center">

![GitHub stars](https://img.shields.io/github/stars/Da4ndo/rust-aur-release-deploy?style=for-the-badge&color=yellow)
![GitHub license](https://img.shields.io/github/license/Da4ndo/rust-aur-release-deploy?style=for-the-badge&color=blue)
![GitHub release](https://img.shields.io/github/v/release/Da4ndo/rust-aur-release-deploy?style=for-the-badge&color=green)

**A powerful GitHub Action for building Rust applications, creating GitHub releases, and preparing packages for the Arch User Repository (AUR) deployment.**

[Features](#-features) • 
[Usage](#-usage) • 
[Inputs](#-inputs) • 
[Documentation](#-documentation) • 
[License](#-license)

</div>

---

## ✨ Features

- 🦀 **Build and package Rust applications** with ease
- 🎯 **Cross-platform support** for Linux and Windows builds
- 📦 **Smart release handling** - automatically uses existing releases or creates new ones
- 🏗️ **Prepare packages for AUR deployment** automatically
- 🔄 **Modular workflow design** for flexible deployment options
- 🛠️ **Customizable build and packaging options** for each platform

## 🚀 Usage

### Basic Example

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

      - name: Build and Prepare Release
        id: release
        uses: Da4ndo/rust-aur-release-deploy@v1
        with:
          pkg_name: your-package-name
          version: ${{ github.event.inputs.version }}  # If not provided, uses latest tag
          rel: ${{ github.event.inputs.rel || '1' }}
          build_linux: true
          build_windows: true
          linux_files: '["LICENSE", "config.toml"]'
          windows_files: '["LICENSE", "config.win.toml"]'
          release_files: '["README.md"]'
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

> 💡 **Note:** For more complex examples, check out the [examples directory](./examples/).

### Release Handling

The action intelligently handles GitHub releases:

1. If a `version` is provided:
   - If a release with that version tag exists, it will upload assets to it
   - If no release exists, it will create a new one

2. If no `version` is provided:
   - Uses the latest git tag as the version
   - If no tags exist, the action will fail with a clear error message
   - Follows the same logic as above for existing/new releases

This means you can:
- Create new releases with new versions
- Add additional assets to existing releases
- Use the latest tag automatically
All without any additional configuration!

## 📋 Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `version` | Version to deploy (e.g., v1.0.0). If not provided, uses the latest release tag. | No | Latest release tag |
| `pkg_name` | Name of your package | Yes | N/A |
| `build_linux` | Whether to build for Linux | No | true |
| `build_windows` | Whether to build for Windows | No | false |
| `linux_files` | List of additional files to include in the Linux tar.gz archive (JSON array) | No | '[]' |
| `windows_files` | List of additional files to include in the Windows release (JSON array) | No | '[]' |
| `release_files` | List of additional files to include in the GitHub release but not in platform archives (JSON array) | No | '[]' |
| `rel` | Release number for AUR package | No | '1' |
| `prepare_aur` | Whether to prepare PKGBUILD for AUR deployment | No | true |
| `pkgbuild` | Path to the PKGBUILD file to use for AUR deployment | No | '' |
| `pkgbuild_output_path` | Path where the prepared PKGBUILD file should be saved | No | './prepared_pkgbuild/PKGBUILD' |
| `git_deploy` | Whether this is a -git deployment (skips release creation) | No | false |

## 📤 Outputs

| Name | Description |
|------|-------------|
| `version` | The version that was deployed |
| `linux_archive` | Path to the Linux tar.gz archive |
| `linux_sha256` | SHA256 sum of the Linux archive |
| `windows_archive` | Path to the Windows zip archive |
| `windows_sha256` | SHA256 sum of the Windows archive |
| `pkgbuild_path` | The path to the prepared PKGBUILD file |

## 🔐 Environment Variables

| Name | Description |
|------|-------------|
| `GITHUB_TOKEN` | GitHub token for creating releases |

## 📚 Documentation

- [**PKGBUILD Guide**](./docs/PKGBUILD-GUIDE.md) - Learn how to create and customize your PKGBUILD file
- [**Changelog**](./CHANGELOG.md) - View the version history and changes
- [**Contributing Guide**](./CONTRIBUTING.md) - Learn how to contribute to this project

## 🔧 Setting Up AUR Deployment

1. Create an SSH key pair for AUR authentication:
   ```bash
   ssh-keygen -t ed25519 -C "your-email@example.com" -f ~/.ssh/aur
   ```

2. Add the public key to your AUR account at https://aur.archlinux.org/account/

3. Add the following secrets to your GitHub repository:
   - `AUR_SSH_PRIVATE_KEY`: Your AUR SSH private key
   - `AUR_USERNAME`: Your AUR username
   - `AUR_EMAIL`: Your AUR email

4. Create a PKGBUILD file in your repository (see [PKGBUILD Guide](./docs/PKGBUILD-GUIDE.md))

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details.

---

<div align="center">

Made with ❤️ by [Da4ndo](https://github.com/Da4ndo)

⭐ Star this repository if you find it useful! ⭐

</div> 
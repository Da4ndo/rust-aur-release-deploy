# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Released]

## [1.0.0] - 2025-03-05

### Added
- Cross-platform build support for Linux and Windows
- Platform-specific file packaging with `linux_files` and `windows_files`
- Separate release files with `release_files` parameter
- Automatic tar.gz archive creation for Linux builds
- Automatic zip archive creation for Windows builds
- SHA256 sum calculation for all archives
- Intelligent release handling (automatically uses existing release or creates new one)
- More flexible output paths with `pkgbuild_output_path`
- Improved error handling for missing git tags
- Enhanced emoji-based logging for better visibility
- Initial release of the Rust AUR Release Deploy GitHub Action
- Support for building Rust applications for multiple platforms
- Support for creating GitHub releases with compiled binaries
- Support for deploying packages to the Arch User Repository (AUR)
- Support for using existing PKGBUILD files
- Documentation and examples

### Changed
- Split AUR deployment into separate step using KSXGitHub/github-actions-deploy-aur@v4.1.1
- Renamed `aur` parameter to `prepare_aur` to better reflect its purpose
- Changed file packaging to use platform-specific archives
- Improved error handling and logging with emojis
- Made the action more modular and flexible
- Simplified release handling logic (removed `create_release` parameter)
- Updated documentation and examples for new features
- Shortened parameter names for better usability:
  - `additional_files` → `files`
  - `deploy_to_aur` → `aur`
  - `pkgbuild_path` → `pkgbuild`
- Changed `aur` parameter to use boolean type instead of string
- Modified `files` parameter to only accept JSON arrays

### Removed
- Direct AUR deployment (now handled by KSXGitHub/github-actions-deploy-aur)
- aarch64 Linux build support (focusing on x86_64)
- Combined file list in favor of platform-specific lists
- `create_release` parameter (now handled automatically)

[Unreleased]: https://github.com/Da4ndo/rust-aur-release-deploy/compare/v1.0.0...HEAD
[1.0.0]: https://github.com/Da4ndo/rust-aur-release-deploy/releases/tag/v1.0.0
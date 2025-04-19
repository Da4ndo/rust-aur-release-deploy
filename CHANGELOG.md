# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.2.0] - 2025-04-19

### Changed
- Improved error handling for Linux binary builds

### Fixed
- Fixed conditional logic for platform-specific builds to properly evaluate boolean expressions
- Improved build failure handling to exit early when required binaries cannot be built
- Fixed release tag comparison logic to ensure proper version control

## [2.1.0] - 2025-04-18

### Added
- Platform parameter to control build targets (linux, windows, or both)
- Improved git package support with automatic version extraction
- Auto-release and release notes generation controls
- Test job integration in git workflow example
- Enhanced documentation for git packages
- Improved error handling and logging

### Changed
- Renamed parameters for better clarity:
  - `pkg_name` → `package_name`
  - `build_linux` and `build_windows` → `platform`
  - `git_deploy` → `is_git_package`
- Updated workflow examples to use release events
- Improved PKGBUILD handling for git packages
- Enhanced version extraction from Cargo.toml
- Made release number optional in workflow_dispatch
- Simplified platform selection with single parameter

### Fixed
- Git package version handling with proper metadata
- Conditional build steps based on package type
- Release asset handling for existing releases
- PKGBUILD source URL formatting
- Documentation consistency across examples

## [2.0.0] - 2025-04-17

### Added
- `git_deploy` input parameter to handle -git deployments
- Automatic release notes generation using GitHub's built-in generator
- Support for skipping release creation for -git deployments

### Changed
- Improved boolean handling in workflow conditions
- Updated workflow examples to include git_deploy configuration
- Enhanced documentation for git deployment scenarios

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

[Unreleased]: https://github.com/Da4ndo/rust-aur-release-deploy/compare/v2...HEAD
[2.2.0]: https://github.com/Da4ndo/rust-aur-release-deploy/releases/tag/v2
[1.0.0]: https://github.com/Da4ndo/rust-aur-release-deploy/releases/tag/v1
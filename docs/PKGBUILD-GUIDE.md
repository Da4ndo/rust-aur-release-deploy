# 📦 PKGBUILD Guide

This guide explains how to create and customize your PKGBUILD file for use with the Rust AUR Release Deploy action.

## 📝 Basic PKGBUILD Template

```bash
# Maintainer: Your Name <your.email@example.com>
pkgname=your-package-name
pkgver=0.1.0
pkgrel=1
pkgdesc="Description of your package"
arch=('x86_64')
url="https://github.com/yourusername/your-repo"
license=('MIT')
depends=()
makedepends=()
source=("https://github.com/yourusername/your-repo/releases/download/v$pkgver/$pkgname-v$pkgver-linux-x86_64.tar.gz")
sha256sums=('SKIP')  # Will be automatically updated by the action

package() {
    cd "$srcdir"
    
    # Create directories
    install -dm755 "$pkgdir/usr/bin"
    
    # Install binary
    install -Dm755 "$pkgname" "$pkgdir/usr/bin/$pkgname"
    
    # Install additional files (if included in the archive)
    if [ -f "LICENSE" ]; then
        install -Dm644 "LICENSE" "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
    fi
    if [ -f "README.md" ]; then
        install -Dm644 "README.md" "$pkgdir/usr/share/doc/$pkgname/README.md"
    fi
    if [ -f "config.toml" ]; then
        install -Dm644 "config.toml" "$pkgdir/etc/$pkgname/config.toml"
    fi
}
```

## 🔧 Customization

### Package Information

- `pkgname`: Must match the name specified in your workflow
- `pkgver`: Will be automatically updated by the action
- `pkgrel`: Will be automatically updated by the action
- `pkgdesc`: Add a clear description of your package
- `arch`: Keep as `('x86_64')` as the action builds for x86_64
- `url`: Update to your repository URL
- `license`: Update to match your project's license

### Dependencies

Add any runtime dependencies to the `depends` array:

```bash
depends=(
    'openssl'
    'gtk3'
    # Add other dependencies
)
```

Add build dependencies to `makedepends` if needed (though not typically necessary as the binary is pre-built):

```bash
makedepends=(
    'cargo'
    # Add other build dependencies
)
```

### Source and SHA256

The action will automatically update these fields:

```bash
source=("https://github.com/yourusername/your-repo/releases/download/v$pkgver/$pkgname-v$pkgver-linux-x86_64.tar.gz")
sha256sums=('SKIP')  # Will be updated with actual SHA256
```

### Package Function

The `package()` function should handle files included in your Linux archive. Update it based on the files specified in `linux_files` in your workflow:

```bash
package() {
    cd "$srcdir"
    
    # Binary installation (always included)
    install -dm755 "$pkgdir/usr/bin"
    install -Dm755 "$pkgname" "$pkgdir/usr/bin/$pkgname"
    
    # Documentation
    if [ -f "README.md" ]; then
        install -Dm644 "README.md" "$pkgdir/usr/share/doc/$pkgname/README.md"
    fi
    if [ -f "CHANGELOG.md" ]; then
        install -Dm644 "CHANGELOG.md" "$pkgdir/usr/share/doc/$pkgname/CHANGELOG.md"
    fi
    
    # License
    if [ -f "LICENSE" ]; then
        install -Dm644 "LICENSE" "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
    fi
    
    # Configuration
    if [ -f "config/linux.toml" ]; then
        install -Dm644 "config/linux.toml" "$pkgdir/etc/$pkgname/config.toml"
    fi
    
    # Assets
    if [ -d "assets" ]; then
        cp -r assets "$pkgdir/usr/share/$pkgname/"
    fi
}
```

## 🚀 Usage with the Action

1. Save your PKGBUILD template in your repository (e.g., `./PKGBUILD`)

2. In your workflow, specify the path to your PKGBUILD:

```yaml
- name: Build and Prepare Release
  id: release
  uses: Da4ndo/rust-aur-release-deploy@v1
  with:
    pkg_name: your-package-name
    pkgbuild: './PKGBUILD'
    linux_files: |
      [
        "LICENSE",
        "README.md",
        "config/linux.toml",
        "assets/*"
      ]

- name: Deploy to AUR
  uses: KSXGitHub/github-actions-deploy-aur@v4.1.1
  with:
    pkgname: your-package-name
    pkgbuild: ${{ steps.release.outputs.pkgbuild_path }}
    commit_username: ${{ secrets.AUR_USERNAME }}
    commit_email: ${{ secrets.AUR_EMAIL }}
    ssh_private_key: ${{ secrets.AUR_SSH_PRIVATE_KEY }}
```

## 📋 Best Practices

1. **File Organization**
   - Keep your PKGBUILD template in version control
   - Use consistent paths in your repository
   - Document any special file requirements

2. **Dependencies**
   - List all runtime dependencies
   - Test the package installation in a clean environment
   - Consider using `optdepends` for optional features

3. **Installation**
   - Follow the [Arch Packaging Guidelines](https://wiki.archlinux.org/title/Arch_package_guidelines)
   - Use appropriate file permissions
   - Install files to standard locations

4. **Testing**
   - Test your PKGBUILD locally before deployment
   - Verify file paths and permissions
   - Check that the package works after installation

## 🔍 Troubleshooting

1. **Missing Files**
   - Ensure all files listed in `linux_files` exist in your repository
   - Check file paths are relative to the repository root
   - Verify file permissions are correct

2. **Installation Issues**
   - Check the package function handles all files correctly
   - Verify file paths in the installed package
   - Test the package in a clean environment

3. **Dependency Issues**
   - Verify all dependencies are correctly listed
   - Test with minimal dependencies first
   - Consider using `namcap` to check the package 
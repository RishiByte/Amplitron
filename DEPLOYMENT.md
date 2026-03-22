# Amplitron - Deployment Guide

This document explains the CI/CD pipeline and how to create releases.

## GitHub Actions Workflows

### 1. CI Workflow (`.github/workflows/ci.yml`)

**Triggers**: Push to `main` or `develop`, Pull Requests

**What it does**:
- Builds Amplitron on Windows, macOS, and Linux
- Runs the full test suite (64+ tests)
- Uploads test artifacts for debugging

**Platforms**:
- **Windows**: MSYS2/MinGW64
- **macOS**: Homebrew dependencies
- **Linux**: Ubuntu 22.04 with apt packages

### 2. Release Workflow (`.github/workflows/release.yml`)

**Triggers**: 
- Git tags matching `v*.*.*` (e.g., `v1.0.0`)
- Manual workflow dispatch

**What it does**:
1. Creates a GitHub Release
2. Builds platform-specific packages:
   - **Windows**: `Amplitron-Windows-x64.zip` (includes all DLLs)
   - **macOS**: `Amplitron-macOS-Universal.tar.gz` (app bundle)
   - **Linux**: `Amplitron-Linux-x64.tar.gz` (with launcher script)
3. Uploads binaries to the release
4. Deploys the download page to GitHub Pages

## Creating a Release

### Step 1: Prepare the Release

1. Ensure all tests pass locally:
   ```bash
   cd build
   ./amplitron-tests
   ```

2. Update version numbers if needed (in CMakeLists.txt, docs, etc.)

3. Commit all changes:
   ```bash
   git add -A
   git commit -m "Prepare release v1.0.0"
   git push origin main
   ```

### Step 2: Create and Push a Tag

```bash
# Create an annotated tag
git tag -a v1.0.0 -m "Release v1.0.0 - Initial public release"

# Push the tag to GitHub
git push origin v1.0.0
```

### Step 3: Wait for Automation

GitHub Actions will automatically:
- ✅ Build for all platforms
- ✅ Run tests
- ✅ Create the release
- ✅ Upload binaries
- ✅ Deploy website to https://sudip-mondal-2002.github.io/Amplitron/

Monitor progress at: https://github.com/sudip-mondal-2002/Amplitron/actions

### Step 4: Verify the Release

1. Check the release page: https://github.com/sudip-mondal-2002/Amplitron/releases
2. Download and test each platform binary
3. Verify the website is live

## Manual Release (Workflow Dispatch)

You can also trigger a release manually:

1. Go to: https://github.com/sudip-mondal-2002/Amplitron/actions/workflows/release.yml
2. Click "Run workflow"
3. Enter the version (e.g., `v1.0.0`)
4. Click "Run workflow"

## GitHub Pages Setup

### Enable GitHub Pages

1. Go to repository Settings → Pages
2. Source: Deploy from a branch
3. Branch: `gh-pages` / `root`
4. Click Save

The website will be available at: https://sudip-mondal-2002.github.io/Amplitron/

### Update the Download Page

Edit `docs/index.html` to update:
- Version numbers
- Feature descriptions
- Download links
- Screenshots

Commit and push changes - GitHub Pages will auto-deploy.

## Platform-Specific Packaging

### Windows (`scripts/package_windows.ps1`)

Creates a ZIP with:
- `Amplitron.exe`
- All MinGW runtime DLLs
- PortAudio and SDL2 DLLs
- Assets and presets
- README.txt

### macOS (`scripts/package_macos.sh`)

Creates an app bundle with:
- `Amplitron.app/Contents/MacOS/Amplitron`
- `Info.plist` with proper metadata
- Resources (assets, presets)
- Tarball for distribution

### Linux (`scripts/package_linux.sh`)

Creates a tarball with:
- `amplitron` binary
- `amplitron.sh` launcher (checks dependencies)
- Assets and presets
- README.txt with installation instructions

## Troubleshooting

### CI Build Fails

1. Check the Actions logs
2. Common issues:
   - Missing dependencies
   - Test failures
   - CMake configuration errors

### Release Upload Fails

- Ensure `GITHUB_TOKEN` has proper permissions
- Check if the tag already exists
- Verify file paths in the workflow

### GitHub Pages Not Updating

1. Check Actions → pages-build-deployment
2. Ensure `gh-pages` branch exists
3. Verify Settings → Pages is configured correctly

## Version Numbering

Use semantic versioning: `vMAJOR.MINOR.PATCH`

- **MAJOR**: Breaking changes
- **MINOR**: New features (backward compatible)
- **PATCH**: Bug fixes

Examples:
- `v1.0.0` - Initial release
- `v1.1.0` - Added new effect
- `v1.1.1` - Fixed bug in reverb

## Contact

For CI/CD issues or questions:
- Email: sudmondal2002@gmail.com
- GitHub Issues: https://github.com/sudip-mondal-2002/Amplitron/issues

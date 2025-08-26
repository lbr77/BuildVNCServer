# GitHub Workflows

This directory contains GitHub Actions workflows for automatically building and testing the BuildVNCServer iOS library.

## Workflows

### 1. Build Workflow (`build.yml`)

**Trigger**: On push to main/master/develop branches, pull requests, and manual dispatch

**Purpose**: Builds the VNC Server library for iOS with the latest SDK and Theos

**Features**:
- Uses macOS 14 runner with Xcode 15.1
- Installs and configures Theos for iOS development
- Downloads iOS SDK 17.2 automatically
- Updates build script to use iOS 14.0 deployment target (up from 13.0)
- Caches dependencies for faster builds
- Verifies build outputs and library architectures
- Uploads build artifacts for download
- Includes error handling and build log upload on failure
- Uses latest GitHub Actions (v4) for improved performance

**Artifacts**:
- `libvncserver-ios-arm64` - Built libraries and headers
- `build-logs` - Build logs (on failure only)

### 2. Release Workflow (`release.yml`)

**Trigger**: On version tag push (v*) or manual dispatch

**Purpose**: Creates automated releases with built libraries

**Features**:
- Builds the library with latest configuration
- Creates versioned release packages (tar.gz and zip)
- Generates SHA256 checksums for verification
- Creates detailed release notes
- Uploads release assets to GitHub Releases

**Release Assets**:
- `libvncserver-ios-{version}.tar.gz` - Compressed archive (recommended)
- `libvncserver-ios-{version}.zip` - ZIP archive
- `*.sha256` - Checksum files for verification

### 3. SDK Version Testing (`test-sdk-versions.yml`)

**Trigger**: Manual dispatch or weekly schedule (Sundays at 2 AM UTC)

**Purpose**: Tests compatibility across different iOS SDK versions and deployment targets

**Features**:
- Matrix builds testing multiple SDK/deployment target combinations
- Tests iOS SDKs: 16.4, 17.0, 17.2
- Tests deployment targets: 13.0, 14.0, 15.0
- Uploads test artifacts for each combination
- Provides test results summary

## Configuration

### Environment Variables

All workflows use these common environment variables:

```yaml
env:
  IOS_DEPLOYMENT_TARGET: "14.0"  # Minimum iOS version
  XCODE_VERSION: "15.1"          # Xcode version to use
  IOS_SDK_VERSION: "17.2"        # iOS SDK version
```

### Theos Setup

Theos is automatically installed and configured in all workflows:

- Installation path: `/opt/theos`
- iOS SDKs downloaded from: `https://github.com/theos/sdks/releases`
- Environment variables set for Theos tools

### Dependencies

All workflows install these build dependencies via Homebrew:
- cmake
- ninja
- autoconf
- automake
- libtool
- pkg-config

## Usage

### Triggering Builds

1. **Automatic builds**: Push to main/master/develop or create pull requests
2. **Manual builds**: Use "Run workflow" button in GitHub Actions tab
3. **Releases**: Push a version tag (e.g., `git tag v1.0.0 && git push origin v1.0.0`)

### Creating a Release

1. Ensure your code is ready for release
2. Create and push a version tag:
   ```bash
   git tag v1.0.0
   git push origin v1.0.0
   ```
3. The release workflow will automatically:
   - Build the library
   - Create release packages
   - Upload to GitHub Releases

### Manual Release

You can also create releases manually:

1. Go to Actions → Release workflow
2. Click "Run workflow"
3. Enter the desired version tag (e.g., v1.0.0)
4. Click "Run workflow"

## Artifacts and Downloads

### Build Artifacts

After each build, you can download:
- Built static libraries (.a files)
- Header files
- Build logs (if build fails)

### Release Assets

Releases include:
- Complete library package with all dependencies
- Both compressed (tar.gz) and ZIP formats
- SHA256 checksums for verification
- Detailed version information

## Verification

To verify downloaded releases:

```bash
# Download release and checksum
curl -L -O https://github.com/your-repo/releases/download/v1.0.0/libvncserver-ios-v1.0.0.tar.gz
curl -L -O https://github.com/your-repo/releases/download/v1.0.0/libvncserver-ios-v1.0.0.tar.gz.sha256

# Verify checksum
shasum -a 256 -c libvncserver-ios-v1.0.0.tar.gz.sha256
```

## Troubleshooting

### Common Issues

1. **Xcode version mismatch**: Update `XCODE_VERSION` in workflow files
2. **SDK not found**: Check if the iOS SDK version is available in the Theos SDKs repository
3. **Build failures**: Check the build logs artifact for detailed error information

### Debugging

1. Enable workflow debugging by setting repository secrets:
   - `ACTIONS_STEP_DEBUG: true`
   - `ACTIONS_RUNNER_DEBUG: true`

2. Use manual workflow dispatch to test specific configurations

3. Check the test SDK versions workflow for compatibility issues

## Customization

### Updating SDK Versions

1. Modify the `IOS_SDK_VERSION` environment variable
2. Ensure the SDK is available in the Theos SDKs repository
3. Test with the SDK version testing workflow

### Changing Deployment Targets

1. Update `IOS_DEPLOYMENT_TARGET` in workflow files
2. Update the build script modification commands
3. Test compatibility with your target iOS versions

### Adding New Build Configurations

1. Create new matrix entries in the test workflow
2. Add new environment variable combinations
3. Test thoroughly before deploying to main workflows
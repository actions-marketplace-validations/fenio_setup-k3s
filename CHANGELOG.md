# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.0.0] - 2025-12-09

### Changed
- **BREAKING:** Simplified implementation from Node.js/TypeScript to pure shell script
- **BREAKING:** Removed automatic cleanup/post-run functionality - action now only sets up k3s
- Converted to composite action format (no build step required)
- Removed all Node.js dependencies and build process

### Removed
- **BREAKING:** Post-run cleanup phase removed
- Node.js/TypeScript implementation removed
- Build process and compiled artifacts removed
- Cleanup of conflicting Kubernetes installations removed

### Added
- Simple `setup.sh` shell script for k3s installation
- Easier maintenance with no build step required

## [1.0.0] - 2025-11-09

### Added
- Initial release
- Automatic k3s installation and configuration
- Cleanup of conflicting Kubernetes installations
- Automatic post-run cleanup to restore system state
- Configurable k3s version selection
- Custom k3s arguments support
- Cluster readiness waiting with timeout
- Kubeconfig output for easy integration

# AGENTS.md

This file provides comprehensive documentation about the setup-k3s GitHub Action for AI agents and developers working with this codebase.

## Project Overview

**setup-k3s** is a GitHub Action that installs and configures k3s - Lightweight certified Kubernetes distribution for CI/CD. The action is implemented as a simple composite action using a shell script.

### Key Features
- Automatic installation of k3s with version/channel selection
- Support for custom k3s arguments
- Cluster readiness checks with configurable timeout
- Outputs kubeconfig path for easy integration with kubectl
- Simple shell script implementation (no build process required)

## Architecture

The action uses GitHub Actions' composite action format with a single shell script.

### Execution Flow

```
setup.sh: installK3s() → waitForClusterReady()
```

**Installation Phase**
- Resolves version/channel (latest, stable, or specific version)
- Uses official k3s install script from https://get.k3s.io
- Passes custom arguments to k3s installer
- Waits for k3s service to start
- Verifies service is active
- Location: `setup.sh:14-47`

**Readiness Check Phase**
- Polls for cluster readiness with configurable timeout
- Checks: k3s service active → kubeconfig exists → kubectl connects → node Ready → system pods running
- Sets KUBECONFIG output and environment variable
- Shows cluster info when ready
- Location: `setup.sh:53-107`

## File Structure

```
setup-k3s/
├── setup.sh             # Main shell script for k3s setup
├── action.yml           # GitHub Action metadata and interface
├── .github/workflows/   # CI/CD workflows
│   └── ci.yml           # CI workflow
├── README.md            # User documentation
├── AGENTS.md            # This file
├── LICENSE              # MIT License
└── .gitignore           # Git ignore rules
```

## Key Technical Details

### Action Configuration (action.yml)

**Inputs:**
- `version` (default: 'stable'): k3s version to install (e.g., v1.28.5+k3s1, latest, stable, or channel like stable)
- `k3s-args` (default: '--write-kubeconfig-mode 644'): Additional arguments to pass to k3s installer
- `wait-for-ready` (default: 'true'): Wait for k3s cluster to be ready
- `timeout` (default: '120'): Timeout in seconds for readiness check

**Outputs:**
- `kubeconfig`: Path to kubeconfig file (`/etc/rancher/k3s/k3s.yaml`)

**Runtime:**
- Composite action using bash shell
- Main script: `setup.sh`

### Dependencies

**None!** This action is a pure shell script with no external dependencies beyond what's available in a standard GitHub Actions runner:
- bash
- curl
- systemctl
- kubectl (installed by k3s)

## System Requirements

- **OS:** Linux (tested on ubuntu-latest)
- **Permissions:** sudo access (available by default in GitHub Actions)
- **Network:** Internet access to download k3s install script and binaries

## Common Modification Scenarios

### Adding New Configuration Options

1. Add input to `action.yml`:
```yaml
inputs:
  new-option:
    description: 'Description of the new option'
    required: false
    default: 'default-value'
```

2. Pass input to shell script in `action.yml`:
```yaml
env:
  INPUT_NEW_OPTION: ${{ inputs.new-option }}
```

3. Read input in `setup.sh`:
```bash
NEW_OPTION="${INPUT_NEW_OPTION:-default-value}"
```

4. Update README.md documentation

### Modifying Installation Logic

The installation logic is in `setup.sh:14-47`. Key areas:
- Version/channel resolution: lines 20-27
- Install command construction: lines 19-29
- Service verification: lines 37-43

### Adjusting Readiness Checks

Readiness check logic is in `setup.sh:53-107`. Key checks:
- Service active check: line 72
- Kubeconfig existence: line 74
- kubectl connectivity: line 76
- Node Ready status: line 78
- System pods running: line 82

## Testing Strategy

### Testing Checklist
**Setup Phase:**
- [ ] k3s installs successfully
- [ ] Cluster becomes ready within timeout
- [ ] kubectl can connect and list nodes
- [ ] KUBECONFIG output is set correctly
- [ ] KUBECONFIG environment variable is exported

**Version Selection:**
- [ ] stable channel works
- [ ] latest channel works
- [ ] Specific version (e.g., v1.28.5+k3s1) works

**Custom Arguments:**
- [ ] Custom k3s-args are passed correctly
- [ ] Components can be disabled (e.g., --disable=traefik)

## Debugging

### Enable Debug Logging
Set repository secret: `ACTIONS_STEP_DEBUG = true`

### Key Log Messages
- "Starting k3s setup..." - Setup begins
- "Installing k3s..." - Installation starts
- "k3s installed successfully" - Installation complete
- "Node is Ready" - Node is ready
- "All system pods are running" - All pods running
- "k3s cluster is fully ready!" - Cluster fully ready
- "k3s setup completed successfully!" - Setup complete

### Diagnostic Information
When cluster readiness times out, diagnostics are displayed:
- k3s service status
- k3s journal logs (last 100 lines)
- Kubeconfig directory contents

## Related Resources

- **k3s Project**: https://k3s.io/
- **k3s GitHub**: https://github.com/k3s-io/k3s
- **k3s Documentation**: https://docs.k3s.io/
- **GitHub Actions Documentation**: https://docs.github.com/actions
- **Composite Actions Guide**: https://docs.github.com/actions/creating-actions/creating-a-composite-action

## Contributing

### Development Workflow
1. Make changes to `setup.sh` or `action.yml`
2. Test locally if possible
3. Test in a workflow on GitHub
4. Update README.md if adding/changing features
5. Create pull request

### Release Process
Releases are typically managed via tags. Tags should follow semantic versioning (e.g., v1.0.0).

## Simplification Notes

This action was simplified from a Node.js/TypeScript implementation to a pure shell script composite action. Benefits include:
- No build step required
- No dependencies to manage
- Simpler codebase to maintain
- No need to commit compiled code to the repository
- Easier to understand and modify

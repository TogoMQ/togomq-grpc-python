# GitHub Actions Workflows

This directory contains GitHub Actions workflows for the TogoMQ Python gRPC package.

## Workflows

### 1. CI (`ci.yml`)
Runs on every push and pull request to `main`:
- Tests imports on Python 3.9, 3.10, 3.11, and 3.12
- Compiles all Python files
- Builds the package
- Lints code with flake8

### 2. Auto Release (`release.yml`)
Automatically creates version tags and GitHub releases:
- **On main push**: Auto-detects version bump from commit messages
- **Manual trigger**: Choose patch/minor/major version bump
- Updates version in `setup.py`, `pyproject.toml`, and `__init__.py`
- Creates Git tag (e.g., `v0.1.0`)
- Creates GitHub Release with changelog

**Commit Message Conventions:**
- `major:` or `breaking:` → Major version bump (1.0.0 → 2.0.0)
- `feat:` or `feature:` or `minor:` → Minor version bump (0.1.0 → 0.2.0)
- Any other commit → Patch version bump (0.1.0 → 0.1.1)

### 3. Publish (`publish.yml`)
Handles PyPI publishing:
- **On tag push (v*)**: Publishes to PyPI (production)
- Uses GitHub's Trusted Publishers (OIDC) - no API tokens needed!

## Setting Up PyPI Publishing

### Prerequisites
1. Package must be registered on PyPI: https://pypi.org/project/togomq-grpc/

### Configure Trusted Publisher

1. Go to https://pypi.org/manage/project/togomq-grpc/settings/publishing/
2. Add a new publisher:
   - **PyPI Project Name**: `togomq-grpc`
   - **Owner**: `TogoMQ`
   - **Repository name**: `togomq-grpc-python`
   - **Workflow name**: `publish.yml`
   - **Environment name**: `pypi`

### GitHub Repository Settings

#### Create Environment
1. Go to repository Settings → Environments
2. Create environment:
   - **pypi**: For production releases (add protection rules if desired)

## Publishing a Release

### Automatic Release (Recommended)

The release workflow automatically handles versioning and tagging.

#### Option 1: Auto-detect from commit message
Push to main with a semantic commit message:
```bash
git commit -m "feat: add new message type support"
git push origin main
```

This will:
1. Detect the version bump type from commit message
2. Update version numbers in all files
3. Create a Git tag (e.g., `v0.2.0`)
4. Create a GitHub Release
5. Trigger PyPI publication

#### Option 2: Manual trigger
1. Go to Actions → Auto Release
2. Click "Run workflow"
3. Select version bump type: `patch`, `minor`, or `major`
4. Click "Run workflow"

### Production Release (PyPI)
Once a tag is created (automatically by the release workflow or manually), it will:
1. Build the package
2. Publish to PyPI
3. Make it available via `pip install togomq-grpc`

Install the package:
```bash
pip install togomq-grpc
```

#### Manual tag creation (not recommended)
If needed, you can still manually create tags:
```bash
# Update version in setup.py and pyproject.toml first
git tag v0.1.0
git push origin v0.1.0
```

## Version Management

The release workflow automatically updates version numbers in:
- `setup.py`: `version="X.Y.Z"`
- `pyproject.toml`: `version = "X.Y.Z"`
- `__init__.py`: `__version__ = "X.Y.Z"`

Follow semantic versioning: https://semver.org/

### Semantic Versioning Guide
- **Major (X.0.0)**: Breaking changes (incompatible API changes)
- **Minor (0.X.0)**: New features (backward-compatible)
- **Patch (0.0.X)**: Bug fixes (backward-compatible)

## Troubleshooting

### "Trusted Publisher" errors
- Verify the publisher is configured correctly in PyPI/TestPyPI
- Check that environment names match exactly
- Ensure the workflow file path is correct

### Build failures
- Check Python syntax: `python -m py_compile mq/v1/*.py`
- Validate package: `python -m build && twine check dist/*`

### Import errors in CI
- Verify all `__init__.py` files exist
- Check that protobuf files are generated correctly

## Resources

- [PyPI Trusted Publishers Documentation](https://docs.pypi.org/trusted-publishers/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Python Packaging Guide](https://packaging.python.org/)

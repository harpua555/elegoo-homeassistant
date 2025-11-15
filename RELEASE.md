# Release Guide for Standalone Spool Manager

## Creating a New Release

When you're ready to release a new version of the standalone spool manager:

### 1. Make sure you're on the standalone branch with all changes committed

```bash
git checkout standalone
git status  # Should show clean working tree
```

### 2. Create and push a tag

```bash
# Create a tag (e.g., spool-v1.0.0, spool-v1.1.0, etc.)
git tag -a spool-v1.0.0 -m "Release version 1.0.0 of standalone spool manager"

# Push the tag to GitHub
git push origin spool-v1.0.0
```

### 3. GitHub Actions will automatically:

- Create a release ZIP file containing:
  - All package files
  - All dashboard files
  - Documentation (README, guides)
  - Installation script

- Create a GitHub Release with:
  - Release notes
  - Download link for the ZIP
  - Installation instructions

### 4. The release will be available at:

`https://github.com/danielcherubini/elegoo-homeassistant/releases`

## Tag Naming Convention

Use the format `spool-v<MAJOR>.<MINOR>.<PATCH>`:

- **MAJOR**: Breaking changes or major new features (e.g., spool-v2.0.0)
- **MINOR**: New features, backwards compatible (e.g., spool-v1.1.0)
- **PATCH**: Bug fixes, small improvements (e.g., spool-v1.0.1)

Examples:
- `spool-v1.0.0` - Initial release
- `spool-v1.0.1` - Bug fix
- `spool-v1.1.0` - Added new material densities
- `spool-v2.0.0` - Breaking changes to entity names

## Manual Release (Without GitHub Actions)

If you prefer to create releases manually:

```bash
# Create release directory
mkdir -p release/config/{packages,dashboards}

# Copy files
cp config/packages/*.yaml release/config/packages/
cp config/dashboards/*.yaml release/config/dashboards/
cp config/lovelace-spool-manager.yaml release/config/
cp README.md SPOOL_MANAGER.md SPOOL_HISTORY_GUIDE.md LICENSE release/

# Create ZIP
cd release && zip -r ../elegoo-spool-manager-standalone.zip . && cd ..

# Upload to GitHub Releases manually
```

## Updating the Main Integration

To merge spool manager changes back to the main dev branch:

```bash
# Switch to dev
git checkout dev

# Merge specific files from standalone
git checkout standalone -- config/packages/elegoo_spool_manager.yaml
git checkout standalone -- config/packages/elegoo_spool_history.yaml
git checkout standalone -- config/dashboards/
git checkout standalone -- config/lovelace-spool-manager.yaml
git checkout standalone -- SPOOL_MANAGER.md
git checkout standalone -- SPOOL_HISTORY_GUIDE.md

# Commit the updates
git commit -m "Update spool manager from standalone branch"
```

## Troubleshooting

### GitHub Actions not triggering

- Verify the tag format matches `spool-v*`
- Check that you pushed the tag: `git push origin <tag-name>`
- Look at the Actions tab on GitHub for error messages

### Release ZIP missing files

- Check `.github/workflows/release-standalone.yml`
- Verify file paths are correct
- Run the workflow manually from the Actions tab

### Users reporting installation issues

1. Test the installation script yourself
2. Update README.md with clearer instructions
3. Create an issue template for installation problems

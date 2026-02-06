# CI/CD Workflow Configuration Guide

## Overview

The `publish-skill.yml` GitHub Actions workflow automatically validates, packages, and publishes the python-backend-creator skill to official hubs whenever changes are pushed to the `main` branch.

## Workflow Stages

### 1. Validate & Package

- **Trigger**: Push to `main` branch or manual workflow dispatch
- **Steps**:
  - Checkout repository
  - Set up Python 3.11
  - Run skill validation (`quick_validate.py`)
  - Package the skill (`package_skill.py`)
  - Create release notes
  - Upload to GitHub Releases

### 2. Publish to Hubs

- **Trigger**: Only runs if validation and packaging succeed
- **Steps**:
  - Prepare skill metadata
  - Publish to Claude Skills Hub (with token)
  - Create distribution archive
  - Generate installation guides
  - Upload distribution artifacts to GitHub Actions

### 3. Notification

- **Trigger**: Runs after hub publication (always)
- **Steps**:
  - Generate publication status report
  - Comment on the commit with status

## Setup Instructions

### Step 1: Enable GitHub Actions

1. Go to your repository settings
2. Navigate to **Actions** → **General**
3. Ensure **Actions permissions** is set to "Allow all actions and reusable workflows"
4. Save settings

### Step 2: Configure GitHub Secrets

For full functionality, configure these secrets in your repository:

#### Required Secrets

1. **GITHUB_TOKEN** (Automatic)
   - Already available in GitHub Actions
   - Used for creating GitHub Releases

2. **CLAUDE_SKILLS_HUB_TOKEN** (Optional, for direct API publishing)
   - Visit: https://skills.sh/ (when available)
   - Generate an API token
   - Add to GitHub Secrets:
     ```
     Settings → Secrets and variables → Actions → New repository secret
     Name: CLAUDE_SKILLS_HUB_TOKEN
     Value: <your-api-token>
     ```

#### Optional Hub Tokens

For future integrations with other skill hubs:
   - `CURSOR_API_TOKEN`: For Cursor IDE skill registry
   - `HUGGINGFACE_API_TOKEN`: For HuggingFace skill distribution
   - `OPENAI_API_TOKEN`: For OpenAI GPT marketplace integration

### Step 3: Customize Workflow

Edit `.github/workflows/publish-skill.yml` to configure:

```yaml
on:
  push:
    branches:
      - main                              # Trigger on main branch only
    paths:
      - '.github/skills/python-backend-creator/**'  # Monitor skill files
      - '.github/workflows/publish-skill.yml'        # Monitor workflow itself
```

## Workflow Outputs

### GitHub Releases

After each successful workflow run:
- Skill package (.skill file)
- Checksum file (.sha256)
- Release notes with changelog
- Installation instructions

Visit: `https://github.com/jajupmochi/python-backend-creator-skill/releases`

### GitHub Actions Artifacts

Available in the workflow run details:
- `skill-distribution/`: Distribution-ready archive
- `publication-notification`: Status report

Retention: 90 days

## Hub Integration Status

### ✅ Implemented

- **GitHub Releases**: Automatic upload on success
- **GitHub Actions Artifacts**: Automatic upload for distribution
- **Release Notes Generation**: Automatic from SKILL.md metadata

### 🔄 In Progress

- **Claude Skills Hub (skills.sh)**
  - Status: API prepared, awaiting token configuration
  - Location: `.github/workflows/publish-skill.yml` lines 89-115
  - Implementation: REST API with authentication
  - Required: `CLAUDE_SKILLS_HUB_TOKEN` secret

### 📋 Manual (Not yet automated)

1. **GitHub Marketplace**
   - Visit: https://github.com/marketplace/new
   - Upload skill package from releases
   - Select category: "Development tools"
   - Frequency: Quarterly or as needed

2. **Cursor Extensions Community**
   - Visit: https://cursor.ai/extensions (or community portal)
   - Upload `skill-distribution/python-backend-creator-skill.zip`
   - Fill in metadata from `skill_metadata.json`
   - Frequency: Quarterly or as needed

3. **OpenAI GPT Marketplace**
   - Visit: https://openai.com/gpts/editor
   - Create custom GPT with skill instructions
   - Link to skill repository
   - Frequency: Quarterly or as needed

## Testing the Workflow Locally

### Option 1: Dry Run

```bash
# Validate skill
python3 .github/skills/skill-creator/scripts/quick_validate.py \
  .github/skills/python-backend-creator

# Package skill
python3 .github/skills/skill-creator/scripts/package_skill.py \
  .github/skills/python-backend-creator \
  dist

# Check output
ls -lh dist/
```

### Option 2: Test on Branch

1. Push changes to a test branch (not `main`)
2. Create a PR to `main` (but don't merge yet)
3. Go to **Actions** tab and manually trigger workflow:
   - Click "Publish Skill to Hub"
   - Click "Run workflow" → "Run workflow"
4. Check logs for any errors
5. Review generated artifacts

### Option 3: Manual Trigger

```bash
# After pushing to main, trigger workflow manually
gh workflow run publish-skill.yml
```

## Troubleshooting

### Workflow fails on validation

**Error**: "Skill validation failed"

**Solution**: 
1. Check `/\.github/skills/python-backend-creator/SKILL.md` for syntax errors
2. Run local validation: `python3 .github/skills/skill-creator/scripts/quick_validate.py`
3. Fix errors and push again

### Workflow fails on packaging

**Error**: "Package skill failed"

**Solution**:
1. Verify all reference files exist in `references/` folder
2. Check permissions: `ls -la .github/skills/python-backend-creator/`
3. Run local packaging to debug

### Publication to Claude Skills Hub fails

**Error**: "Hub authentication failed" or "Token invalid"

**Solution**:
1. Verify `CLAUDE_SKILLS_HUB_TOKEN` is properly configured in GitHub Secrets
2. Check token expiration in https://skills.sh/ dashboard
3. Regenerate token if necessary
4. Update the secret value

### GitHub Release creation fails

**Error**: "Release already exists"

**Solution**:
1. The workflow uses `${{ github.run_number }}` as tag
2. Each workflow run gets a unique number
3. If manually retriggering, ensure previous release was deleted

## Advanced Customization

### Adding New Hub

To add publishing to a new skill hub:

1. **Update workflow**:
   ```yaml
   - name: Publish to [Hub Name]
     if: secrets.[HUB_TOKEN] != ''
     run: |
       SKILL_FILE=$(ls dist/*.skill | head -1)
       # Add hub-specific publishing logic here
       echo "Published to [Hub Name]"
   ```

2. **Add GitHub Secret**:
   - Go to Settings → Secrets and variables → Actions
   - Create new secret: `[HUB_NAME]_TOKEN`

3. **Update HUB_PUBLICATION_STATUS.md** section in the workflow

### Conditional Publishing

Publish only on version tags:

```yaml
on:
  push:
    tags:
      - 'v*'    # Only on version tags
```

### Custom Release Notes

Modify the release notes generation section:

```yaml
- name: Create release notes
  run: |
    # Custom release notes logic here
```

## Monitoring

### Check Workflow Status

1. Go to **Actions** tab in your GitHub repository
2. Click "Publish Skill to Hub"
3. View recent runs and their status

### View Artifacts

```bash
# List published releases
gh release list --repo jajupmochi/python-backend-creator-skill

# Download specific release
gh release download v1.0.0 --repo jajupmochi/python-backend-creator-skill
```

### Check Commit Status

GitHub automatically shows workflow status on commits and PRs:
- ✅ All workflows passed
- ❌ Workflow failed
- 🔄 Workflow in progress

## Performance Metrics

| Stage | Typical Duration |
|-------|-----------------|
| Checkout + Setup | ~15-20 seconds |
| Validation | ~5-10 seconds |
| Packaging | ~5-10 seconds |
| Release Creation | ~10-15 seconds |
| Hub Publishing | ~5-30 seconds |
| **Total** | **~40-85 seconds** |

## Support & Issues

- **Workflow Issues**: Check GitHub Actions logs
- **Skill Issues**: See [troubleshooting.md](../skills/python-backend-creator/references/troubleshooting.md)
- **API Issues**: See hub-specific documentation

## Future Enhancements

- [ ] Slack/Discord notifications on publication
- [ ] Automated version bumping based on commit messages
- [ ] Pre-release testing on develop branch
- [ ] Automated changelog generation from commit history
- [ ] Integration with semantic versioning
- [ ] Feature branch publication to staging hubs

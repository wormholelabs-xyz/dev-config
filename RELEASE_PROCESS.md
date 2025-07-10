# Release Walkthrough: From PR to Production

This document provides a step-by-step walkthrough of the complete release
process using semantic-release, covering all manual steps, approvals, and
automation triggers.

## 🏗️ **System Overview**

### **Release Strategy**

- **Main branch** → Automatic pre-releases (`1.0.0-development.1`,
  `1.0.0-development.2`, etc.)
- **Release branches** → Release candidates (`1.0.0-rc.1`, `1.0.0-rc.2`, etc.)
- **Production** → Stable releases (`1.0.0`, `1.1.0`, etc.) with 2+ approvals
- **Hotfixes** → Emergency fixes on existing release branches (`1.0.1-rc.1`,
  then `1.0.1`) with 2+ approvals

### **Security & Approvals**

- ✅ **Pre-releases**: Automatic, no approvals needed
- 🔒 **Production releases**: Require 2 different approvals via GitHub
  Environment Protection
- 📝 **All releases**: Full audit trail and provenance attestation

---

## 📋 **Complete Happy Path Walkthrough**

### **Phase 1: Development & PR Creation**

#### Step 1: Create Feature Branch

```bash
git checkout main
git pull origin main
git checkout -b feat/awesome-new-feature
```

#### Step 2: Make Changes with Conventional Commits

```bash
# Make your changes
git add .
git commit -m "feat: add awesome new feature that does amazing things

This feature enables users to do incredible things with better performance.

Closes #123"
```

#### Step 3: Create Pull Request

```bash
git push origin feat/awesome-new-feature
# Create PR via GitHub UI targeting 'main' branch
```

#### Step 4: Review Semantic Release Preview

- **Automatic**: GitHub Action runs `Semantic Release Preview`
- **Result**: Bot comment shows what release would be created
- **Example Output**:

  ```
  🤖 Semantic Release Preview

  ✅ Release Would Be Created
  Target Branch: main
  Current Version: 0.0.0-development
  Next Version: 1.1.0
  Release Type: Pre-release (1.1.0-development.1)
  NPM Tag: development

  ℹ️ This will create an automatic pre-release when merged.
  ```

### **Phase 2: Pre-release (Automatic)**

#### Step 5: Merge PR to Main

```bash
# Via GitHub UI: Click "Merge pull request"
# Or via CLI:
git checkout main
git merge feat/awesome-new-feature
git push origin main
```

#### Step 6: Automatic Pre-release Workflow Triggers

- **Trigger**: Push to `main` branch
- **Workflow**: `.github/workflows/prerelease.yml`
- **Actions Performed**:
  1. ✅ Security validation
  2. ✅ Install dependencies with pnpm
  3. ✅ Build package
  4. ✅ Run `npm audit signatures`
  5. ✅ Create pre-release with semantic-release
  6. ✅ Publish to npm with `development` tag
  7. ✅ Create GitHub release (pre-release)
  8. ✅ Generate audit log

#### Step 7: Verify Pre-release

**Manual Steps**:

```bash
# Check npm
npm view @wormhole-labs/dev-config@development

# Install and test
npm install @wormhole-labs/dev-config@development
```

**GitHub UI**:

- ✅ Check Releases page for new pre-release
- ✅ Verify green provenance badge on npm
- ✅ Review security audit logs

### **Phase 3: Release Candidate Creation (Automated)**

#### Step 8: Create Release Branch & RC (Automated)

**GitHub UI - One-Click Process**:

1. Go to **Actions** tab
2. Select **"Create Release Branch & RC"** workflow
3. Click **"Run workflow"**
4. Fill in the form:
   - **Target Version**: `1.1.0` (semantic version)
   - **Source Selection**: Choose "Fetch latest 10 development releases"
   - **Custom Tag**: (leave empty to use latest development release)
5. Click **"Run workflow"**

#### Step 9: Automatic RC Creation

**Fully Automated Process**:

1. ✅ **Validates** target version format
2. ✅ **Fetches** latest development releases (shows last 10)
3. ✅ **Creates** `release/1.1.0` branch from selected development release
4. ✅ **Pushes** branch to GitHub
5. ✅ **Triggers** Production Release workflow automatically
6. ✅ **Creates** Release Candidate `1.1.0-rc.1` (**No approvals needed**)
7. ✅ **Publishes** to npm with `@rc` tag
8. ✅ **Creates** GitHub release
9. ✅ **Generates** CHANGELOG.md entry
10. ✅ **Creates** provenance attestation

#### Step 10: RC Available for Testing

**Immediate Results**:

- ✅ **Release Candidate** `1.1.0-rc.1` published to npm
- ✅ **Install command**: `npm install @wormhole-labs/dev-config@rc`
- ✅ **GitHub Release** created with release notes
- ✅ **Branch** `release/1.1.0` ready for final release

### **Phase 4: Production Release**

#### Step 14: Test Release Candidate

**Manual Steps**:

```bash
# Test RC thoroughly
npm install @wormhole-labs/dev-config@rc
# Run your test suites
# Verify in staging environments
```

#### Step 15: Merge Release Branch to Main

```bash
git checkout main
git merge release/1.1.0
git push origin main
```

#### Step 16: Trigger Final Stable Release

**Manual GitHub Action Trigger**:

1. Go to **Actions** tab
2. Select **"Production Release"** workflow
3. Click **"Run workflow"**
4. Fill in the form:
   - **Branch**: `release/1.1.0`
   - **Release Type**: `final`
5. Click **"Run workflow"**

#### Step 17: Final Release Environment Protection

**2-Approval Process for Stable Release Only**:

1. **Workflow pauses** at "production" environment
2. **Email notification** sent to required reviewers
3. **First reviewer** clicks "Review deployments" → "Approve and deploy"
4. **Second reviewer** (different person) clicks "Review deployments" → "Approve
   and deploy"
5. **Workflow continues** after 2nd approval

#### Step 18: Stable Release Published

**Automatic after 2 approvals**:

1. ✅ Creates `v1.1.0` stable release
2. ✅ Publishes to npm with `latest` tag
3. ✅ Updates CHANGELOG.md on release branch
4. ✅ Creates provenance attestation
5. ✅ Comprehensive security audit

#### Step 19: Automatic Changelog PR Creation

**Automatic after stable release**:

1. ✅ **PR created automatically** to merge changelog from release branch to
   main
2. ✅ **PR title**: `chore: merge changelog for v1.1.0 to main`
3. ✅ **PR labels**: `changelog`, `automated`, `release`
4. ✅ **Contains only**: Updated CHANGELOG.md with release notes

**Manual Action Required**:

- **Review and merge** the changelog PR to update main branch with release
  history

### **Phase 5: Verification & Cleanup**

#### Step 20: Verify Production Release

**Manual Steps**:

```bash
# Verify npm package
npm view @wormhole-labs/dev-config

# Install latest
npm install @wormhole-labs/dev-config

# Check GitHub
# - ✅ Release created with proper tag
# - ✅ CHANGELOG.md updated
# - ✅ Provenance badge visible on npm
```

#### Step 21: Cleanup

```bash
# Delete feature branch
git branch -d feat/awesome-new-feature
git push origin --delete feat/awesome-new-feature

# Delete release branch (after changelog PR is merged)
git branch -d release/1.1.0
git push origin --delete release/1.1.0
```

---

## 🚨 **Hotfix Process**

### Emergency Hotfix Walkthrough

#### Step 1: Identify Source Release Branch

```bash
# Find the release branch for the version needing the hotfix
# For example, if fixing v1.1.0, use release/1.1.0
git checkout release/1.1.0
git pull origin release/1.1.0
```

#### Step 2: Apply Hotfix with Conventional Commit

```bash
# Make the emergency fix
git add .
git commit -m "fix: resolve critical security vulnerability in auth module

This fixes CVE-2024-XXXX by properly validating input parameters.

BREAKING CHANGE: Auth module now requires additional validation parameter"
```

#### Step 3: Push to Release Branch

```bash
git push origin release/1.1.0
```

#### Step 4: Automatic RC Creation

**Automatic Process**:

1. ✅ **Push triggers** automatic RC creation
2. ✅ **Creates** `1.1.1-rc.1` (or `2.0.0-rc.1` if breaking)
3. ✅ **Publishes** to npm with `@rc` tag
4. ✅ **No approvals needed** for RC

#### Step 5: Test and Final Release

**Manual Process**:

1. **Test the RC**: `npm install @wormhole-labs/dev-config@rc`
2. **Trigger final release**: Actions → Production Release → Set type to "final"
3. **2-approval process**: Same as regular final releases
4. **Stable hotfix published**: `1.1.1` with `@latest` tag

---

## 🔧 **Manual Intervention Points**

### **Required Manual Steps**

1. **PR Creation**: Create and merge PRs (normal development)
2. **RC Creation**: GitHub UI → Actions → "Create Release Branch & RC" → Run
   workflow
3. **Final Release Triggering**: GitHub UI → Actions → "Production Release" →
   Set type to "final"
4. **Environment Approvals**: 2 different people must approve **final releases
   only**
5. **Changelog PR Review**: Review and merge the automatic changelog PR to main
6. **Release Verification**: Test packages after release
7. **Branch Cleanup**: Delete feature/release branches

### **Approval Requirements**

- **Pre-releases**: ❌ No approvals (automatic)
- **Release Candidates**: ❌ No approvals (automatic after branch creation)
- **Final Stable Releases**: ✅ 2 approvals required
- **Hotfix RCs**: ❌ No approvals (automatic after push to release branch)
- **Final Hotfix Releases**: ✅ 2 approvals required

### **Security Gates**

1. **Branch validation**: Only allowed branches can trigger releases
2. **Actor validation**: Only team members can approve
3. **Different approver**: 2nd approver must be different person
4. **Audit logging**: Every action logged with timestamps
5. **Provenance**: Cryptographic proof of build integrity

---

## 📊 **Monitoring & Verification**

### **What to Check After Each Release**

#### NPM Package

```bash
npm view @wormhole-labs/dev-config
# ✅ Version updated
# ✅ Provenance badge visible
# ✅ Correct tag (development/rc/latest)
```

#### GitHub

- ✅ Release created with proper tag
- ✅ CHANGELOG.md updated in repository
- ✅ Security audit logs available
- ✅ Workflow execution successful

#### Security Verification

- ✅ Provenance attestation generated
- ✅ Audit logs captured
- ✅ Only authorized approvers used
- ✅ No security scan failures

---

## 🎯 **Key Benefits Achieved**

✅ **Automatic pre-releases** - Every commit to main becomes testable  
✅ **Security approval gates** - 2+ people must approve production releases  
✅ **Full audit trail** - Every release action logged and traceable  
✅ **NPM provenance** - Cryptographic proof of package integrity  
✅ **Zero manual commands** - Everything automated after approvals  
✅ **Conventional commits** - Structured commit messages drive versioning  
✅ **Hotfix capability** - Emergency fixes via existing release branches with
same security controls  
✅ **Future monorepo ready** - Can extend to multiple packages later

This system provides **maximum security** with **minimal friction** for
developers while maintaining **full traceability** of all release activities.

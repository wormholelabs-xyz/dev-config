# Release Walkthrough: From PR to Production

This document provides a step-by-step walkthrough of the complete release
process using semantic-release, covering all manual steps, approvals, and
automation triggers.

## 🏗️ **System Overview**

### **Release Strategy**

- **Main branch** → Automatic pre-releases (`1.0.0-alpha.1`, `1.0.0-alpha.2`,
  etc.)
- **Release branches** → Release candidates (`1.0.0-rc.1`, `1.0.0-rc.2`, etc.)
- **Production** → Stable releases (`1.0.0`, `1.1.0`, etc.) with 2+ approvals
- **Hotfix branches** → Emergency fixes (`1.0.1`, `1.0.2`, etc.) with 2+
  approvals

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
  Release Type: Pre-release (1.1.0-alpha.1)
  NPM Tag: alpha

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
  6. ✅ Publish to npm with `alpha` tag
  7. ✅ Create GitHub release (pre-release)
  8. ✅ Generate audit log

#### Step 7: Verify Pre-release

**Manual Steps**:

```bash
# Check npm
npm view @wormhole-labs/dev-config@alpha

# Install and test
npm install @wormhole-labs/dev-config@alpha
```

**GitHub UI**:

- ✅ Check Releases page for new pre-release
- ✅ Verify green provenance badge on npm
- ✅ Review security audit logs

### **Phase 3: Release Candidate Preparation**

#### Step 8: Create Release Branch (Manual)

```bash
git checkout main
git pull origin main
git checkout -b release/1.1.0
git push origin release/1.1.0
```

#### Step 9: Release Candidate Preview

- **Automatic**: Push to `release/*` triggers preview
- **Workflow**: `.github/workflows/changelog-preview.yml`
- **Result**: Shows RC version (`1.1.0-rc.1`) will be created

#### Step 10: RC Creation (Manual Trigger)

**GitHub UI**:

1. Go to **Actions** tab
2. Select **"Production Release"** workflow
3. Click **"Run workflow"**
4. Select branch: `release/1.1.0`
5. Set release type: `release`
6. Click **"Run workflow"**

#### Step 11: Environment Protection - First Approval

**GitHub UI**:

1. Workflow pauses at **"production"** environment
2. **Email notification** sent to required reviewers
3. **First reviewer** clicks **"Review deployments"**
4. Reviews changes and clicks **"Approve and deploy"**

#### Step 12: Environment Protection - Second Approval

**GitHub UI**:

1. Workflow still paused (needs different approver)
2. **Second reviewer** (different from first) clicks **"Review deployments"**
3. Reviews changes and clicks **"Approve and deploy"**
4. **Workflow continues** after 2nd approval

#### Step 13: RC Release Execution

**Automatic after approvals**:

1. ✅ Security validation
2. ✅ Install dependencies
3. ✅ Build package
4. ✅ Run semantic-release
5. ✅ Publish `1.1.0-rc.1` with `rc` tag
6. ✅ Create GitHub release
7. ✅ Generate CHANGELOG.md entry
8. ✅ Create provenance attestation
9. ✅ Post-release security audit

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

#### Step 16: Production Release Triggers

**Automatic**:

- Push to `main` from release branch triggers production workflow
- Semantic-release detects RC should become stable release

#### Step 17: Production Environment Protection

**Same 2-approval process as RC**:

1. First reviewer approves
2. Second reviewer (different person) approves
3. Production release executes

#### Step 18: Stable Release Published

**Automatic after approvals**:

1. ✅ Creates `v1.1.0` stable release
2. ✅ Publishes to npm with `latest` tag
3. ✅ Updates CHANGELOG.md in repository
4. ✅ Creates provenance attestation
5. ✅ Comprehensive security audit

### **Phase 5: Verification & Cleanup**

#### Step 19: Verify Production Release

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

#### Step 20: Cleanup

```bash
# Delete feature branch
git branch -d feat/awesome-new-feature
git push origin --delete feat/awesome-new-feature

# Delete release branch
git branch -d release/1.1.0
git push origin --delete release/1.1.0
```

---

## 🚨 **Hotfix Process**

### Emergency Hotfix Walkthrough

#### Step 1: Create Hotfix Branch

```bash
git checkout main  # or tag where issue exists
git checkout -b hotfix/1.1.1
```

#### Step 2: Fix Issue with Conventional Commit

```bash
# Make fix
git add .
git commit -m "fix: resolve critical security vulnerability in auth module

This fixes CVE-2024-XXXX by properly validating input parameters.

BREAKING CHANGE: Auth module now requires additional validation parameter"
```

#### Step 3: Push and Trigger Hotfix

```bash
git push origin hotfix/1.1.1
```

#### Step 4: Hotfix Environment Protection

**Same 2-approval process**:

1. **GitHub UI**: Actions → Production Release workflow runs
2. **First approval**: Required reviewer approves hotfix
3. **Second approval**: Different reviewer approves hotfix
4. **Automatic execution**: Hotfix `1.1.1` (or `2.0.0` if breaking) released

---

## 🔧 **Manual Intervention Points**

### **Required Manual Steps**

1. **PR Creation**: Create and merge PRs (normal development)
2. **Release Branch Creation**: `git checkout -b release/X.Y.Z`
3. **Workflow Triggering**: GitHub UI → Actions → Run workflow
4. **Environment Approvals**: 2 different people must approve via GitHub UI
5. **Release Verification**: Test packages after release
6. **Branch Cleanup**: Delete feature/release branches

### **Approval Requirements**

- **Pre-releases**: ❌ No approvals (automatic)
- **Release Candidates**: ✅ 2 approvals required
- **Production Releases**: ✅ 2 approvals required
- **Hotfixes**: ✅ 2 approvals required

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
# ✅ Correct tag (alpha/rc/latest)
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
✅ **Hotfix capability** - Emergency releases with same security controls  
✅ **Future monorepo ready** - Can extend to multiple packages later

This system provides **maximum security** with **minimal friction** for
developers while maintaining **full traceability** of all release activities.

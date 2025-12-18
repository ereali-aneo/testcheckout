# Test Checkout Demonstration

This repository demonstrates the issue with using `ref: ${{ github.ref }}` in GitHub Actions checkout steps for pull requests.

## The Problem

In ArmoniK.Core workflows, all checkout actions use:
```yaml
- uses: actions/checkout@v4
  with:
    ref: ${{ github.ref }}  # ← PROBLEMATIC!
```

This causes issues with pull requests because:

### On `push` events:
- `github.ref` = `refs/heads/main` ✅ Works fine

### On `pull_request` events:
- `github.ref` = `refs/pull/123/merge` ❌ Problematic
- This ref exists in the base repository but not in the fork
- Can checkout wrong code or fail entirely

## The Solution

**Remove `ref: ${{ github.ref }}` entirely!**

```yaml
- uses: actions/checkout@v4
  # No ref parameter - let GitHub Actions decide automatically
```

GitHub Actions automatically:
- On `push`: checks out the pushed branch
- On `pull_request`: checks out the PR merge commit (PR code + base branch merged)
- Works correctly for both internal and fork PRs

## How to Test This Demonstration

1. **On Push to main:**
   - Both jobs will succeed
   - Both show the same commit

2. **On Pull Request (internal or fork):**
   - Compare the outputs of both jobs
   - The job WITHOUT `ref` will show the correct merge commit
   - The job WITH `ref` may show different behavior or fail

## Run the Demo

1. Push this repository to GitHub
2. Create a pull request
3. Check the GitHub Actions output
4. Compare the two jobs to see the difference

## Expected Results

The workflow will show side-by-side:
- ✅ Correct behavior (without ref)
- ❌ Problematic behavior (with ref)

This proves that removing `ref: ${{ github.ref }}` from ArmoniK.Core workflows will fix PR support.

---
name: security-triage
description: "Triage GitHub security advisories (GHSA) and security reports with shipped-tag and trust-model proof. Complements security-review. Use when reviewing GHSA reports for any repository."
---
<!-- FORK ADAPTADO: Quitar todas las refs hardcodeadas a openclaw/openclaw y 'Peter'; reemplazar pbcopy (macOS) → Set-Clipboard (PowerShell); generalizar npm view command; skill ahora aplica a cualquier repo · origen: openclaw-openclaw-agents-skills-security-triage-skill-md · 2026-06-26 -->

# Security Triage

Use when reviewing GitHub security advisories (GHSA), security drafts, or security reports for any repository.

Goal: high-confidence maintainers' triage without over-closing real issues or shipping unnecessary regressions.

## Close Bar

Close only if one of these is true:

- duplicate of an existing advisory or fixed issue
- invalid against shipped behavior
- out of scope under `SECURITY.md`
- fixed before any affected release/tag

Do not close only because `main` is fixed. If latest shipped tag or npm/PyPI release is affected, keep it open until released or published with the right status.

## Required Reads

Before answering:

1. Read `SECURITY.md` in the target repository.
2. Read the GHSA body:

   ```powershell
   gh api /repos/{owner}/{repo}/security-advisories/{GHSA}
   ```

3. Inspect the exact implicated code paths.
4. Verify shipped state:

   ```powershell
   git tag --sort=-creatordate | Select-Object -First 20
   # For npm packages:
   npm view {package} version
   # For PyPI packages:
   pip index versions {package}
   # Check if fix commit is in a tag:
   git tag --contains {fix-commit}
   # Inspect file at a tag:
   git show {tag}:path/to/file
   ```

5. Search for canonical overlap:

   ```powershell
   gh search issues --repo {owner}/{repo} --match title,body,comments -- "{terms}"
   gh search prs --repo {owner}/{repo} --match title,body,comments -- "{terms}"
   gh api /repos/{owner}/{repo}/security-advisories --paginate
   ```

## Review Method

For each advisory, decide:

- `close`
- `keep open`
- `keep open but narrow`

Default to one advisory at a time when comments/closures are involved:

1. Review exactly one GHSA.
2. Print the GHSA URL first.
3. Summarize the decision and evidence for discussion.
4. Draft one maintainer-ready comment.
5. Copy that one comment to the clipboard (see Clipboard Step below).
6. Stop and wait for the maintainer to post/discuss before moving to the next GHSA.

Do not batch multiple close comments unless the maintainer explicitly asks for a batch.

## Check in this order

1. **Trust model**
   - Is the prerequisite already inside trusted host/local/plugin/operator state?
   - Does `SECURITY.md` explicitly call this class out as out of scope or hardening-only?

2. **Shipped behavior**
   - Is the bug present in the latest shipped tag or release?
   - Was it fixed before release?

3. **Exploit path**
   - Does the report show a real boundary bypass, not just prompt injection, local same-user control, or helper-level semantics?
   - If data only moves between trusted workspace-memory files called out in `SECURITY.md`, do not treat "injection markers" alone as a security bug.
   - In that case, frame sanitization as optional hardening only if it preserves expected workflows.

4. **Functional tradeoff**
   - If a hardening change would reduce intended user functionality, call that out before proposing it.
   - Prefer fixes that preserve user workflows over deny-by-default regressions unless the boundary demands it.

5. **Hardening follow-up**
   - Even when the GHSA should close, ask whether a narrow hardening change would reduce footguns.
   - Separate hardening from vulnerability status. Phrase it as "not required for GHSA closure, but worth considering".
   - Bring up hardening only if it is concrete, low-risk, and preserves intended maintainer/operator workflows.
   - If hardening would require a product/security model change, say that explicitly.

## Response Format

When preparing a maintainer-ready close reply:

1. Print the GHSA URL first.
2. Draft a detailed response the maintainer can post.
3. Include:
   - Exact reason for close
   - Exact code refs
   - Exact shipped tag / release facts
   - Fix provenance or canonical duplicate GHSA when applicable
   - Optional hardening note only if worthwhile and functionality-preserving

Keep tone firm, specific, non-defensive.

## Public Wording Hygiene

- Keep raw commit hashes, PR titles/numbers, and fix-mechanism summaries out of public advisory text. Use the patched release/version field only.
- Keep exact commit SHAs, PRs, and implementation notes in internal notes and verification files.
- For hardening/no-publish outcomes, do not add exploit-heavy details, "Fixed by" text, or a "Fix Commit(s)" section. Thank reporters, preserve credit, state the `SECURITY.md` boundary, and say clearly that the GHSA will close without publication.
- For published CVE/GHSA text, prefer `### Patched Versions` with the fixed release. Do not explain how the patch works unless explicitly asked.
- Keep GHSA ids out of changelog and release-note wording unless explicitly asked.

## Discussion Mode

When the maintainer is manually posting GHSA comments, use this flow:

1. Show the URL.
2. Give a terse verdict (`close`, `keep open`, or `keep open but narrow`).
3. List the strongest evidence bullets.
4. State any optional hardening follow-up separately from the close reason.
5. Copy the proposed comment body (see Clipboard Step).
6. End the reply after the one advisory. Do not continue to the next until the maintainer says to continue.

If the GitHub API cannot post comments for private advisories, say so once and keep using clipboard/UI paste.

## Clipboard Step

After drafting the final post body for the current advisory, copy it to the clipboard:

**PowerShell (Windows)**:

```powershell
Set-Clipboard -Value @"
<final response here>
"@
```

**macOS** (if running in macOS environment):

```bash
pbcopy <<'EOF'
<final response>
EOF
```

Tell the maintainer that the clipboard now contains the proposed response for that advisory.

## Useful Commands

```powershell
# Get a specific GHSA
gh api /repos/{owner}/{repo}/security-advisories/{GHSA}

# List all GHSAs for a repo
gh api /repos/{owner}/{repo}/security-advisories --paginate

# Recent git tags
git tag --sort=-creatordate | Select-Object -First 20

# Check npm package version
npm view {package} version

# Check if fix is in a tag
git tag --contains {commit}

# Inspect file at specific tag
git show {tag}:{path/to/file}

# Search issues/PRs
gh search issues --repo {owner}/{repo} --match title,body,comments -- "{terms}"
gh search prs --repo {owner}/{repo} --match title,body,comments -- "{terms}"
```

## Decision Notes

- "fixed on main, unreleased" is usually NOT a close.
- "needs attacker-controlled trusted local state first" is usually out of scope.
- "same-host same-user process can already read/write local state" is usually out of scope.
- "trusted workspace memory promotes/reindexes trusted workspace memory" is usually out of scope unless it crosses a documented boundary.
- "helper function behaves differently than documented config semantics" is usually invalid.
- If only the severity is wrong but the bug is real, keep it open and narrow the impact in the reply.

---
description: Generate a structured merge commit message from PR changes
---

You are composing a MERGE COMMIT MESSAGE in Claude Code.

AUTO-CONTEXT
- Infer PR# from branch name or commits (e.g. "#123").
- Identify base branch (master/main) with: git remote show origin | grep 'HEAD branch'
- Get ONLY this PR's changes with: git diff <base-branch>...HEAD
- Get ONLY this PR's commits with: git log <base-branch>..HEAD --oneline
- This avoids including already-merged changes from earlier rebases.
- Read for themes, not details. Spot material breaking/security impacts only.
- Ask max 2 questions ONLY if PR# or motivation unknown.

OUTPUT (plain text, no Markdown, max 14 lines total)

Title: Brief description (#PR)
- <= 72 chars, imperative mood

Why
- One line. The problem or motivation.

Summary
- One to two lines. Core changes across PR.

Key improvements
- Max 3 bullets, one line each. Skip if minor.

Impact
- Existing: one line
- New: one line

Risks
- Breaking + security. Max 2 bullets or "None".

CONSTRAINTS
- No repeated phrases between sections.
- No section > 2 lines (except improvements: 3 bullets).
- Skip "This PR", "we", filler words.
- Wrap lines to ~72 chars.

SAVE
num=1; while [ -e "/tmp/commit-msg-$num.txt" ]; do num=$((num+1)); done
cat > "/tmp/commit-msg-$num.txt" << 'EOF'
<YOUR_COMMIT_MESSAGE>
EOF
echo "Saved: /tmp/commit-msg-$num.txt"

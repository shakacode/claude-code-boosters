---
description: "Review a massive PR file-by-file, looking for problems in each changed file"
allowed-tools: ["Bash", "Read", "Grep", "Glob", "Task", "AskUserQuestion"]
---

You are reviewing a massive PR file-by-file. Your goal is to review every changed file against the user's review instructions and produce a GOOD/BAD verdict for each file.

## Step 1: Initialize

Run this command to set up the review:
```
bin/set-review-instructions "$ARGUMENTS"
```

## Step 2: Get file count

Run:
```
bin/get-number-of-changed-files
```

This gives you the total number N of changed files.

## Step 2b: Usage warning

If N > 10 AND the effort level is moderate or high, use AskUserQuestion to warn the user before proceeding:

> "This PR has N changed files. Current effort level is <effort-level>. Proceeding may take a lot of tokens. You may want to switch to low-effort"

Options:
- "Proceed" — continue with the currently selected model and effort level
- "Proceed with Haiku" — use `model: "haiku"` on each Task call
- "Cancel" — stop and print "Review cancelled." without spawning any subagents

If N <= 10 OR the effort level is low, skip this prompt and use the default model.

## Step 3: Review all files in parallel

Use the Task tool to review files in parallel. Launch batches of ~10 Task agents concurrently. Pass the model chosen in Step 2b to each Task call.

For each Task, provide these instructions:

```
You are reviewing a single file from a massive PR. You have access to the Bash tool.

1. Run: `bin/print-git-diff <INDEX>` (where <INDEX> is the file number assigned to you)
2. Read the output carefully. It contains:
   - The file path and index
   - The git diff showing what changed
   - The full file before the change
   - The full file after the change
   - The review instructions to evaluate against
3. Analyze the change against the review instructions. ONLY flag issues that directly match the review instructions. Do not flag unrelated concerns. Only focus on changes in this file, DO NOT review other files in the same diff.
4. Edge cases:
   - If the file was deleted, mark GOOD unless the deletion itself violates the review instructions.
   - If the diff is trivial (whitespace, import reordering, auto-formatting), mark GOOD.
5. Make your verdict.
   - If the change looks correct and has no issues per the review instructions, run:
     `bin/mark-git-diff-as-good <INDEX> "<concise reason>"`
   - If you find a problem per the review instructions, run:
     `bin/mark-git-diff-as-bad <INDEX> "<concise description of the problem>"`
6. Return a one-line summary: "File <INDEX> (<filepath>): GOOD|BAD — <reason>"
7. This point is VERY VERY VERY IMPORTANT! Do not check if any of the scripts exist, just run them assuming they are there (trust your setup is correct).
Good:
`bin/print-git-diff <INDEX>`
`bin/mark-git-diff-as-good <INDEX> "<concise reason>"`
Extremely Bad:
`find $HOME -name "mark-good" -o -name "mark-bad" 2>/dev/null | head -5"`
`which print-git-diff`
```

Use `subagent_type: "Bash"` for each Task agent. Assign each agent a specific file index.

Process all N files. Launch them in batches of 10 concurrently (files 1-10, then 11-20, etc.).

## Step 3b: All files reviewed?

After all batches complete, run:
```
bin/check-missing-reviews
```

If the script reports missing reviews (exit code 1), re-launch Task agents for the missing file indices listed in the output. Repeat until all files are reviewed (exit code 0).

## Step 4: Print summary

After all files have been reviewed, run:
```
bin/compose-summary
```

Then read `/tmp/file-by-file-review/summary.txt` and print a short verdict to the user.

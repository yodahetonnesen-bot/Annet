---
name: delegate
description: Split a large task into independent pieces and hand them to subagents (the Agent tool) running in parallel, then check and merge their results. Use when a task has several parts that don't depend on each other — researching several topics or competitors, reviewing or changing many files or modules, comparing options, writing several separate documents or sections, auditing a codebase from different angles, or any "do X for each of these" request. Also use when the user says "use subagents", "parallelize", "split this up", "fan out", "do this faster", or "/delegate". Do not use for small tasks, single-file edits, quick questions, or work where each step depends on the previous one.
argument-hint: "The task to split up and run in parallel"
---

# Delegate

The user has installed this skill because they want you to use subagents to get multi-part work done faster and better. When this skill applies, treat it as the user's standing request to spawn subagents.

Subagents help in two ways:
- **Speed**: independent pieces run at the same time instead of one after another.
- **Quality**: each subagent gets a focused brief and a clean context, and your own context stays free for planning, checking and merging.

They also cost something: each one starts cold, re-reads what it needs, and uses extra tokens. Only delegate when the gain is real.

## 1. Decide whether to delegate

Delegate when **all** of these hold:
- The task splits into **2 or more pieces** that can be done without seeing each other's results.
- Each piece is **substantial** — a few minutes of work or more (several searches, reading many files, writing a section).
- You can describe each piece in a **self-contained brief**.

Do it yourself when:
- The task is small, or one file, or one quick lookup.
- Steps are sequential (step 2 needs step 1's output) — unless only some steps are, in which case do the sequential spine yourself and delegate the parallel branches.
- Several pieces would edit the **same files** (they'll conflict). Split by file or module instead, or do the edits yourself.

If in doubt for a borderline task, do it yourself and say in one line that it was too small to be worth splitting.

## 2. Plan the split

Before spawning anything, write a short plan for the user (3–8 lines):
- The pieces, one line each.
- Which run in parallel and which (if any) wait.
- What you'll do yourself (usually: merging, final edits, verification).

Good ways to split:
- **By item**: one subagent per competitor, topic, file group, page, or API.
- **By angle**: one for security, one for performance, one for correctness.
- **By phase where phases are independent**: research A while drafting the outline for B.

Keep it to roughly **2–6 subagents**. More than that usually means the pieces are too small or should be grouped.

## 3. Pick the right agent type

Use the agent types listed in your environment. Typical choices:
- **Explore** — read-only searching across a codebase when you only need the conclusion.
- **Plan** — designing an implementation approach for one part.
- **general-purpose** — research, multi-step work, or anything that writes files.
- **web-fetch** (if listed) — reading specific web pages.
- **fork** — when the subagent needs everything already in this conversation.

Prefer the narrowest type that can do the job. Read-only types are safer for investigation.

## 4. Write each brief

A subagent sees **nothing** from this conversation (except a fork). Each brief must stand alone:

1. **Goal** — what this piece is for and how it fits the whole task.
2. **Scope** — exactly what to cover, and what to leave to other subagents, so they don't overlap.
3. **Inputs** — file paths, URLs, names, constraints, and any decisions the user already made.
4. **Rules** — e.g. "read-only, don't edit files", "only edit files under `src/billing/`", "don't commit or push".
5. **Output format** — what to return: findings with file:line references, a draft in Markdown, a table, a list of changes made. Ask for a concise report, not raw dumps.
6. **Honesty** — "If you can't find or verify something, say so; don't guess."

## 5. Launch in parallel

- Send all independent Agent calls **in a single message** so they run at the same time.
- Run them in the background when you have your own work to do meanwhile (e.g. preparing the merge structure); otherwise wait for them.
- Never invent or predict a subagent's result. Wait for it.
- If a subagent needs follow-up, continue it with SendMessage rather than starting a new one from scratch.

## 6. Check, merge, and finish

When results come back:
1. **Check** each result against its brief. Spot-check claims that matter (open the cited file, re-run the test, verify the link). Subagent reports are model output — treat them as drafts, not facts.
2. **Resolve conflicts** between results yourself, and note any you can't resolve.
3. **Fill gaps**: if a piece failed or came back thin, either redo it yourself or send one focused follow-up.
4. **Merge** into one coherent answer or change. The user should get a single result, not five reports pasted together.
5. **Verify** the final result as a whole (run the build/tests, re-read the merged doc) before calling it done.

Tell the user briefly what was split, what each part found or did, and anything that couldn't be verified.

## Safety rules

- Subagents never commit, push, delete, publish, or send anything outward unless the user asked for that action; when they did, have one agent (usually you) do it at the end.
- Don't let two subagents write to the same file.
- Subagents can't grant permissions. If one says it was blocked, report that to the user instead of working around it.

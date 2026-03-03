# Alex Hormozi Full Course Content

A structured markdown knowledge base for Alex Hormozi business frameworks, plus a Claude Code command/router that selects the most relevant course notes for a user request.

## What is in this repository

This repo contains:

- `100M Leads Course/` - acquisition and lead generation modules
- `100M Offers Course/` - offer design, pricing, value, guarantees, and naming
- `Money Models Course/` - CAC, margins, payback, subscriptions, upsells, downsells, and monetization design
- `0-100M Scaling Course/` - additional scaling material currently not wired into the main command router
- `CONCEPT-GRAPH.MD` - the routing map used to connect business intents to course concepts and file clusters
- `hormozi-command.md` - the controller prompt for Claude Code

The repository is set up as a retrieval-oriented knowledge base, not just a document dump. The `hormozi-command.md` file tells Claude how to interpret a request, map it to the right frameworks, load the right notes, and generate an applied answer.

## Repository structure

```text
.
├── 0-100M Scaling Course/
├── 100M Leads Course/
├── 100M Offers Course/
├── Money Models Course/
├── CONCEPT-GRAPH.MD
└── hormozi-command.md
```

## How it works in Claude Code

### 1) `hormozi-command.md` is the controller

This file is designed to be used as a Claude Code custom command.

In Claude Code, markdown files placed in `.claude/commands/` still work as slash commands. The filename becomes the command name. So if you place this file at:

```text
.claude/commands/hormozi-command.md
```

you invoke it as:

```text
/hormozi-command [your request]
```

The command uses `$ARGUMENTS`, so whatever you type after `/hormozi-command` becomes the user request that the controller processes.

### 2) The command enforces a 3-role workflow

The prompt defines three strict stages:

1. **INTENT-ANALYST**
   - Parses the request
   - Infers business stage
   - Detects deliverable type
   - Extracts key Hormozi concepts needed

2. **CONTEXT-STRATEGIST**
   - Uses `CONCEPT-GRAPH.MD`
   - Maps the request to concepts, concept clusters, and course files
   - Limits context loading to the most relevant files
   - Builds full absolute file paths for reading

3. **SYNTHESIZER**
   - Reads the selected files
   - Applies the frameworks to the user's actual problem
   - Produces a strategy/spec/code/concept output with source-grounded reasoning

In other words, the command is not just "answer from the course." It is a routing-and-synthesis system.

### 3) `CONCEPT-GRAPH.MD` is the routing layer

This file is the lookup map for the controller. It contains:

- **Concept tiers** - maps core business concepts to their primary source files
- **Cross-course relationships** - defines which files should be paired when concepts depend on each other
- **Concept clusters** - pre-grouped file bundles like `PRICING_STRATEGY`, `OFFER_DESIGN`, `PAID_ACQUISITION`, `SUBSCRIPTION_MODEL`
- **Business stage routing** - maps stage signals like pre-revenue, traction, scaling, or monetization optimization to the right starting clusters
- **Intent patterns** - maps request types like pricing, first customers, subscriptions, ads, referrals, etc. to the right file groups
- **File path reference** - tells Claude how to convert shorthands like `Leads/07` into real file paths

That means the graph is not content for end users. It is infrastructure for prompt-time retrieval.

## Current path problems you need to fix

The repository as published and the routing files are **not aligned** yet.

### Problem 1 - The command expects a different folder location than this repo currently shows

`hormozi-command.md` expects the knowledge base to live under:

```text
[PLACE FILE PATH HERE]/.claude/knowledge/Alex Hormozi Courses/
```

But this repository currently presents the course folders and routing files at the repository root.

So before using the command, you need to decide where the knowledge base will actually live on disk, then replace every placeholder with the **real absolute path**.

### Problem 2 - The concept graph uses the wrong Leads folder name

In `CONCEPT-GRAPH.MD`, Section 6 currently uses:

```text
100M - Leads Course
```

But the repository root shows:

```text
100M Leads Course
```

That hyphen mismatch will break path construction on a real filesystem.

### Problem 3 - The command references `CONCEPT-GRAPH.md`, but the repo file is `CONCEPT-GRAPH.MD`

If the filesystem is case-sensitive, this matters.

You should either:

- rename the file to `CONCEPT-GRAPH.md`, or
- update `hormozi-command.md` to match `CONCEPT-GRAPH.MD`

### Problem 4 - The concept graph says to use the file index in `hormozi.md`

There is no `hormozi.md` in the repo root.
The file is named:

```text
hormozi-command.md
```

That reference should be corrected.

## Recommended Claude Code layout

A clean project layout would be:

```text
your-project/
├── .claude/
│   ├── commands/
│   │   └── hormozi-command.md
│   └── knowledge/
│       └── Alex Hormozi Courses/
│           ├── CONCEPT-GRAPH.MD
│           ├── 0-100M Scaling Course/
│           ├── 100M Leads Course/
│           ├── 100M Offers Course/
│           └── Money Models Course/
└── ...
```

## Exact edits to make

### Edit `hormozi-command.md`

Replace this:

```text
[PLACE FILE PATH HERE]/.claude/knowledge/Alex Hormozi Courses/CONCEPT-GRAPH.md
```

with your real path, for example:

```text
/Users/yourname/projects/your-project/.claude/knowledge/Alex Hormozi Courses/CONCEPT-GRAPH.MD
```

Replace this base path block:

```text
[PLACE FILE PATH HERE]/.claude/knowledge/Alex Hormozi Courses/
```

with your actual absolute base path, for example:

```text
/Users/yourname/projects/your-project/.claude/knowledge/Alex Hormozi Courses/
```

### Edit `CONCEPT-GRAPH.MD`

Replace Section 6 with the real base paths that match your filesystem. Example:

```text
100M Leads Course: /Users/yourname/projects/your-project/.claude/knowledge/Alex Hormozi Courses/100M Leads Course/
100M Offers Course: /Users/yourname/projects/your-project/.claude/knowledge/Alex Hormozi Courses/100M Offers Course/
Money Models Course: /Users/yourname/projects/your-project/.claude/knowledge/Alex Hormozi Courses/Money Models Course/
```

Also fix:

- `100M - Leads Course` -> `100M Leads Course`
- `hormozi.md` -> `hormozi-command.md`

## Recommended cleanup before publishing or using

1. Normalize filenames and casing
   - Prefer `.md` consistently instead of mixing `.MD` and `.md`
2. Keep one path convention everywhere
   - Either all files assume `.claude/knowledge/Alex Hormozi Courses/...`
   - Or all files assume repository-root relative storage
3. Decide whether this is a **legacy custom command** or a **Claude Code skill**
   - Current Anthropic docs say custom commands still work
   - Skills are now the recommended format
4. Add a project `CLAUDE.md`
   - Document where the knowledge base lives
   - Document how to invoke the command
   - Document that scaling course files are currently out of scope unless explicitly added to routing

## Suggested invocation examples

```text
/hormozi-command Help me price a new B2B SaaS product for agencies.
```

```text
/hormozi-command I have zero customers and no audience. Give me a first-client acquisition plan.
```

```text
/hormozi-command Design an upsell flow for my low-ticket offer and include subscription options.
```

## Best interpretation of the repo

This repository is best understood as:

- a markdown knowledge base of Hormozi course notes/transcripts
- a routing graph that turns business requests into relevant file selections
- a Claude Code command that applies those notes to real problems instead of dumping summaries

If you fix the path mismatches, it becomes a usable domain-specific Claude Code retrieval workflow.

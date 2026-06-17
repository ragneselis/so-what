# discovery-kit

A product-discovery toolkit for product builders - anyone who owns what gets built and why, whether you ship with code, no-code, or a prototype. A Claude Code plugin that stops you building for a problem nobody has, or missing the problem with what you build.

## What it does

discovery-kit forces you through structured product discovery before you start building. It asks hard questions, challenges your assumptions, and won't let you skip ahead until you've clarified what you're building and why.

### Workflows

1. **Problem Statement** - Who has the problem? What is it? Why does it matter?
2. **Interview Script** - A JTBD-style interview script to validate the problem with real people *before* you build anything downstream. Walks the switch/timeline and the four forces, and includes a kill question.
3. **Competitive Research** - What already exists? Why is your approach different?
4. **Jobs to be Done** - What progress are your users trying to make? Draft jobs as hypotheses, then ground them in your interview results. Every job carries a status (hypothesis, supported, refined, contradicted, discovered) so a guess never masquerades as a validated need.
5. **Personas** - Who specifically are you building for? Behavioural patterns grounded in evidence - real constraints, a job they're trying to get done, and how they solve it today. No demographics, no fictional backstory, no stock-photo personas.
6. **Product Document** - Vision + outcome-driven requirements assembled from all the above, plus a concrete **Product Conception** (form factor and platform, look & feel, functionality scope, deployment) so the built thing is actually picturable - not just a list of outcomes.

Minimum path: Problem Statement + Product Document.

### Open questions don't get lost

Every unvalidated assumption raised during discovery goes into a shared register (`product/open-questions.md`) instead of dying at the bottom of a document. The hub surfaces them as you work and routes you to whatever closes them. Before it assembles the Product Document, discovery-kit forces a decision on each testable one: answer it, plan how to validate it, or knowingly accept the risk. Nothing reaches the final doc as a silent guess.

### Interviews are a first-class input

You can drop interview notes or transcripts into `product/interviews/` (or paste them into chat) at any point. discovery-kit picks them up automatically, synthesizes them into your jobs, and stamps each one as handled - and if an interview contradicts an earlier artifact, it routes you back to fix that instead of quietly papering over it.

### Already have a product? Add features the same way

Once a Product Document exists, discovery-kit switches to feature mode. A new feature isn't bolted into the doc directly - it goes through its own scoped discovery from the feature's own problem, its riskiest assumption gets validated like anything else, and it lands in the Product Document's Features backlog as `Status: Open`, flipped to `Done` when built.

### Out of scope

1. **Analytics & instrumentation** - how to collect, pipe, and analyse user-behaviour data after launch. (discovery-kit DOES define a success signal for each key outcome in the Product Document - it just doesn't build your analytics stack.)
2. **Building it out** - discovery-kit owns discovery and planning, not the build. When your Product Document is ready, take it to whatever build method you use - write the code yourself, use a no-code tool, make a prototype, or hand it to someone else as the spec. If you happen to be building with code in Claude Code, you can pass the artifacts to coding skills (Obra Superpowers is just one example of a code path) for planning and implementation. 

## Install

Copy paste this to Claude Code CLI and run:

```bash
claude plugins marketplace add ragneselis/discovery-kit
claude plugins install discovery-kit@discovery-kit --scope user
```

Restart Claude Code. The plugin activates automatically when you start a new project.

## How it works

When you signal you want to build something, discovery-kit guides you through product discovery. It asks opinionated multiple-choice questions with recommendations, so you sharpen the thinking behind what you're building - who it's for, what problem it solves, and why it's worth making.

All artifacts are saved to a `product/` directory in your project.

## Uninstall

```bash
claude plugins uninstall discovery-kit
claude plugins marketplace remove discovery-kit
```

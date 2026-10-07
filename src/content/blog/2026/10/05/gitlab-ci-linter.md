---
title: GitLab CI Linter
date: 2026-10-05
description: How gitlab-ci-linter flattens GitLab CI config files and catches common pipeline problems.
category: blog
tags: [gitlab, ci, ai]
---

GitLab's CI pipeline is configured with yaml files that have some unique and quirky rules. This can make it difficult to debug or diagnose problems. GitLab will often present an error that simply says the pipeline is invalid, usually without pointing to a specific line number.

I wrote gitlab-ci-linter at Superhuman (fka Grammarly) to debug problems like these. Now that the company is moving off GitLab and has no more use for it, I've gotten permission to open-source it. If you have a `.gitlab-ci.yml` file, you can run it with

```sh
npx gitlab-ci-linter .gitlab-ci.yml
```

## Flattening the config

The package works by first flattening the yaml config according to GitLab's semantics, then applying a handful of heuristic rules to the flat config. To summarize and simplify the approach, here are the flattening steps:

1. Follow all `include:` references to other local and remote yaml files. These can recursively include further documents, and can match files using globs.
2. Spread properties using the `<<:` and yaml anchors.
3. Inline properties from parents using `extends:` inheritance, as well as `defaults:` for variables.
4. Inline all `!reference` blocks, GitLab's custom yaml extension for reusing a sequence in another spot in the document.
5. Delete all jobs whose names start with a `.`, as these are only used as abstract base classes for inheritance.

## Heuristics

Just flattening the config can be enough to find some errors. Once it's flattened, we can also apply some heuristic rules to catch common problems.

If a job's `rules:` would cause it to not be run under some conditions, that job _does not exist_ for those pipeline executions. If any job declares `needs: [that job]`, the pipeline fails when GitLab tries to create it. Check whether any jobs that depend on it via `needs:` have rules that would run in more cases than the job they depend on. For example: could we be trying to run a `publish` in scenarios where `build` was skipped? GitLab has a particularly obtuse error for this case, [described in this Stack Overflow answer](https://stackoverflow.com/a/67610425). The same applies for `only:`/`except:` and for `rules:if` / `rules:changes` conditions.

A common cause of pipelines hanging is when a job is `when: manual` but not `allow_failure: true`. When this happens, the job sits in `manual` state and the pipeline stays `blocked` until someone clicks the play button — even though the job must pass for the pipeline to succeed.

## Creating gitlab-ci-linter

I started thinking about writing a tool like this about the time I wrote [this post on JS package development](https://brianschiller.com/blog/2024/04/29/js-package-dev.md). There were so many weird tricks and gotchas that I was keeping in my head and I just knew I would forget. When the LLM agents got good enough in late 2025, I threw some credits at the problem and this is what came out of it.

Please take that as a warning: this is completely vibe-coded. I've reviewed the source in a few places, but mostly I've only checked the output against examples.

However, it's been extremely useful for us. One of my coworkers on the platform team said that applying this tool would cover 90% of the reasons his team was asked for help. I hope it can help you too.

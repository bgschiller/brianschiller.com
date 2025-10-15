---
title: Mark Generated Files with gitattributes
date: 2025-10-14
description: Mark files as generated for easier review in Github and Gitlab
category: blog
tags: [git, code]
---

I learned that you can hint to Github and Gitlab's code review interface when files are generated and don't need review. Add the following to the .gitattributes file at the root of your repository

```sh
path/to/generated/** linguist-generated=true gitlab-generated
```

This causes the matching files to be hidden by default in the Pull Request or Merge Request view.

For extra credit, you can also mark the files as read-only (at least in VS-Code and Cursor) so you don't accidentally make edits to them that are blown away the next time the files are re-generated. Add the following to .vscode/settings.json, also in your project root.

```json
{
  "files.readonlyInclude": {
    "path/to/generated/**": true
  }
}
```

There are two situations where I've recently found this helpful:

- I have an Avro schema that is used to generate code in C#, Swift, and TypeScript.
- For complicated reasons, I needed to commit compiled-to-js dist files alongside the source TypeScript.

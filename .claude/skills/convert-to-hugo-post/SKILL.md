---
name: convert-to-hugo-post
description: Use when the user asks to convert a source .md, draft, or notes into a blog post for this site, publish a document as a post, or turn raw markdown into a Hugo post. Skip for edits to already-published posts.
---

# Convert Source Markdown to Hugo Post

## Workflow

1. Read the source file. Classify it: **troubleshooting** (an error or incident and its fix) or **how-to** (a tool or procedure).
2. Read two exemplar posts for tone, matched to genre:
   - Troubleshooting: `content/posts/gitlab-runner-dind-dns-timeout.md`, `content/posts/docker-hub-pulls-failing-ipv6-dns.md`
   - How-to: `content/posts/calculate-lines-of-code-using-cloc.md`, `content/posts/gh-auth-personal-access-token.md`
3. Ask the user (AskUserQuestion) only for details that cannot be derived from the source:
   - Publish date, if there is reason it should not be today
   - Tags, when the topic is ambiguous
   - For troubleshooting posts missing context: what was being done when the problem occurred? Posts are written from first-hand experience ("While running CI jobs through a GitLab Runner, ...")
4. Derive a descriptive title matching existing title patterns, and a kebab-case slug.
5. Rewrite the content per the style recipe below.
6. Output:
   - Source already under `content/posts/`: rewrite it in place, then rename to `<slug>.md` if the filename differs (`git mv` if tracked, `mv` if untracked)
   - Source elsewhere: write `content/posts/<slug>.md` and leave the source untouched
7. Verify the site builds and the post page is generated: from the repo root run `hugo --quiet -F -d <scratch dir>` and check the `<year>/<month>/<slug>/` directory exists in the output. `-F` is required because the 10:00+08:00 convention makes same-day posts future-dated for part of the morning. Do not commit; leave the file for the user to review.

## Style recipe

A post is:

**Front matter** - exactly this shape, nothing more:

```yaml
---
title: 'Descriptive Title Here'
date: YYYY-MM-DDT10:00:00+08:00
draft: false
url: /YYYY/MM/<slug>
tags:
- lowercase
- tags
---
```

Filename, slug, and the final url segment are identical. The url year/month match the date.

**Body** - flowing prose paragraphs alternating with fenced code blocks. No headings of any level. Each command block is introduced by a short plain sentence saying what it does. Inline code for commands, paths, and tool names. Hyphens, never em dashes. No emoji.

**Opening, by genre**:

- Troubleshooting: "While <doing X>, <thing> kept failing with the following error:" then the verbatim error in a code block, then a paragraph explaining the root cause, then the fix.
- How-to: one or two sentences stating what the tool does and the problem it solves, then straight into install and usage.

**Ending** - a verification step or expected outcome ("Image pulls should succeed after the runner restarts."). The post ends when the task is done.

## Common mistakes

- Keeping the source's H1/H2 headings or "Tips"/"Conclusion" sections - fold them into prose
- Inline-array tags `["a", "b"]` - use the YAML list form shown above
- `draft: true`, or a missing `url` field
- Marketing tone ("powerful", "seamless", "handy") - the exemplars are terse and factual

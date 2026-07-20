---
title: 'Authenticating gh with a Personal Access Token'
date: 2026-06-26T10:00:00+08:00
draft: false
url: /2026/06/gh-auth-personal-access-token
tags:
- github
- gh
- wsl
---

While authenticating `gh` in WSL, running `gh auth login` opened the browser within WSL for device authorization. The device code was accepted, but the callback never returned to the CLI - the handshake hung indefinitely.

Using a Personal Access Token (PAT) with `--with-token` sidesteps the browser flow entirely.

Go to `https://github.com/settings/tokens/new` and generate a token. Choose an expiry that suits your needs (90 days is a reasonable default) and select the scopes you need. For general `gh` usage:

- `repo` - full control of private repositories
- `read:org` - read org and team membership
- `gist` - create and manage gists
- `workflow` - update GitHub Actions workflow files

Copy the token immediately - GitHub only shows it once.

Pipe it directly into `gh`:

```bash
echo "YOUR_TOKEN" | gh auth login --with-token
```

Verify the login succeeded:

```bash
gh auth status
```

Expected output:

```
github.com
  Logged in to github.com account YOUR_USERNAME (keyring)
  Active account: true
  Git operations protocol: https
  Token: ghp_****
  Token scopes: gist, read:org, repo, workflow
```

## Updating an expired token

When the PAT expires, `gh` commands start failing auth checks again. Generate a fresh token the same way as before, then refresh the stored credential:

```bash
gh auth refresh
```

This prompts for the new token without logging you out first. If the new token has different scopes than before, pass them explicitly:

```bash
gh auth refresh -h github.com -s repo,workflow,read:org
```

If `gh auth refresh` doesn't pick up the new token, fall back to a clean logout and re-login with `--with-token`, same as the initial setup:

```bash
gh auth logout
echo "YOUR_NEW_TOKEN" | gh auth login --with-token
```

Confirm it worked:

```bash
gh auth status
```

---
title: 'Multiple GitHub Accounts and Repositories'
date: 2023-05-12T14:00:00+08:00
draft: false
url: /2023/05/multiple-github-accounts-and-repositories.html
tags:
- github
cover:
  image: "https://upload.wikimedia.org/wikipedia/commons/c/c2/GitHub_Invertocat_Logo.svg"
  alt: "GitHub"
  relative: false
---

We work in an environment where we have multiple GitHub accounts that is tied to different repositories. It took us a while to figure out how to properly set this up, so that the correct identify is tied to the correct repository.

Let me walk you through an example on how we did it.

Assuming that you have 3 identity, ie. 3 private keys, tied to 3 different GitHub repository.

Here's what our `~/.ssh/config` looks like.

```
Host github.com.one
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_one
Host github.com.two
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_two
Host github.com.three
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_three
```

Let's say, we checked out the following public repository owned by each of the 3 identities.

```
one/repository_one
two/repository_two
three/repository_three
```

Upon checking out each repository, before you can commit to it, you need to update its `.git/config`.

`repository_one/.git/config` looks like this.

```
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
[remote "origin"]
    url = git@github.com:one/repository_one.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
    remote = origin
    merge = refs/heads/main
```

You need to update the `url` to the following.

```
    url = git@github.com.one:one/repository_one.git
```

Also, add `[user]` to `.git/config`.

```
[user]
    name = User One
    email = user_one@one.com
```

When done, `repository_one/.git/config` will look like this.

```
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
[remote "origin"]
    url = git@github.com.one:one/my_repository.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
    remote = origin
    merge = refs/heads/main
[user]
    name = User One
    email = user_one@one.com
```

`repository_two/.git/config` will look like this.

```
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
[remote "origin"]
    url = git@github.com.two:two/my_repository.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
    remote = origin
    merge = refs/heads/main
[user]
    name = User Two
    email = user_two@two.com
```

`repository_three/.git/config` will look like this.

```
[core]
    repositoryformatversion = 0
    filemode = true
    bare = false
    logallrefupdates = true
[remote "origin"]
    url = git@github.com.three:three/my_repository.git
    fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
    remote = origin
    merge = refs/heads/main
[user]
    name = User Three
    email = user_three@three.com
```

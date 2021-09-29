---
layout: post
title: "Cannot git commit using GPG signing"
published: true
mathjax: true
date: "2021-09-02"
category: [Programming]
---


# Cannot git commit using GPG signing

* GPG signing can verify that this git commit is actually the person who claims who he/she is.


- After logout and log back,  `git commit` displayed

```bash
error: gpg failed to sign the data
fatal: failed to write commit object
```

- To solve this I found:

```bash
echo "test" | gpg --clearsign

# Which outputs:
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA256

test
gpg: signing failed: No pinentry
gpg: [stdin]: clear-sign failed: No pinentry
```

- Therefore, I used `which pinentry-mac` to find the path of pinentry, but the path is already in `~/.gnupg/gpg-agent.conf`.
- Solution:

```bash
gpgconf --kill gpg-agent  # Restart gpg-agent
```

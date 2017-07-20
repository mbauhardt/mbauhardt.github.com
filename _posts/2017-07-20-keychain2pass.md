---
layout: post
title: "Keychain2Pass - Migrate Your OSX Keychain To Pass"
tags: [gpg, pass, keychain, password-store]
---

In my [previous blog post](http://mbauhardt.github.io/2017/03/03/a-better-gpg-keychain.html) wrote about a better GPG keychain. I'm using a [yubikey](https://www.yubico.com) to encrypt/decrypt documents.

Why not using the yubikey key for my passwords too?
So i decided to store my passwords no more in my OSX keychain. I wanted to switch from OSX keychain to [pass](http://www.passwordstore.org).

It turns out that the manual migration from the OSX keychain to pass is really time consuming. I was to lazy to it manually. For that reason i wrote a [small programm](https://github.com/mbauhardt/keychain2pass) to to this job.

Give [keychain2pass](https://github.com/mbauhardt/keychain2pass) a try if you also want to switch from the OSX keychain to pass.

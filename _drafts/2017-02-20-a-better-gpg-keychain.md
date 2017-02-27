---
layout: post
title: "A Better GPG Keychain"
tags: [gpg]
---

## Background

My personal goal is to have a GPG keychain i use on more than one machine. I don't want to loose all my signatures in case i lost my key.
After reading the internet i figure it out how can i achive a better GPG keychain.

The approach of a master keypair and a bunch of subkeys will minimize the damage of the worst case (loosing my keypair). So i would like to explain what the benefit of subkeys are and how to generate subkeys.

In general if you have your keypair on your laptop and this laptop is stolen by an attacker then you will revoke your key and publish the revoked key on a keyserver. And that means that you will loose all your signatures you maybe collected the past few years. Subkeys can help you to minimze the damage in case you will loose your key but it will not prevent the attacker to use your stolen private subkey to decrypt messages which was encrypted with the related public key. Also signatures can be done with that stolen key but can not be verified with the related revoked public key.

The concept of subkeys can be explained as

* Generate a keypair which contains __only__ a cerification key.
* Adding three subkeys. A signing key, an encryption key and a authentication key.
* This is your __master keypair__. Put it on a safe place.
* Copy this keypair on your computer whare you want to use it (e.g. your laptop) and use GPG to remove the _certification key_. Only your subkeys remain in this this keyring and this keyring is called __Laptop Keypair__.

The laptop keypair is there to encrypt/decrypt messages, to sign messages and todo SSH logins.

The advantage of this configuration is that in case this keypair is stolen you have only to revoke the subkeys. You will not loose the signatures because the signatures are saved onto the master keypair.



## Generate master keypair

Use `gpg --expert --gen-key`, choose option (8) RSA with own capabilities. Toggle _Sign_ and _Encrypt_ that the current allowed actions are _Ceritify_ only. Save the keyair with keylength 4096 and i choose that the key does not expire. Adding also your name and email to this key.



## Generate Subkeys

Use `gpg --expert --edit-key <KEYID>` to open the menu to be able to edit the key. Use `addkey`, choose option (8) RSA with own capabilities. Toggle _Encryption_ and _Authentication_ to have a key which is there for _Signing_ only. Choose a keylength of _4096_ and let the key expire. I'm using one year and i'm adding every year new subkeys.

Do the same for two more subkeys. But toggle the capabilities to get a key for _Encryption_ and another key for _Authentication_.
At the end you have a keypair which contains your ceritifation key and three subkeys, one for signing, one for encryption and one for authentication.


## Create Revocation Certificate

If you will loose your _master keypair_ the _revocation certificate_ is the only way to communicate that your keypair is stolen and must be ignored. This step is urgent and should not be ignored or skipped.

Use `gpg --output <YOUR_EMAIL>.gpg-revocation-certificate --gen-revoke <YOUR_EMAIL>` and choose a reason for the revocation. Put that revocation certificate also on a safe place.


## Export the whole keypair

First export your private key with `gpg --export-secret-keys --armor <YOUR_EMAIL> > <YOUR_EMAIL>.private.gpg-key`. Aftter that export your public key with `gpg --export --armor <YOUR_EMAIL> > <YOUR_EMAIL>.public.gpg-key`.

Put the two key files, the private and the public, together with the revocation certificate on a safe place. These three files are called your __master keypair__.



## Transform your Master Keypair into a Laptop Keypair

There is only one step you have todo to transform your master keypair into a laptop keyair. You have to remove the private certification key from the whole keypair. In gpg version > `2.0` is much easier or more convenience. This documentation is made for gpg version `2.0.30`
So what you have todo is to export the secret subkeys by executing the command `gpg --export-secret-subkeys <YOUR_EMAIL> > subkeys`. Then delete all secret keys from the keypair by executing `gpg --delete-secret-key <YOUR_EMAIL>` and import the subkeys back with `gpg --import subkeys`. At the end don't forget to delete the previous exported `subkeys` via `rm subkeys` or better `shred --remove subkeys`.
You will see that your private key is marked with `sec#` when using `gpg -K`. The sign `#` is there to mark a secret key as _not available_.



## Revoke Subkeys

In case your laptop keypair is stolen you use your master keypair to revoke your subkeys. Your are doing this by import your private and public key from the master keypair. `gpg --import /path/to/<YOUR_EMAIL>.public.gpg-key /path/to/<YOUR_EMAIL>.private.gpg-key`. `gpg --edit-key <YOUR_EMAIL>`. Select all 3 subkeys via `key 1`, `key 2` and `key 3`. Use `revkey` to revoke the selected subkeys and execute `save` to save the changed keyring. After that upload the revoked keys to a keyserver.


## Smartcard

I want to go one step further. I don't want to leave any foot print on a laptop. So the laptop key is not that ideal goal for me. The idea is to move the generated private subkeys to a smartcard. And copy only the public keys to the laptop. A yubikey satisfy the configuration i want to have.



#### Sources

+ [https://nightsi.de/das-perfekte-gpg-schlusselpaar-generieren](https://nightsi.de/das-perfekte-gpg-schlusselpaar-generieren)
+ [https://alexcabal.com/creating-the-perfect-gpg-keypair/](https://alexcabal.com/creating-the-perfect-gpg-keypair/)
+ [https://www.void.gr/kargig/blog/2013/12/02/creating-a-new-gpg-key-with-subkeys/](https://www.void.gr/kargig/blog/2013/12/02/creating-a-new-gpg-key-with-subkeys/)
+ [https://wiki.debian.org/Subkeys](https://wiki.debian.org/Subkeys)


---
title: "Easiest Way to Encrypt Message"
date: 2019-10-25T15:35:49+01:00
draft: false
tags: ["easiest", "gpg"]
---

Presume User `B` want to pass encrypted message to User `A`.
`B` must have `public key` come from `A`.
So `A` should generat a pair of key, one `public key`, one `private key`.
There will be 2 step in the below. 1. prepare keys. 2. trans message.

## 1. prepare keys

I use Alpine in docker. So just run `apk add gnupg` will install the tool.


Run command below, and then type in `name`, `email`, and others what you want to set.
There should be a `Passphrase` for using `private key`.

```shell
# A
gpg --full-generate-key
```

After generating key, you can show all keys and export `public key` by command below.
The `email` is what you typein in the `gererating key` step.
`--export test1@test.com` means will export the publickey come from `test1@test.com`.
`--armor` means will show result in ASCII.
`--output pubkey.gpg` means store the result in the file `pubkey.gpg`.

```shell
# A
gpg --list-keys
gpg --armor --output pubkey.gpg --export test1@test.com
# pubkey.gpg
```

Now copy `pubkey.gpg` to `B`. And import it in this command.

```shell
# B
gpg --import pubkey.gpg
gpg --list-keys
```

## 2. trans encrypted message

Presume the message stored in file `msg.file`.
We can use the command to entrypt it to a new file `msg.file.asc`.
`-r test1@test.com` means the message will be sent to `test1@test.com`,
it also mean will use the `public key` come from `test1@test.com` to encrypt the message.
`-e msg.file` means read the mesage from the file `msg.file`. If you leave `-e` with empty value, application will read from stdin. the output will also print to stdout.

```shell
# B
gpg --armor -r test1@test.com -e msg.file
# echo "hello" | gpg --armor -r test1@test.com -e
```

Use command below to decrypt message at `A` side. the final message will print to stdout.

```shell
# A
gpg -d msg.file.asc
# echo $ENCRYPTMSG | gpg -d
```

There is no content talk about store `private key` for a long time. May be there will be in the future.
# secrets

We have a number of secrets necessary for development and testing across several
product-os products and therefore repositories. We use [`git secret`](https://git-secret.io/)
to manage keys and encrypt/decrypt these secret files.

## Adding a key

Example of granting a new key access to our secrets. This script combines `git secret` and `git` commands
as described on [git secret](https://git-secret.io/) section "Usage: Adding someone to a repository using git-secret"

First we need the public key. In this example we get if from the github profile, but can also be
exported with `gpg --armor --export foo@bar.com`.

The `git secret hide` re-encrypts the files giving access to the newly added user.


```
curl https://github.com/foobar.gpg | gpg --import
cd git/product-os/secrets
git checkout -b add-foobar
git secret reveal -f
git secret tell foo@bar.com
git secret hide
git add --all
git commit -m "Grant access to foobar"
git push origin add-foobar
```

## Removing a key
Example of revoking access:
```
cd git/product-os/secrets
git checkout -b remove-foobar
git secret reveal -f
git secret removeperson foo@bar.com
git secret hide
git add --all
git commit -m "Revoke foobar access"
git push origin remove-foobar
```

## Adding a secret
Example of adding a new secret:
```
cd git/product-os/secrets
git checkout add-secret
git secret reveal -f
cp ~/some_token .balena/secrets/
git secret add .balena/secrets/some_token
vim .gitignore # ensure unencrypted version of secret is ignored
git secret hide
git add --all
git commit -m "Add new secret"
git push --force-with-lease origin
```

## Removing a secret
Example of removing a secret:
```
cd git/product-os/secrets
git checkout remove-secret
git secret reveal -f
git secret remove -c .balena/secrets/some_token
rm .balena/secrets/some_token
vim .gitignore # remove secret to keep things tidy
git secret hide
git add --all
git commit -m "Remove old secret"
git push --force-with-lease origin
```

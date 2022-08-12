# secrets

We have a number of secrets necessary for development and testing across several
product-os products and therefore repositories. We use [`git secret`](https://git-secret.io/)
to manage keys and encrypt/decrypt these secret files.

## Adding a key
Example of granting a new key access to our secrets:
```
curl https://github.com/foobar.gpg | gpg --import
cd git/product-os/secrets
git checkout add-foobar
git secret reveal -f
git secret tell foo@bar.com
git secret hide
git add --all
git commit -m "Grant access to foobar"
git push --force-with-lease origin
```

## Removing a key
Example of revoking access:
```
cd git/product-os/secrets
git checkout remove-foobar
git secret reveal -f
git secret removeperson foo@bar.com
git secret hide
git add --all
git commit -m "Revoke foobar access"
git push --force-with-lease origin
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

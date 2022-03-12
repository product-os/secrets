# secrets

We have a number of secrets necessary for development and testing across several
product-os products and therefore repositories. We use [`git secret`](https://git-secret.io/)
to manage keys and encrypt/decrypt these secret files. We also use a GitHub action
to sync updated files and `git secret` metadata out to other repositories anytime
these files are modified.

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

Once the new PR is merged, shepherd through any sync PRs automatically opened
by the sync GitHub action. The list of repositories to watch are listed in `.github/sync.yml`.

## Removing a key
Example of revoking access:
```
cd git/product-os/secrets
git checkout remove-foobar
git secret reveal -f
git secret killperson foo@bar.com
git secret hide
git add --all
git commit -m "Revoke foobar access"
git push --force-with-lease origin
```

Like when adding a key, keep an eye out for sync PRs opened up on sync target repos.
Also, note that the official `git secret` docs use `git secret removeperson` instead
of `git secret killperson` so you may need to check which command is available with
your installation.

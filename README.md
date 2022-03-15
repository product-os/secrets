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

Once this PR is merged, the sync GitHub action will open PRs for all repositories
listed in `.github/sync.yml`. In those PRs, you will need to ensure that the
unencrypted version of the new secret is added to the `.gitignore` file in that repo.
If this step is missed, a developer could accidentally push and expose the
unencrypted secret in a subsequent push.

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

Once this PR is merged, the sync GitHub action will open PRs for all repositories
listed in `.github/sync.yml`. In those PRs, you should remove the secret from the
`.gitignore` file in that repo to keep things tidy.

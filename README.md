# rsync deployments

This GitHub Action deploys *everything* in `GITHUB_WORKSPACE` to a folder on a server via rsync over ssh. 

This action would usually follow a build/test action which leaves deployable code in `GITHUB_WORKSPACE`.

# Required secrets

This action needs a `DEPLOY_KEY` secret variable. This should be the private key part of an ssh key pair. The public key part should be added to the authorized_keys file on the server that receives the deployment.

# Required inputs

This action requires six inputs:

1. `FLAGS` for any initial/required rsync flags, eg: `-avzr --delete`

2. `EXCLUDES` for any `--exclude` flags and directory pairs, eg: `--exclude .htaccess --exclude /uploads/`. Use `""` if none required.

3. `USER` for the deployment target, and should be in the format: `deploybot`

4. `HOST` for the server user, eg: `myserver.com`

5. `LOCALPATH` for the local path to sync, eg: `/src`

5. `REMOTEPATH` for the remote path to sync, eg: `/srv/myapp/public/htdocs/`

# Example usage

```
name: Deploy to production

on:
  push:
    branches:
      - master

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: contention/rsync-deployments@v2.0.0
        with:
          FLAGS: -avzr --delete
          EXCLUDES: --exclude .htaccess
          USER deploybot
          HOST: myserver.com
          LOCALPATH: /dist
          DEST: /srv/myapp/public/htdocs/
        env:
          DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}

```

## REMINDER! 

Check your keys. Check your deployment paths. Check your flags. And use at your own risk.

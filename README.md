# rsync deployments GitHub Action

This GitHub Action deploys your GitHub Action Workspace, totally or partially, to a remote server via rsync over ssh. 

This action would usually follow a build/test action which leaves deployable code in `GITHUB_WORKSPACE`.

## Example usage

```
name: Checkout repo master branch and deploy to production

on:
  push:
    branches:
      - master

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@master
      - uses: contention/rsync-deployments@master
        with:
          USER_AND_HOST: user@example.com
          DEST: /path/to/target
        env:
          DEPLOY_KEY: ${{ secrets.SSH_PRIVATE_KEY }} 
```

## Inputs

### USER_AND_HOST

**Mandatory**. Deployment user and host, and should be in the format: `[USER]@[HOST]`

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - # ...
      - uses: contention/rsync-deployments@master
        with:
          # ...
          USER_AND_HOST: user@domain.tld
```

### DEST

**Mandatory**. Deployment destination path.


```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - # ...
      - uses: contention/rsync-deployments@master
        with:
          # ...
          DEST: /path/to/target
```

### SRC

_Optional_. Change this to deploy a smaller subset of your [GitHub workspace](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/using-environment-variables#default-environment-variables). Any value understood by `rsync` is accepted. By default, the entire workspace is deployed.

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - # ...
      - uses: contention/rsync-deployments@master
        with:
          # ...
          SRC: _site/
```

### RSYNC_OPTIONS

_Optional_. Any initial/required rsync flags, as found in the _Options Summary_ section of [rsync manual](https://linux.die.net/man/1/rsync). 

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - # ...
      - uses: contention/rsync-deployments@master
        with:
          # ...
          RSYNC_OPTIONS: -avzr --delete --exclude node_modules --exclude '.git*'
```

## Required SECRET

This action needs a `DEPLOY_KEY` secret variable. This should be the private key part of an ssh key pair. The public key part should be added to the authorized_keys file on the server that receives the deployment.

```yaml
jobs:
  # ...
  deploy:
    runs-on: ubuntu-latest
    steps:
      - # ...
      - uses: contention/rsync-deployments@master
        with:
          # ...
        env:
          DEPLOY_KEY: ${{ secrets.SSH_PRIVATE_KEY }}
```

In this example, it is expected you create a new repo `Settings › Secrets` named `SSH_PRIVATE_KEY`, with the content of a private key, with access to the remote host you want to deploy to.


## Disclaimer

If you're using GitHub Actions, you'll probably already know that it's still in limited public beta, and GitHub advise against using Actions in production. 

So, check your keys. Check your deployment paths. And use at your own risk.

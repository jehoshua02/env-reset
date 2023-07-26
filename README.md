# Env Reset

Scripts I use to reset my environment, which relies on many repos, docker compose, and some custom scripts.

## Setup

1. Clone this repo somewhere
2. Add `bin` to your path in your shell config file (example: .zshrc)
```
PATH=$PATH:/path/to/env-reset/bin
```
3. Copy `.env.example` to `.env` and customize values

## Configuration

- `CONFIGURE_CMD`: A command you want to run during `env-reset`, within `REPOS_PATH`, to do any configuration special to your environment (example: `./bin/init`).
- `REPOS_PATH`: The folder path containing all your git repositories. Used by `env-reset`. It is assumed the top of this path contains a `docker-compose.yml`.

## Commands

### `env-reset`

Does everything it can to reset your environment to a clean slate with everything up to date.

```
env-reset
```

What it does:

- Stops and removes docker compose containers. Assumes `docker-compose.yml` lives at the top of your `REPOS_PATH`.
- Prunes docker images, so all new images get pulled on next up.
- Checks out main branch in all repos under `REPOS_PATH`. 
- Pulls all repos under `REPOS_PATH`. 
- Runs `CONFIGURE_CMD`. 
- Prunes branches in all repos under `REPOS_PATH`.

After an `env-reset` you then `docker compose up -d <whatever-service-you-want>`, which will pull new images since all of them were pruned, and volume mounts will be fresh, since all repos were updated, and there should be no configuration to do since `CONFIGURE_CMD` was executed.

So in a nutshell, `env-reset` and `docker compose up -d <service>` should have you in a fresh, fully updated, working state every time.

### `repos`

Runs any command you want in all repos under `$PWD`. Used heavily by `env-reset` to perform repo related tasks.

```
repos <command>
```

The `env-reset` command doesn't handle untracked changes in repos. It's up to you to manually resolve any untracked changes in repos. 

The `repos` command combined with `grep` can be useful for finding repos with untracked changes.

```
repos | grep "changes: true"
```

You can also use the `repos` command to look for repos on branches.

```
repos | grep -v "main"
```

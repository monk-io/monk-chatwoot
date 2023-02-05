# Chatwoot & Monk

This repository contains Monk.io template to deploy Chatwoot & Monk either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

## Prerequisites

- [Install Monk](https://docs.monk.io/docs/get-monk)
- [Register and Login Monk](https://docs.monk.io/docs/acc-and-auth)
- [Add Cloud Provider](https://docs.monk.io/docs/cloud-provider)
- [Add Instance](https://docs.monk.io/docs/multi-cloud)

### Make sure monkd is running

```bash
foo@bar:~$ monk status
daemon: ready
auth: logged in
not connected to cluster
```

## Clone Repository

```bash
git clone https://github.com/monk-io/chatwoot
```

## Load Template

```bash
cd chatwoot
monk load MANIFEST
```

### Let's take a look at the themes I have installed

```bash
foo@bar:~$ monk list chatwoot
✔ Got the list
Type      Template                      Repository  Version  Tags
runnable  chatwoot/chatwoot-db     local       -        -
runnable  chatwoot/chatwoot-rails  local       -        -
runnable  chatwoot/chatwoot-rds    local       -        -
group     chatwoot/stack           local       -        -
```

## Deploy Stack

```bash
foo@bar:~$ monk run chatwoot/stack
? Select tag to run [local/chatwoot/stack] on: monk
✔ Starting the job: local/chatwoot/stack... DONE
✔ Preparing nodes DONE
✔ Checking/pulling images...
✔ [================================================] 100% chatwoot/chatwoot:latest monk-1
✔ [================================================] 100% redis:latest monk-2
✔ [================================================] 100% postgres:latest monk-1
✔ Checking/pulling images DONE
✔ Started local/chatwoot/stack

🔩 templates/local/chatwoot/stack
 ├─🧊 Peer monk-1
 │  ├─🔩 templates/local/chatwoot/chatwoot-db
 │  │  └─📦 9c5c2c7ed9087d7008ffafe8c988b9d4-l-chatwoot-chatwoot-db-db
 │  │     └─🧩 postgres:latest
 │  └─🔩 templates/local/chatwoot/chatwoot-rails
 │     └─📦 957bf05f242e228aa74f518941c82f9d--chatwoot-chatwoot-rails-rails
 │        ├─🧩 chatwoot/chatwoot:latest
 │        └─🔌 open 13.48.58.56:3000 (127.0.0.1:3000) -> 3000
 └─🧊 Peer monk-2
    └─🔩 templates/local/chatwoot/chatwoot-rds
       └─📦 2ecfc2c225f552b1de79290f252dbff8-nk-chatwoot-chatwoot-rds-redis
          └─🧩 redis:latest

💡 You can inspect and manage your above stack with these commands:
 monk logs (-f) local/chatwoot/stack - Inspect logs
 monk shell     local/chatwoot/stack - Connect to the container's shell
 monk do        local/chatwoot/stack/action_name - Run defined action (if exists)
💡 Check monk help for more!
```

## Check web gui

`http://13.48.58.56:3000/`

## Setup Database

```bash
monk do chatwoot/chatwoot-rails/prepare-db
```

## Variables

The variables are in `stack.yml` file. You can quickly setup by editing the values here.

## Stop, remove and clean up workloads and templates

```bash
monk purge chatwoot
```

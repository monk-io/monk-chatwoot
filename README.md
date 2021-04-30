# Chatwoot & Monk

This repository contains Monk.io template to deploy Chatwoot system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

## Start 

Setup Monk - https://docs.monk.io/monk-in-10/

Start `monkd -d` and login.

```bash
monk login --email=<email> --password=<password>
```

## Local Deployment

This template is available directly from Monkhub.io therefore if you want a quick deploy simply run below command after launching `monkd`:
```bash
➜  monk list chatwoot
✔ Got the list
Type      Template           Repository  Version  Tags
runnable  chatwoot/chatwoot  monk        latest   self hosted, messaging, communication
runnable  chatwoot/mailhog   monk        latest   smtp, email
runnable  chatwoot/postgres  monk        12       dataops, database
runnable  chatwoot/redis     monk        alpine   -
runnable  chatwoot/sidekiq   monk        latest   self hosted, messaging, communication
group     chatwoot/stack     monk        latest   self hosted, messaging, communication

➜  monk run chatwoot/stack

✔ Started chatwoot2/stack

✨ All done! 

🔩 chatwoot2/stack
 └─🧊 local 
    ├─📦 templates-local-chatwoot2-redis-redis       
    │  ├─🧩 docker.io/redis:alpine                
    │  ├─💾 /Users/toymachine/.monk/volumes/redis -> /data
    │  └─🔌 open localhost:6379 -> 6379
    ├─📦 templates-local-chatwoot2-sidekiq-rails     
    │  └─🧩 chatwoot/chatwoot:latest
    ├─📦 templates-local-chatwoot2-mailhog-mailhog   
    │  ├─🧩 mailhog/mailhog:latest            
    │  ├─🔌 open localhost:1025 (0.0.0.0:1025) -> 1025
    │  └─🔌 open localhost:8025 (0.0.0.0:8025) -> 8025
    ├─📦 templates-local-chatwoot2-postgres-postgres 
    │  ├─🧩 postgres:12                                                
    │  ├─💾 /Users/toymachine/.monk/volumes/db_data -> /var/lib/postgresql/data
    │  └─🔌 open localhost:5432 (0.0.0.0:5432) -> 5432
    └─📦 templates-local-chatwoot2-chatwoot-rails    
       ├─🧩 chatwoot/chatwoot:latest          
       └─🔌 open localhost:3000 (0.0.0.0:3000) -> 3000
```

## Cloud Deployment
To deploy the above system to your cloud provider, create a new Monk cluster and provision your instances.
```bash
➜  monk cluster new                                     
? New cluster name chatwoot-deployment
✔ Cluster created
Your cluster has been created successfully.

➜  monk cluster provider add
? Cloud provider gcp
? GOOGLE_APPLICATION_CREDENTIALS is set. Try load it? Yes
✔ Provider added successfully

➜  monk cluster grow -p gcp
? Name for the new instance my-instance
? Tags (split by whitespace) chatwoot
? Instance region (gcp) europe-west2
? Instance zone (gcp) europe-west2-c
? Instance type (gcp) n2-standard-2
? Disk Size (GBs) 10
? Number of instances (or press ENTER to use default = 1) 2
✔ Creating a new instance(s) on gcp... 
✔ Syncing nodes DONE
✔ Cluster grown successfully
```
Once cluster is ready execute the same command as for local and select your cluster (the option will appear automatically).
```bash
➜  monk run chatwoot/stack
? Select tag to run on: chatwoot
```

## Configuration
You can add/remove or override current configuration of the template or create a brand new one which could inherit the components that you require.

The current variables can be found in `chatwoot/stack/variables` section
```bash
  variables:
    defines: variables
    proxy-pass-protocol: http
    chatwoot-port: 3000
    server-name: chat.yoursite.io
    email: chat@yoursite.io
    ssl: true
    frontend-url:
      env: FRONTEND_URL
      value: http://0.0.0.0:3000
    rails-env:
      env: RAILS_ENV
      value: production
    secret-key-base:
      env: SECRET_KEY_BASE
      value: 3bsefe45bc938475ecba45075c53cdf0f94299a23f821b05beaf7955b4b0c60ff2b0c66c9047a026578deb5ecadabgs342891be2be68ed123a7b26876d4daddf
    db-user:
      env: POSTGRES_USERNAME
      value: postgres
    db-password:
      env: POSTGRES_PASSWORD
      value: "CHANGE_ME"
    db-port: 5432
    smtp-username:
      env: SMTP_USERNAME
      value: "chat@yoursite.io"
    smtp-password:
      env: SMTP_PASSWORD
      value: "CHANGE_ME"
    smtp-port:
      env: SMTP_PORT
      value: 1025
    smtp-address:
      env: SMTP_ADDRESS
      value: "SMTP_HOST"
    redis-password:
      env: REDIS_PASSWORD
      value: "CHANGE_ME"
    sidekiq-auth-username:
      env: SIDEKIQ_AUTH_USERNAME
      value: "chat@yoursite.io"
    sidekiq-auth-password:
      env: SIDEKIQ_AUTH_PASSWORD
      value: "CHANGE_ME"
```
To override the settings or add new ones for your own template you can inherit it in this way:
```bash
namespace: my-chatwoot

chatwoot-prod:
  defines: process-group
  inherits: chatwoot/stack
  variables:
    postgres-db-user: postgres
    postgres-db-password: my-chatwoot-postgres-pass
    smtp-username: user-1234
    smtp-password: secret-pass
    smtp-port: 2525
    smtp-address: smtp.my-domain.io
    redis-password: my-chatwoot-redis-pass
    sidekiq-auth-username: my-chatwoot-sidekiq-auth-username
    sidekiq-auth-password: my-chatwoot-sidekiq-auth-password
    domain: chat.my-domain.io
    domain-email: chat@my-domain.io
    mailer-sender-email: chat@my-domain.io

```

### Logs & Shell
```bash
➜  monk logs -l 1000 -f chatwoot/chatwoot

➜  monk shell chatwoot/chatwoot
```

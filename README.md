# Chatwoot & Monk

This repository contains Monk.io template to deploy Chatwoot system either locally or on cloud of your choice (AWS, GCP, Azure, Digital Ocean).

## Local Deployment

This template is available directly from Monkhub.io therefore if you want a quick deploy simply run below command after launching `monkd`:
```
➜  monk-chatwoot git:(main) ✗ monk run chatwoot/stack

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
```
➜  monk-chatwoot git:(main) ✗ monk cluster new                                     
? New cluster name chatwoot-deployment
✔ Cluster created
Your cluster has been created successfully.

➜  monk-chatwoot git:(main) ✗ monk cluster provider add
? Cloud provider gcp
? GOOGLE_APPLICATION_CREDENTIALS is set. Try load it? Yes
✔ Provider added successfully

➜  monk-chatwoot git:(main) ✗ monk cluster grow -p gcp
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
```
➜  monk-chatwoot git:(main) ✗ monk run chatwoot/stack
? Select tag to run on: chatwoot
```

## Configuration
You can add/remove or override current configuration of the template or create a brand new one which could inherit the components that you require.

The current variables can be found in `chatwoot/common/variables` and `chatwoot/postgres` section
```
  variables:
    defines: variables
    secret-key-base: 3beeee45bc938475ecba45075c53aae0f94299a83f824b25bbaf7965b4b0c60ff2b0c66c9047a026578deb5ecadabaa602891be2be66ed123a7b26876d4daddf
    db-user: postgres
    db-password: password
    db-port: 5432
    smtp-user: ""
    smtp-password: ""
    smtp-port: 1025

postgres:
  defines: runnable
  inherits: /postgres/12
  variables:
    db-name: chatwoot_production
    db-user: postgres
    db-password: password
    db-port: 5432
```
To override the settings or add new ones for your own template you can inherit it in this way:
```
my-chatwoot:
  defines: runnable
  inherits: /chatwoot/chatwoot
  variables:
    db-user: my-postgres-user
```

### Logs & Shell
```
➜  monk logs -l 1000 -f chatwoot/chatwoot

➜  monk shell chatwoot/chatwoot
```

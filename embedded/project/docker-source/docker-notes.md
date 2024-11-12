# Notes for GSRS Docker embedded deployment

## Terminal Environment

```
export DOCKER_SOURCE=../../docker-source
export HOST_VOLUMES=../../volumes

export DB_TEST_USERNAME=root
export DB_TEST_PASSWORD=yourpassword

# export RELEASE_MODE=development
export RELEASE_MODE=public
```


## gsrs-ci

Below gsrs-ci refers to the deployments folder used by FDA. but you may use any similar deployment repository such as gsrs-example-deployment, or gsrs3-main-deployment.  

## Clone gsrs-ci

```
# Temporarily clone your gsrs-ci repo here
cd gsrs3-docker-deployments/projects
```

## Running the containers

First you'll need to build your images (see below)

```
cd gsrs-ci
docker-compose -f ../docker-source/docker-compose.yml up postgresql frontend gateway substances clinical-trials 
```

## Check environment variables

See if environment variables are interpolated as expected.

```
docker-compose -f ../docker-source/docker-compose.yml config
```

## Building images

You may want to run with `--build-arg RELEASE_MODE=$RELEASE_MODE`

```
# substances
docker build -f $DOCKER_SOURCE/substances/Dockerfile --no-cache --progress=plain  --build-arg BUILD_VERSION=v2023.0714.1 -t  gsrs3/gsrs-emb-docker-substances:0.0.1-SNAPSHOT .

# gateway
docker build -f $DOCKER_SOURCE/gateway/Dockerfile --no-cache --progress=plain --build-arg BUILD_VERSION=v2023.0714.1 -t  gsrs3/gsrs-emb-docker-gateway:0.0.1-SNAPSHOT .

# frontend
docker build -f $DOCKER_SOURCE/frontend/Dockerfile --no-cache --progress=plain --build-arg BUILD_VERSION=v2023.0714.1 -t  gsrs3/gsrs-emb-docker-frontend:0.0.1-SNAPSHOT .

# adverse-events
docker build -f $DOCKER_SOURCE/adverse-events/Dockerfile --no-cache --progress=plain --build-arg BUILD_VERSION=v2023.0714.1 -t  gsrs3/gsrs-emb-docker-adverse-events:0.0.1-SNAPSHOT .

# applications
docker build -f $DOCKER_SOURCE/applications/Dockerfile --no-cache --progress=plain --build-arg BUILD_VERSION=v2023.0714.1 -t  gsrs3/gsrs-emb-docker-applications:0.0.1-SNAPSHOT .

# clinical-trials
docker build -f $DOCKER_SOURCE/clinical-trials/Dockerfile --no-cache --progress=plain --build-arg BUILD_VERSION=v2023.0714.1 -t  gsrs3/gsrs-emb-docker-clinical-trials:0.0.1-SNAPSHOT .

# impurities
docker build -f $DOCKER_SOURCE/impurities/Dockerfile --no-cache --progress=plain --build-arg BUILD_VERSION=v2023.0714.1 -t  gsrs3/gsrs-emb-docker-impurities:0.0.1-SNAPSHOT .

# invitro
docker build -f $DOCKER_SOURCE/invitro-pharmacology/Dockerfile --no-cache --progress=plain --build-arg BUILD_VERSION=v2023.0714.1 -t  gsrs3/gsrs-emb-docker-invitro-pharmacology:0.0.1-SNAPSHOT .

# products
docker build -f $DOCKER_SOURCE/products/Dockerfile --no-cache --progress=plain --build-arg BUILD_VERSION=v2023.0714.1 -t  gsrs3/gsrs-emb-docker-products:0.0.1-SNAPSHOT .

# ssg4m
docker build -f $DOCKER_SOURCE/ssg4m/Dockerfile --no-cache --progress=plain --build-arg BUILD_VERSION=v2023.0714.1 -t  gsrs3/gsrs-emb-docker-ssg4m:0.0.1-SNAPSHOT .
```

## Clean up indexes

Before committing to Git, clean up folders from test instances

rm -r ./volumes/app-data/adverse-events/ginas.ix
rm -r ./volumes/app-data/applications/ginas.ix
rm -r ./volumes/app-data/clinical-trials/ginas.ix
rm -r ./volumes/app-data/impurities/ginas.ix
rm -r ./volumes/app-data/invitro-pharmacology/ginas.ix
rm -r ./volumes/app-data/substances/ginas.ix

## Wipe databases

rm -r ./volumes/app-data/db/mariadb/info && mkdir -p ./volumes/app-data/db/mariadb/info
rm -r ./volumes/app-data/db/postgresql/info && mkdir -p ./volumes/app-data/db/postgresql/info
rm -r ./volumes/app-data/db/mysql/info && mkdir -p ./volumes/app-data/db/mysql/info

## Find more files to exclude from commits or clean up

```
find . -type f  | grep -v app-data/db | grep -v 'frontend/classes'  | grep -v gsrs-ci
```

# Backup configuration files
```
tar -cvzf flavor.env-db.conf.tar.gz  $(find volumes -type f -name  "*env-db.conf")
tar -cvzf backup.all.conf.tar.gz  $(find volumes -name conf -type d) 
```
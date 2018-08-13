# dev-microstack

[![Build Status](https://travis-ci.org/saintech/dev-microstack.svg?branch=master)](https://travis-ci.org/saintech/dev-microstack)

:construction: **currently in early state** :construction:

## Prerequisites

Before you begin, you'll need:

1. Domain with subdomains: **dev**, **ci**, **registry**, **docker**, that resolves to you server IP with **docker** installed. Ports **80** and **443** should be exposable.
2. Git repository on one of the [hostings supported](http://docs.drone.io/install-for-github/) by Drone CI. Integration for Drone CI should be enabled.

## Instructions

```sh
export DOMAIN=<your domain>
export LETSENCRYPT_EMAIL=<your email>

export DRONE_ADMIN=<your username on git hosting>
export DRONE_BITBUCKET=true #for example
export DRONE_BITBUCKET_CLIENT=<...>
export DRONE_BITBUCKET_SECRET=<...>

export COMPOSE_PROJECT_NAME=devops
export AUTOMATE_REPO_PASS=$(</dev/urandom tr -dc A-Za-z0-9-_ | head -c 16)
export DRONE_SECRET=$(</dev/urandom tr -dc A-Za-z0-9-_ | head -c 16)

docker pull saintech/dev-microstack-nginx
docker tag saintech/dev-microstack-nginx registry.${DOMAIN}/nginx
wget -q -O- https://github.com/saintech/dev-microstack/raw/master/docker-compose.yml | docker-compose -f - run --rm --entrypoint htpasswd -v devops_htpasswd:/etc/htpasswd registry -Bcb /etc/htpasswd/.htpasswd automate ${AUTOMATE_REPO_PASS}
# wget -q -O- https://github.com/saintech/dev-microstack/raw/master/docker-compose.yml | docker-compose -f - run --rm --entrypoint htpasswd -v devops_htpasswd:/etc/htpasswd registry -Bb /etc/htpasswd/.htpasswd ${DRONE_ADMIN} my_pass
wget -q -O- https://github.com/saintech/dev-microstack/raw/master/docker-compose.yml | docker-compose -f - up -d
docker-compose logs -f
```

* in ci.$DOMAIN turn on repository for nginx (you can clone this)
* add `$AUTOMATE_REPO_PASS` secret for automate user for the repository
* make your first push to the repository
* in portainer.$DOMAIN add registry.$DOMAIN

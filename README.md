# traefik-docker-socket-proxy-docker-compose-example

After hours of trial and error have arrived at a working [Traefik v2.2](https://docs.traefik.io/) + [Tecnativa/docker-socket-proxy](https://github.com/Tecnativa/docker-socket-proxy) + HTTPS with Let's Encrypt + Automatic Redirect for all HTTP requests to HTTPS + Docker Compose. This was tested on a $5 Digital Ocean droplet running  [Docker 1-Click App(Inc. Ubuntu 18.04 + Docker)](https://marketplace.digitalocean.com/apps/docker)
### Usage
1. Clone the repo to your server
```bash
git clone https://github.com/PythonicAccountant/traefik-docker-socket-proxy-docker-compose-example.git
```
2. Update the .env file with your email and domain name (or update the Docker-Compose file with your own info)
```bash
vim .env
```
```
ACME_EMAIL= Email address you want to use for your Let's Encrypt certificates
TRAE_DOMAIN_NAME= Domain name attached to your server
```
3. Create Docker network for Traefik to talk to the other containers behind it 
```bash
docker network create web_external
```
4. Start it up
```bash
docker-compose -up -d
```
5. Make sure the correct labels are applied to downstream containers. I've included an example with a modifed version of the WhoAmI Example from the Traefik Docs.

### Why?

I am in the process of teaching myself how to program in Python and use Django to create web applications. During this learning process I have started multiple projects but they haven't seen the light of day because I didn't know how to deploy them. So for my most recent learning project I have decided to learn more about deployment before continuing my Django journey. 

This led me to Docker and Docker-Compose which I've been learning for a few weeks now, which turned into buying a domain name, which turned into I want to deploy multiple apps as subdomains on my new domain and not have to pay for multiple droplets to host hobby/learning projects that no one but me will use just yet. This then lead me to Traefik which was easy to initially launch using the [Basic WhoAmI example](https://docs.traefik.io/user-guides/docker-compose/basic-example/) on the Traefik docs.

Right after getting this basic example up and running I made my way over to the [Docker provider specifc documentation](https://docs.traefik.io/providers/docker/) where I saw this security note:

> Accessing the Docker API without any restriction is a security concern: If Traefik is attacked, then the attacker might get access to the underlying host.

After a bit of googling I landed on this line from the [Basic WhoAmI example](https://docs.traefik.io/user-guides/docker-compose/basic-example/) being the no bueno part.

```
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
```

Per my googling, the :RO(read-only) part wouldn't actually prevent a bad actor from doing bad things.

Luckily the docs have some solutions! One of those solutions is [Tecnativa/docker-socket-proxy](https://github.com/Tecnativa/docker-socket-proxy). This [medium article](https://medium.com/@containeroo/traefik-2-0-paranoid-about-mounting-var-run-docker-sock-22da9cb3e78c) got me up and running with a docker-compose file for the docker-socket-proxy part, but I couldn't really find any good examples of getting it up and running together with Traefik so had to do some trial and error and sporadic googling. This repo is the result of that process.

#### Disclaimer
If you haven't gathered by my username and the above wall of text, I am in no way a professional programmer so take this all with a grain of salt. Please raise an issue or PR if there's something that could be better. 

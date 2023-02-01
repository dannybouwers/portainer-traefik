# Portainer with Traefik Proxy and Let's Encrypt via Cloudflare

You can use this compose file to deploy Portainer Enterprise Edition with a readable url and https. Traefik is used as a reverse proxy and Cloudflare DNS to acquire a Let's Encrypt certificate.

## Result

Once deployed, Portainer can be reached via https://gui.example.com. The Traefik dashboard can be reached via https://traefik.example.com. All http (unencrypted) traffic will de redirected to https.

## Get Cloudflare API key

Traefik can automatically obtain and renew a Let's Encrypt certificate to enable secure HTTPS traffic. By using the DNS-01 challenge there is no need to open port 80 to your server. If you only want to use Portainer locally, you can even omit opening ports and configuring public DNS at all (this requires [Split-horizon DNS](https://en.wikipedia.org/wiki/Split-horizon_DNS)).

To generate a Cloudflare token with the right privileges:
1. Navigate to https://dash.cloudflare.com/profile/api-tokens
1. Hit *Create Token*
1. Hit *Use template* on the line "Edit zone DNS"
1. Below "Zone Resources*, select the domain you are using
1. Add your public IP below "Client IP Address Filtering" and set the "operator" to *Is in*
1. Hit *Continue to summary* > *Create token*
1. Copy and save the token displayed on this screen. It will only be provided once! You will need it during setup.

## Setup and run

1. Copy the file ```docker-compose.yml``` to your working directory.
1. Create a file called ```.env``` (```touch ./.env```) and add a value for PROXY_DOMAIN and LE_EMAIL (see ```.env.example``` for details)
1. Create the directory and files for Traefik to run:
    ```shell
    mkdir -p ./traefik
    touch -a ./traefik/traefik.log
    touch -a ./traefik/acme.json
    chmod 600 ./traefik/acme.json
    ```
1. Store your Cloudflare API key as a secret:
    ```shell
    echo 'your-secret-key' > ./secrets/cloudflare_api_token
    ```
1. Start the containers using Docker Compose:
    ```shell
    docker compose up -d
    ```
1. Open your favorite browser, navigate to https://gui.PROXY_DOMAIN and follow the instructions to configure Portainer

## Portainer Community Edition
If you would like to run the free community edition of Portainer, change ```portainer/portainer-ee``` on line 63 of ```docker-compose.yml``` to ```portainer/portainer-ce```.
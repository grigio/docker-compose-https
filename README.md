

# Wildcard self-signed root certificate with Trafik and Docker

The purpose is to a have a valid lan (local network) with docker apps and a valid HTTPS certificate, easy to maintain without depending on external services like cloudflare, external dns, etc..

```
Example
https://portainer.example.lo
https://traefik.example.lo
https://bitwarden.example.lo

tested with
Docker version 20.10.12, build e91ed5707e
Docker Compose version 2.2.3

https://docs.docker.com/compose/cli-command/#install-on-linux
...
````

## STEP 1: local DNS via Home Assistant or /etc/hosts

```
sudo nano /etc/hosts
127.0.0.1  localhost bitwarden.example.lo homeassistant.example.lo
192.168.1.6 graphana.example.lo
```

To have `*.example.lo` to resolve to a certain ip you have to use a local DNS (like Adguard DNS rewrites)

NB: in Android/iOS if you can't specify your local DNS in your LAN you can use a VPN via Wireguard and force it via VPN.

Create `.env` , see `env.example`

## STEP 2: generation wildcard self-signed rootCA certificate (every N months)

**INSTALL mkcert** https://mkcert.dev

Save the specific command in `certs/generate_certs.sh` for convenience

```
mkcert -key-file wildcard_example.com.key -cert-file wildcard_example.com.cer example.lo *.example.lo
```

## STEP 3: installation wildcard self-signed rootCA certificate (on every device)

**INSTALL your self-signed Certification Authority RootCA.pem for your OS** https://wiki.archlinux.org/title/User:Grawity/Adding_a_trusted_CA_certificate#System-wide_%E2%80%93_Arch,_Fedora_(p11-kit)

```
# example
sudo trust anchor --store rootCA.pem
# or / sudo cp ~/.local/share/mkcert/rootCA.pem /usr/local/share/ca-certificates/traefiklocal/rootCA.pem
```

## STEP 4: initialization network
```
docker network create traefiknet
```

## RUN the `docker-compose.yml`
```
docker compose up # -d / for background
docker compose down # remove the stack
```

## Enjoy your webapps with the green lock :)
![traefik-selfsigned-bitwarden](https://user-images.githubusercontent.com/8074/158024211-98ff5390-19e8-429d-83bd-df7d04bdd7ed.png)


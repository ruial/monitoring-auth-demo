# Secure Prometheus with OIDC or LDAP

An example Docker compose project to demonstrate the capabilities of [Pomerium](https://github.com/pomerium/pomerium) and [Authelia](https://github.com/authelia/authelia) to protect backends like [Prometheus](https://prometheus.io) or [Grafana](https://grafana.com). Custom Authorization headers were added with HAProxy and Nginx, based on user information from other headers. Read more on my [blog](https://briefbytes.com/2020/Secure-Prometheus-with-OIDC-or-LDAP).

## Setup

```bash
# create a self-signed certificate and make it trusted locally
brew install mkcert
mkcert -install
mkcert "*.localhost.pomerium.io"

# or use openssl to generate a self-signed certificate and then trust it manually
# https://serverfault.com/q/845766
openssl req \
    -newkey rsa:2048 \
    -x509 \
    -nodes \
    -keyout key.pem \
    -new \
    -out cert.pem \
    -subj '/CN=*.localhost.pomerium.io' \
    -reqexts SAN \
    -extensions SAN \
    -config <(cat /System/Library/OpenSSL/openssl.cnf \
        <(printf '[SAN]\nsubjectAltName=DNS:*.localhost.pomerium.io')) \
    -sha256 \
    -days 3650

# create a .env file according to .env.example in the pomerium directory
# edit the pomerium config to allow your email, according to your oidc provider in .env
# place the certificates in ./pomerium and ./nginx and start the containers
docker-compose up
```

Most of the configurations are based on the official documentation for each project. Always use HTTPS on the public endpoints for security reasons and to avoid issues with redirects and cookies. Check the *docker-compose.yml* file for the URLs used. Any subdomain from *localhost.pomerium.io* points to localhost.

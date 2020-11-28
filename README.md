# Secure Prometheus with OIDC or LDAP

An example Docker compose project to demonstrate the capabilities of [Pomerium](https://github.com/pomerium/pomerium) and [Authelia](https://github.com/authelia/authelia) to secure backends like [Prometheus](https://prometheus.io). Custom Authorization headers were added with HAProxy and Nginx, based on the user information from other headers.

## Setup

```bash
# create a self-signed certificate and make it trusted locally
brew install mkcert
mkcert -install
mkcert "*.localhost.pomerium.io"

# or use openssl to generate a self-signed certificate and then trust it manually
# https://serverfault.com/questions/845766/generating-a-self-signed-cert-with-openssl-that-works-in-chrome-58
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

# create a .env file according to the .env.example file in the pomerium directory
# edit the pomerium config to allow your email, according to your oidc provider in the .env
# place the certificates in ./pomerium and ./nginx and start the containers
docker-compose up
```

Check the *docker-compose.yml* file for the URLs used. Any subdomain from *localhost.pomerium.io* points to localhost.

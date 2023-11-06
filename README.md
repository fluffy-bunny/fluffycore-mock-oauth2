# fluffycore-mock-oauth2

Have you ever wanted a dev oauth2 server just for client_credentials flow?  Also known as Machine-2-Machine tokens.  
This project has a simple docker-compose and a secure traefik configuration.  

docker-compose that hosts a static configurable client_credentials flow oauth server.   

## Docker-Compose

```bash
docker-compose up - ./docker-compose.yml -d
```
## Health Check

The healthcheck is self contained in the dockerfile.  
Look at the ```whoami``` service's ```depends_on:``` entry.  

# Client Config

example.
```json
{
	"claims": {
		"permissions": ["read", "write"],
		"sub": "client1"
	},
	"client_id": "client1",
	"client_secret": "secret",
	"expiration": 3600,
	"issuer": ""
}
```

If issuer is empty or nil then the issuer will be the the base url of the request.

i.e. ```http://host.docker.internal:8084```, ```http://localhost:8084```, etc

TODO: test with traefik reverse proxy.

```curl
 curl --location 'http://host.docker.internal:8084/oauth/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--header 'Authorization: Basic Y2xpZW50MTpzZWNyZXQ=' \
--data-urlencode 'grant_type=client_credentials'
```

The jwt contents.
```json
{
  "client_id": "client1",
  "exp": 1699310389,
  "iat": 1699306789,
  "iss": "http://host.docker.internal:8084",
  "permissions": [
    "read",
    "write"
  ],
  "sub": "client1"
}
```

You can set your issuer to whatever you like.

## Traefik

```bash
# If it's the firt install of mkcert, run
mkcert -install

# Generate certificate for domain "docker.localhost"

mkcert -cert-file certs/local-cert.pem -key-file certs/local-key.pem "docker.localhost" "*.docker.localhost"  

docker-compose up - ./docker-compose.traefik.yml -d
```


### links

[traefik](https://traefik.docker.localhost/)  

[whoami](https://whoami.docker.localhost/)  

[sts-discovery](https://sts.docker.localhost/.well-known/openid-configuration)  

```json
{
    "issuer": "http://sts.docker.localhost",
    "jwks_uri": "http://sts.docker.localhost/.well-known/jwks",
    "token_endpoint": "http://sts.docker.localhost/oauth/token",
    "scopes_supported": [
        "offline_access"
    ],
    "grant_types_supported": [
        "client_credentials"
    ],
    "token_endpoint_auth_methods_supported": [
        "client_secret_basic",
        "client_secret_post"
    ]
}
```
[sts-jwks](http://sts.docker.localhost/.well-known/jwks)  

```json
{
    "keys": [
        {
            "alg": "ES256",
            "crv": "P-256",
            "kid": "0b2cd2e54c924ce89f010f242862367d",
            "kty": "EC",
            "use": "sig",
            "x": "YMrUm_S5-d-euQHrrzQMWJSFafSYcgUE0RYjfI7sErI",
            "y": "u-YUnRNGozzTF2mgxt_ecw9fFcY7Jqj01qJte53FZ58"
        }
    ]
}
```

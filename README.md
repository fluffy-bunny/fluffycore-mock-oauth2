# fluffycore-mock-oauth2

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
[sts-jwks](http://sts.docker.localhost/.well-known/jwks)  

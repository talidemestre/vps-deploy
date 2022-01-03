# VPS Deployment
Deploy all my apps with traefik! Configured for [tali.vision](tali.vision).

## 1. Create .env file
A `.env` file must exist in the same directory as `docker-compose.yml`, and must contain the following fields:
```sh
GITHUB_OAUTH_CLIENT_ID=<github oauth client id>
GITHUB_OAUTH_CLIENT_SECRET=<github oauth client secret>
RANDOM_SECRET=<some random secret value for auth>
DOMAIN=<your domain i.e. tali.vision>
WHITELIST=<primary emails associated with github accounts granted access to authenticated apps>
```

## 2. Ensure ACME file exists.
The `acme.json` file must exist in the `resource` subdirectory, with read/write access for the owner user only. If the file does not exist, it can be created with:
```sh
mkdir resource && touch resource/acme.json && chmod 600 resource/acme.json
```

## 3. Deployment
The applications can now be deployed using `docker-compose`. I recommend deploying in detached mode, so that an SSH session can be terminated while the apps continue to run. To achieve this, run the following from the same directory as `docker-compose.yml`.
```
docker-compose up -d
```

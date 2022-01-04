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

## 3. Create Folder Structure for CryptPad
CryptPad requires some specific folders to exist so that it can persist users and files between restarts. These can be created with:
```sh
mkdir resource/cryptpad resource/cryptpad/customize resource/cryptpad/data resource/cryptpad/data/block resource/cryptpad/data/blob resource/cryptpad/data/data resource/cryptpad/data/files
```
These folders must be owned by userid 4001, as per CryptPad requirements. This can be achieved with:
```sh
sudo chown -R 4001:4001 resource/cryptpad
```
The `resource/cryptpad/data/config.json` file must also be updated with the correct domains in the `httpSafeOrigin` and `httpUnsafeOrigin` fields.

## 4. Deployment
The applications can now be deployed using `docker-compose`. I recommend deploying in detached mode, so that an SSH session can be terminated while the apps continue to run. To achieve this, run the following from the same directory as `docker-compose.yml`.
```
docker-compose up -d
```

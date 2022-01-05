# VPS Deployment
Deploy all my apps with traefik! Configured for [tali.vision](tali.vision).

## 1. Create .env file
A `.env` file must exist in the same directory as `docker-compose.yml` . An example is included as `sample.env`. Minimally the following fields should be included:
```sh
GITHUB_OAUTH_CLIENT_ID=<github oauth client id>
GITHUB_OAUTH_CLIENT_SECRET=<github oauth client secret>
RANDOM_SECRET=<some random secret value for auth>
DOMAIN=<your domain i.e. tali.vision>
WHITELIST=<primary emails associated with github accounts granted access to authenticated apps>

# EMAIL CONFIG
DATABASE_USER_PASSWORD=<mariadb password>
RSPAMD_PASSWORD=<spam domain password>
MAILSERVER_HOSTNAME=mail
MAILSERVER_DOCKER_TAG=1.1.1
VOLUMES_ROOT_PATH=./resource/mail/

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
The `resource/cryptpad/data/config.js` file must also be updated with the correct domains in the `httpSafeOrigin` and `httpUnsafeOrigin` fields.

## 4. Deployment
The applications can now be deployed using `docker-compose`. I recommend deploying in detached mode, so that an SSH session can be terminated while the apps continue to run. To achieve this, run the following from the same directory as `docker-compose.yml`.
```
docker-compose up -d
```

## 5. Sysadmin the Email Accounts
Email will not work straight away, a few things should be done to get this working. Refer to initial configuration at [this address](https://github.com/mailserver2/mailserver/wiki) for full instructions.
### 5.1. Set up Postfixadmin
You must go to the setup page at https://postfixadmin.domain.tld/setup.php and define a setup password. This will provide you with a hash, which you must set by execing into the docker container like so.

```sh
$ docker exec -ti postfixadmin setup
Postfixadmin setup hash : <YOUR HASH>
Setup done.
```

You can set up accounts here which you can then login to from `webmail.domain.tld`.

### 5.2. Set up Rainloop
An admin account will need to be setup. Navigate to https://webmail.domain.tld/?admin and login with 'admin' and '12345'.

Then configure the 'Domain' tab as per this page https://github.com/mailserver2/mailserver/wiki/Rainloop-initial-configuration/ . Generally, this means use the default ports, add your TLD to each field, use SSL/TLS for IMAP, and use STARTTLS for SMTP and SIEVE. Leave the whitelist empty.

### 5.3. Create an SPF Record
Use your name registrar to create an SPF record which prevents spammers from using your email domain to send unauthenticated emails. This should simply contain the following text:
```
v=spf1 ip4:<mail server IP> -all 
```

### 5.4. Use the Email
From here, you can create emails using the Postfixadmin console which can both send and receive!

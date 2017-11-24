# Dokku Locomotive

This project aims to simplify the deployment of [Locomotive CMS](https://www.locomotivecms.com/) engine using [Dokku](https://github.com/dokku/dokku). Just follow the simple steps below.

## Installing Dokku and configuring the server

1. Build a new instance using you favorite VPS provider.

2. Login via SSH on VPS instance and [install Dokku](https://github.com/dokku/dokku#installation).

3. Configure the swap space for your VPS server. You can follow [this](https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-16-04) tutorial.

4. Type the public address of you instance in your browser and add a public key and click "Finish Setup". You must add the key of the developer resposible to deploy new versions of the application.

5. Create Dokku application.

```
> dokku apps:create my_webapp
```

6. Dokku containers are ephemeral. You have do add a persistent storage, otherwise, all your uploaded data will be lost if you reboot your system or container. If you want to [store your assets in AWS S3](#using-s3-to-store-uploaded-assets), skip this step.

```
mkdir -p /home/dokku/storage/my_webapp/public/sites
dokku storage:mount my_webapp /home/dokku/storage/my_webapp/public/sites:/app/public/sites
```

7. Add you domain names in Dokku application

```
> dokku domains:add my_webapp mydomain1.com mydomain2.com
```

8. Locomotive requires MongoDB. Install [Dokku's MongoDB plugin](https://github.com/dokku/dokku-mongo), create the database and link the service to your Dokku application.
```
> sudo dokku plugin:install https://github.com/dokku/dokku-mongo.git mongo
> dokku mongo:create my_webapp_mongodb
> dokku mongo:link my_webapp_mongodb my_webapp
```

## Deploy Locomotive CMS

1. On your local machine, clone the Locomotive CMS application engine.

```
> git clone git@github.com:gushonorato/locomotive_cms_dokku.git
```

2. Add Dokku's production repository (like Heroku):

```
> git remote add dokku dokku@<ip_address>:<application_name>
```

3. To deploy Locomotive CMS, just push to production git repository.

```
> git push dokku master
```

## Optional configuration

### SSL configuration

Configure SSL using [Dokku's Let's Encrypt plugin](https://github.com/dokku/dokku-letsencrypt).

```
> sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
> dokku config:set --no-restart my_webapp DOKKU_LETSENCRYPT_EMAIL=<e-mail para o certificado>
> dokku letsencrypt my_webapp
```

Configure the plugin to auto renew your certificate.

```
> dokku letsencrypt:auto-renew my_webapp
```

### Sending e-mails (SMTP configuration)

If you need to send e-mails though Locomotive CMS, you must add some environment variables using the following command:

```
> dokku config:set my_webapp SMTP_ADDRESS=<value> SMTP_DOMAIN=<value> SMTP_USER_NAME=<value> SMTP_PASSWORD=<value>

```

You can also specify aditional configuration with the following environment variables (optional):

- `SMTP_PORT` (defaut: 587),
- `SMTP_AUTHENTICATION` (default: login)
- `SMTP_ENABLE_STARTTLS_AUTO` (default: true)

### Using S3 to store uploaded assets

You can configure Locomotive to use S3 to store assets and uploads. You must configure environment variables `S3_BUCKET`, `S3_KEY_ID`, `S3_SECRET_KEY` e `S3_BUCKET_REGION` with the following command:

```
> dokku config:set my_webapp S3_BUCKET=xxxx S3_KEY_ID=xxxx S3_SECRET_KEY=xxxx S3_BUCKET_REGION=xxxx
```

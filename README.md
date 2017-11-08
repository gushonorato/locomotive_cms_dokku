# Dokku Locomotive

This project aims to simplify the deployment of [Locomotive CMS](https://www.locomotivecms.com/) engine using [Dokku](https://github.com/dokku/dokku). Just follow the simple steps below.

## Installing Dokku and configuring the server

1. Build a new instance using you favorite VPS provider.

2. Login via SSH on instance and [install Dokku](https://github.com/dokku/dokku#installation).

3. Type the public address of you instance in your browser and add a public key and click "Finish Setup". You must add the key of the developer resposible to deploy new versions of the application.

4. Create Dokku application.

```
> dokku apps:create my_webapp
```

5. O Locomotive uses S3 to store assets and uploads. You must configure environment variables `S3_BUCKET`, `S3_KEY_ID`, `S3_SECRET_KEY` e `S3_BUCKET_REGION` with the following command:

```
> dokku config:set my_webapp S3_BUCKET=xxxx S3_KEY_ID=xxxx S3_SECRET_KEY=xxxx S3_BUCKET_REGION=xxxx
```

6. Add you domain names in Dokku application

```
> dokku domains:add my_webapp mydomain1.com mydomain2.com
```

7. Locomotive requires MongoDB. Install [Dokku's MongoDB plugin](https://github.com/dokku/dokku-mongo), create the database and link the service to your Dokku application.
```
> sudo dokku plugin:install https://github.com/dokku/dokku-mongo.git mongo
> dokku mongo:create my_webapp_mongodb
> dokku mongo:link my_webapp_mongodb my_webapp
```

8. Configure SSL using [Dokku's Let's Encrypt plugin](https://github.com/dokku/dokku-letsencrypt).

```
> sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
> dokku config:set --no-restart my_webapp DOKKU_LETSENCRYPT_EMAIL=<e-mail para o certificado>
> dokku letsencrypt my_webapp
```

9. Configure the plugin to auto renew your certificate.

```
> dokku letsencrypt:auto-renew my_webapp
```

10. If you need to send e-mails though Locomotive CMS, you must add some environment variables using the following command:

```
> dokku config:set my_webapp SMTP_ADDRESS=<value> SMTP_DOMAIN=<value> SMTP_USER_NAME=<value> SMTP_PASSWORD=<value>

```

You can also specify aditional configuration with the following environment variables (optional):

- `SMTP_PORT` (defaut: 587),
- `SMTP_AUTHENTICATION` (default: login)
- `SMTP_ENABLE_STARTTLS_AUTO` (default: true)

## Deploy Locomotive CMS

1. Clone the Locomotive CMS application engine.

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

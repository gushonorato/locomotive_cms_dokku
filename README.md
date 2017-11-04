# Meu plantão médico site engine

O site foi criado usando o Locomotive CMS. Um site criado no Locomotive CMS consiste em dois projetos, o servidor (engine) e o ambiente de desenvolvimento (wagon). Esse projeto consiste no engine. Para mais informações acesse a documentação do Locomotive CMS em: https://locomotive-v3.readme.io/docs/getting-started-with-locomotive.

## Deploy

### Preparando o servidor

O deploy da aplicação é feito em uma EC2 utilizando o PaaS Dokku.

1. Crie uma instância da Amazon EC2. Adicione um Elastic IP e configure corretamente o DNS para os domínios meuplantaomedico.com e meuplantaomedico.com.br.

2. Entre na máquina usando o SSH e instale o Dokku.

```
> wget https://raw.githubusercontent.com/dokku/dokku/v0.10.5/bootstrap.sh
> sudo DOKKU_TAG=v0.10.5 bash bootstrap.sh
```

3. Digite o endereço IP publico da sua instância EC2 no seu navegador

4. Adicione a sua chave pública SSH na interface e clique em "Finish Setup". A chave que deve ser adicionada é do desenvolvedor que irá ser responsável por fazer o deploy das novas versões.

5. Crie a aplicação no Dokku.

```
> dokku apps:create meu_plantao_medico_site_engine
```

6. O Locomotive usa o S3 para armazenar os uploads. Sendo assim, é preciso informar as credenciais através das variáveis de ambiente: `S3_BUCKET`, `S3_KEY_ID`, `S3_SECRET_KEY` e `S3_BUCKET_REGION`. Para adicionar as variáveis de ambiente use o seguinte comando:

```
> dokku config:set meu_plantao_medico_site_engine S3_BUCKET=xxxx S3_KEY_ID=xxxx S3_SECRET_KEY=xxxx S3_BUCKET_REGION=xxxx
```

7. Adicione os domínios ao Dokku

```
> dokku domains:add meu_plantao_medico_site_engine meuplantaomedico.com meuplantaomedico.com.br
```

8. Instale o plugin do mongo, crie a base de dados e faça o link com a aplicação.
```
sudo dokku plugin:install https://github.com/dokku/dokku-mongo.git mongo
dokku mongo:create meu_plantao_medico_site_mongo
dokku mongo:link meu_plantao_medico_site_mongo meu_plantao_medico_site_engine
```

9. Configure o SSL através do plugin Let's Encrypt

```
> sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
> dokku config:set --no-restart mpm DOKKU_LETSENCRYPT_EMAIL=<e-mail para o certificado>
> dokku letsencrypt meu_plantao_medico_site_engine
```

10. Configure o plugin para renovar automaticamente o certificado

```
> dokku letsencrypt:auto-renew meu_plantao_medico_site_engine
```

### Configurando a máquina de desenvolvimento

Para fazer o deploy é tão simples quanto usar o Heroku.

1. Clone a aplicação engine.

```
> git clone git@gitlab.com:meu_plantao_medico/meu_plantao_medico_site_engine.git
```

2. Depois, basta apenas configurar o repositório Git de produção:

```
> git remote add dokku dokku@IP_INSTANCIA_EC2:meu_plantao_medico_site_engine
```

3. Para enviar uma atualiazação, basta fazer um push no repostiório dokku configurado.

```
> git push dokku master
```

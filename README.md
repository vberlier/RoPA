# Record of processing activities

*Read in other languages: [English](README.md), [French](README.fr.md).*

The record of processing activities is a tool distributed freely by [Safran](https://www.safran-group.com/) in order to carry out analyzes of compliance with the regulations on data protection. This application is deployed on a server and can be accessed from a web browser.

![Homepage](https://github.com/Safran/RoPA/blob/master/storage/app/public/homescreen_en.png?raw=true)  
![Analysis](https://github.com/Safran/RoPA/blob/master/storage/app/public/analysisscreen_en.png?raw=true)

Advantages of the tool:
- contains the questions of the French Data Protection Authority (CNIL) for the [small and medium companies](https://www.cnil.fr/fr/rgpd-et-tpepme-un-nouveau-modele-de-registre-plus-simple-et-plus-didactique);
- management of the questions of the analysis (creation, modification, deletion);
- validation workflow;
- 3 roles:
	- administrator who manages questions and users;
	- DPO who manages the analyzes;
	- employee who provides analysis.
- adaptable to any size of structure (multi-company processing management);
- Possibility to create analysis models.

## Getting Started

### Prerequisites

Apache 2.4+
PHP 7.1.3+
Node.js 6.14.x
npm 3.10.10
Mysql 5.5.x
Composer

#### PHP configuration
- safe mode : disabled
- register_globals : off
- session.nocache_limiter : nocache
- session.auto_start : 0
- magic_quotes_gpc: off
- memory_limit = 3000M
- upload_max_filesize = 128M
- post_max_size = 128M
- max_execution_time = 120s
- date.timezone = "Europe/Paris"

#### PHP extensions
MySQLi, Zlib compression functions, DOM functions, Session support, PCRE functions, PHP-CLI, Curl, Multibyte string functions, Exif functions, GD, SOAP, LDAP, Memcache, OpenSSL, PDO, Tokenizer, XML, JSON, CType

#### Apache configuration
The DocumentRoot should point to the RoPA/public folder.
The RoPA folder must be set to AllowOverride All.

#### Apache modules
php_module, rewrite, headers, deflate, expires.

#### MySQL configuration
The MySQL max_allowed_packet variable should be set to 512M.

### Installing

Get the source code : `git clone https://github.com/Safran/RoPA.git`

#### *Database*

Create a database and import the database.sql file.

#### *Compilation*

Move the RoPA directory to the server root folder.

Go to the RoPA directory.

Set the APP_URL to the server url in the .env file.
> **Note:** It should the the same URL as the "ServerName" of your apache server.

Edit the .env file to set the database hostname, name, username and password.

Enter `composer install`.
> **Note:** You may see an error message related to the database that you can ignore.

Enter `npm install`.

Enter `npm run production`.

Enter `php artisan RoPA:install` and answer `yes` to prompt to generate the app key.

#### *Access*

You should be able to load the `http://[APP_URL]/en/login` page to connect with a local account.
If you want to use SAMLv2 authentication, you should load the root page: `http://[APP_URL]`

### Deployment

#### *SAMLv2 / LDAP*

Change the following variables in .env to absolute path:
SAML2_SP_x509="file://[YOUR FULLPATH]/certs/saml.crt"
SAML2_SP_PRIVATEKEY="file://[YOUR FULLPATH]/certs/saml.pem"

The URL for metatada is [APP_URL]/saml2/metadata

You can configure LDAP to check deleted users in .env and config/authcompany.php

#### *Scheduling and Queues*

A cron tab must be set:
````
* * * * * php /[YOU FULLPATH]/artisan schedule:run >> /dev/null 2>&1
* * * * * pgrep php > /dev/null || php /[YOU FULLPATH]/artisan queue:work >> /dev/null 2>&1
````

> **Note:** You can edit the app/Console/Kernel.php file to stop receiving email notifications from scheduling:
Add ['--disable-notifications'] in the second parameter for command.

#### *Account*

A local account is set:
Administrator:
Login: admin
Password: admin

DPO:
Login: dpo
Password: admin

Employee:
Login: employee
Password: admin

#### *EMail*

Emails can be configured in the .env file

#### *Images*

You can change the favicon in RoPA/public/images/favicon.png
You can change the logo in:
- RoPA/public/images/logo.png
- RoPA/public/images/logo.svg
- RoPA/public/images/general/logo.svg
- RoPA/resources/assets/img/general/logo.svg

By default, main images are linked to `localhost` but you can reload them in the administration panel : `[APP_URL]/admin/settings`

## Installation

The package has been installed on Redhat 7, Debian 9, MAMP 4.2 and 5.1.

## Running locally with `docker-compose`

You can run the application locally using `docker-compose`. While the provided configuration isn't suitable for production environments, it can be used for development in order to avoid having to setup the necessary tools on the host system.

### Initial setup

The first thing to do after cloning the project is to create the `.env` file from the `.env.example` template.

```bash
$ cp .env.example .env
```

Then, you need to configure the environment variables related to the database as they're used by the `docker-compose` configuration to create the mysql container.

```ini
# Corresponds to the name of the service defined in docker-compose.yml
DB_HOST=db

DB_DATABASE=local_database
DB_USERNAME=local_user
DB_PASSWORD=local_password
```

Finally, you need to make sure that the application will have access to the `storage` directory through the volume attached to the container.

```bash
$ sudo chmod -R 777 storage
```

### Launching

Once the initial setup is done, you should be able to launch the application by running `docker-compose up` at the root of the project.

```bash
$ docker-compose up
```

If you're launching the application for the first time, you'll now need to install the dependencies and compile the project's assets. Otherwise, the application should be running at <http://localhost/en/login>.

### Installing the dependencies and compiling

You'll first need to enter the container defined by the `web` service in the `docker-compose` configuration.

```bash
$ docker-compose exec -w /RoPA web bash
```

Now, simply run the following commands:

```bash
$ composer install
$ npm install
$ npm run dev
$ php artisan
```

## License

This project is licensed under the GNU GPLv3 License - see the [LICENSE](LICENSE) file for details.
Any contribution or work of contributor as described in the GNU GENERAL PUBLIC LICENSE Version 3 as « contributor version » shall become a contribution ruled under the GNU GENERAL PUBLIC LICENSE Version 3.

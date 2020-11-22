<p align="center">
    <a href="https://sylius.com" target="_blank">
        <img src="https://demo.sylius.com/assets/shop/img/logo.png" />
    </a>
</p>

<h1 align="center">Sylius Standard Edition</h1>

<p align="center">This is Sylius Standard Edition repository for starting new projects.</p>

About
-----

Sylius is the first decoupled eCommerce platform based on [**Symfony**](http://symfony.com) and [**Doctrine**](http://doctrine-project.org). 
The highest quality of code, strong testing culture, built-in Agile (BDD) workflow and exceptional flexibility make it the best solution for application tailored to your business requirements. 
Enjoy being an eCommerce Developer again!

Powerful REST API allows for easy integrations and creating unique customer experience on any device.

We're using full-stack Behavior-Driven-Development, with [phpspec](http://phpspec.net) and [Behat](http://behat.org)

Documentation
-------------

Documentation is available at [docs.sylius.com](http://docs.sylius.com).

Installation
------------

Requirements
Server instance with at least 2048 MB of memory.
Nginx or Apache. In this guide, we use Nginx.
PHP version 7.2 or greater with some specific PHP extensions: gd, exif, fileinfo, intl
PHP configuration settings: memory_limit equal to or greater than 1024M, date.timezone
MySQL version 5.7 or 8.0
Composer
Node.js
Yarn

Follow guide:
https://www.vultr.com/docs/how-to-install-sylius-ecommerce-platform-on-ubuntu-18-04-lts
https://tecnstuff.net/how-to-install-composer-on-ubuntu-18-04/



```bash
$ wget http://getcomposer.org/composer.phar
$ composer update
$ php composer.phar create-project sylius/sylius-standard project
$ cd project
```

Configuration
------------
Sylius uses environment variables to configure the connection with database and mailer services. You can look up the default values in .env file and customise them by creating .env.local with variables you want to override. For example, if you want to change your database name from the default sylius_%kernel.environment% to my_custom_sylius_database, the contents of that new file should look like the following snippet:
```bash
$ DATABASE_URL=mysql://username:password@host/my_custom_sylius_database
```

After everything is in place, run the following command to install Sylius:
```bash
$ php bin/console sylius:install
$ yarn install
$ yarn build
$ wget https://get.symfony.com/cli/installer -O - | bash
$ mv /home/mapiful/.symfony/bin/symfony /usr/local/bin/symfony
```

Run:
------------
```bash
$ symfony server:start -d
$ open http://127.0.0.1:8000/admin
```

Troubleshooting
---------------

If something goes wrong, errors & exceptions are logged at the application level:

```bash
$ tail -f var/log/prod.log
$ tail -f var/log/dev.log
```

If you are using the supplied Vagrant development environment, please see the related [Troubleshooting guide](etc/vagrant/README.md#Troubleshooting) for more information.

Timezones
https://www.php.net/manual/en/timezones.europe.php


# [![](https://bitbag.io/wp-content/uploads/2020/10/vsf.png)](https://bitbag.io/contact-us/?utm_source=github&utm_medium=referral&utm_campaign=plugins_vsf)
# BitBag SyliusVueStorefrontPlugin

Install Elasticsearch
---------------
https://www.willandskill.se/en/install-elasticsearch-6-x-on-ubuntu-18-04-lts/

Follow BitBag SyliusVueStorefrontPlugin
---------------
https://github.com/BitBagCommerce/SyliusVueStorefrontPlugin

```bash
$ php bin/console doctrine:schema:update --force
$ php bin/console sylius:fixtures:load
$ php bin/console fos:elastica:populate
```

Clear cache
sudo php bin/console cache:clear --env=prod

## vsf-default
Vue Storefront Default theme (please consider vsf-capybara instead)

**🎉  Current status: Production ready**

## The design

This theme is basic template of typical eCommerce for a fashion industry.
In the project we used [Material Icons](https://github.com/google/material-design-icons).

<img width="880" alt="Vue Storefront - Annimations in sidebar menu" src="https://user-images.githubusercontent.com/18116406/33559404-ed674bcc-d90c-11e7-864a-91e42d1c92cd.gif">

Here you can read more about the process of [designing PWA for eCommerce](https://www.linkedin.com/pulse/designing-pwa-ecommerce-karl-bzik/).

The design is available in open source in the Figma file format under the URL https://www.figma.com/file/VKyqbHFI55TKIKcQlFLiVpVF/Vue-Storefront-Open-Source.

## Browser Compatibility

* last 2 Chrome versions
* last 2 Firefox versions
* last 2 Edge versions
* modern browsers

## 🔌  Installation
To be able to use new Default theme in your Vue Storefront installation, you need to:

1. Install `lerna` globally:

   ```
   npm install -g lerna
   ```

   or

   ```
   yarn global add lerna
   ```

1. Configure `vsf-default` repository as a git submodule in theme path of your Vue Storefront workspace, and then track `master` branch:

   ```
   git submodule add -b master git@github.com:DivanteLtd/vsf-default.git src/themes/default
   ```

1. Fetch all the data:

   ```
   git submodule update --init --remote
   ```

1. Update Vue Storefront configuration by copying `local.json` file from `vsf-default` to root `config` directory.
1. Update TypeScript compiler option in `tsconfig.json` in root directory: change value for `compilerOptions`**.**`paths`**.**`theme/*` from default theme `["src/themes/default/*"]` to brand new Default theme: `["src/themes/default/*"]`.
1. Download all dependencies and start development server:

   ```
   lerna bootstrap && yarn dev
   ```

1. That’s all! Now after opening your development server (http://localhost:3000 by default) you should see Vue Storefront with Default theme!  🎉


MIT License
-----------

Sylius is completely free and released under the [MIT License](https://github.com/Sylius/Sylius/blob/master/LICENSE).

Authors
-------

Sylius was originally created by [Paweł Jędrzejewski](http://pjedrzejewski.com).
See the list of [contributors from our awesome community](https://github.com/Sylius/Sylius/contributors).

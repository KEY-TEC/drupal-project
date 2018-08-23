# Docksal powered Drupal 8 Installation

This is a sample vanilla Drupal 8 installation pre-configured for use with Docksal.  

Features:

- Vanilla Drupal 8
- `fin init` [example](.docksal/commands/init)
- Using the [default](.docksal/docksal.env#L9) Docksal LAMP stack with [image version pinning](.docksal/docksal.env#L13-L15)
- PHP and MySQL settings overrides [examples](.docksal/etc)
- Drush aliases [example](drush/aliases.drushrc.php) (`drush @docksal status`)

## Setup instructions

### Step #1: Docksal environment setup

**This is a one time setup - skip this if you already have a working Docksal environment.**  

Follow [Docksal environment setup instructions](https://docs.docksal.io/en/master/getting-started/env-setup)

### Step #2: Project setup

1. Clone this repo into your Projects directory

    ```
    git clone git@github.com:KEY-TEC/drupal-project.git drupal8
    cd drupal8
    ```

2. Go to /docroot/sites/default and copy/paste the `default.settings.local.php` as `settings.local.php` to the same directory.

3. Initialize the site

    ```
    fin init
    ```
    This will
    - run `composer install` to install a drupal 8 with its dependencies, creating a vendor folder.
    - run `yarn install` in the custom theme folder `mytheme` to setup patternlab and install additional libraries using bower.
        - You will get a node_modules folder and a bower_components folder in your theme.
        - If you had renamed your theme you should also change the name in the init script in .docksal/commands
    - initialize local settings and running a site-install via drush
    - activate the custom theme
    - enable basic modules like display suite and ui_patterns
    
4. Point your browser to

    ```
    http://drupal8.docksal
    ```

5. If you want to change the name of your theme you need to rename
    - the theme folder in themes/custom including
        - [themename].info.yml
        - [themename].libraries.yml
        - [themename].theme
        - [themename].ui_patterns.yml
    - the drupal `themeFile` setting in `gulpconfig.js`
    - the `THEME_NAME` setting in .docksal/commands/init

When the automated install is complete the command line output will display the admin username and password.

6. Open the page and login as admin to activate your theme in `Appearance`.

## More automation with 'fin init'

Site provisioning can be automated using `fin init`, which calls the shell script in [.docksal/commands/init](.docksal/commands/init).  
This script is meant to be modified per project. The one in this repo will give you a good starting example.

Some common tasks that can be handled by the init script (an other [custom commands](https://docs.docksal.io/en/master/fin/custom-commands/)):

- initialize local settings files for Docker Compose, Drupal, Behat, etc.
- import DB or perform a site install
- compile Sass
- run DB updates, revert features, clear caches, etc.
- enable/disable modules, update variables values


## Security notice

This repo is intended for quick start demos and includes a hardcoded value for `hash_salt` in `settings.php`.  
If you are basing your project code base on this repo, make sure you regenerate and update the `hash_salt` value.  
A new value can be generated with `drush ev '$hash = Drupal\Component\Utility\Crypt::randomBytesBase64(55); print $hash . "\n";'` 
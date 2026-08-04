# Woocommerce environment under docker
- Composer based setup with seperated content / config from standard core
```
├── content                   # Wordpress content dir
│   ├── mu-plugins
│   ├── plugins
│   ├── themes
│   └── uploads
├── core                      # Wordpress core
├── vendor                    # Composer dependencies
├── composer.json             # Composer config
└── wp-config.php             # Wordpress config
```

# Pre-Requistics
- Docker / docker-compose installed
- Some linux and wordpress experience

# Setup host
- Clone this repository in host machine
- Create folders for `woocommerce` and `mariadb` data
- Create `.env` file using sample, `set / update` values as needed
```
cp .env.sample .env
```
- Add this line to `/etc/hosts` for custom url
```
127.0.0.1 localhost.test
```
- Add these lines to `~/.bash_aliases` for easy commands
- Replace `$path_to_compose_file` with actual path
- Default core installation path is `./core`
```
alias woocommerce_stack="docker compose -f $path_to_compose_file"
alias cli_wp="docker exec -it cli wp --path=core"
alias cli_composer="docker exec -it cli composer"
```

# Setup docker
- Build images and start the docker environment
```
woocommerce_stack up -d
```
- Check `status / logs` of containers
```
woocommerce_stack ps
woocommerce_stack logs
```
- Folders for cli `persistent cache` will be auto created on host
```
${HOME}/.wp-cli/cache | ${HOME}/.composer
```

# Install Woocommerce
- Installation can be done either for [existing](#existing-project) or [new](#new-project) project

## Existing Project
- Clone your project into host machine `woocommerce` folder
- Modify existing `composer.json` to match this project structure, [reference](#new-project)
- Download wordpress with custom plugins / themes
```
cli_composer install
```
- Copy over existing `wp-config.php` and update `details` as needed, [reference](#new-project)
- Backup your existing `database` and import it to new `db host`
- Update common and other admin settings using `queries` as needed
```
UPDATE wp_options SET option_value = 'localhost.test' WHERE option_name = 'blogname';
UPDATE wp_options SET option_value = 'http://localhost.test' WHERE option_name = 'home';
UPDATE wp_options SET option_value = 'http://localhost.test' WHERE option_name = 'siteurl';
```
- Copy over any required media from `uploads`
- Navigate to `localhost.test` to get started

## New Project
- Create the `composer config`, sample below
```
{
  "repositories": [
    {
      "name": "wp-packages",
      "type": "composer",
      "url": "https://repo.wp-packages.org"
    }
  ],
  "require": {
    "composer/installers": "^2.2",
    "roots/wordpress": "^6.8",
    "wp-theme/twentytwentyfive": "^1.5",
    "wp-plugin/akismet": "^5.7"
  },
  "config": {
    "allow-plugins": {
      "composer/installers": true,
      "roots/wordpress-core-installer": true
    }
  },
  "extra": {
    "wordpress-install-dir": "core",
    "installer-paths": {
      "content/plugins/{$name}/": ["type:wordpress-plugin"],
      "content/mu-plugins/{$name}/": ["type:wordpress-muplugin"],
      "content/themes/{$name}/": ["type:wordpress-theme"]
    }
  }
}
```
- Download wordpress with standard plugins / themes
```
cli_composer install
```
- Generate fresh config, move it outside core and set content details
```
docker exec cli bash -c 'wp --path=core config create --dbhost="$DB_HOST" --dbname="$DB_NAME" \
  --dbuser="$DB_USER" --dbpass="$DB_PASSWORD" && mv core/wp-config.php .'
cli_wp config set WP_CONTENT_DIR "__DIR__ . '/content'" --raw
cli_wp config set WP_CONTENT_URL "'http://localhost.test/content'" --raw
```
- Install wordpress, this replaces the `wordpress setup wizard`
```
cli_wp core install --url="http://localhost.test" --title="localhost.test" \
  --admin_email="$USER@localhost.test" --admin_user="$USER" --admin_password="$USER#$(id -u)"
```
- Install woocommerce with other plugins and activate all
```
cli_composer require wp-plugin/woocommerce wp-plugin/disable-emails
cli_wp plugin activate --all
```
- Navigate to `localhost.test/wp-admin` in your browser, login window will load
- Default admin `user / password` are your host machine `user / user#uid`
- Once logged in, complete the `woocommerce setup wizard` to get started
- In case `setup wizard` does not auto load, navigate to `WooCommerce/Home`

# Extra Docs
- To deploy sample data, follow this official [guide](https://woocommerce.com/document/importing-woocommerce-sample-data)
- To explore composer packages, check this official [guide](https://wp-packages.org/docs)
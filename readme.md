# Woocommerce environment under docker
- Wordpress powered woocommerce environment under docker

# Pre-Requistics
- Install docker / docker-compose

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
```
alias woocommerce_stack="docker compose -f $path_to_compose_file"
function cli_wp {
  docker exec -it cli bash -ic "wp $*"
}
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

# Install Woocommerce
- Installation can be done either for [existing](#existing-project) or [new](#new-project) project

## Existing Project
- Clone your project into host machine `woocommerce` folder
- Copy over existing `wp-config.php` and update `db details` as needed
- Backup your existing `database` and import it to new `db host`
- Update common and other admin settings using `queries` as needed
```
UPDATE wp_options SET option_value = 'localhost.test' WHERE option_name = 'blogname';
UPDATE wp_options SET option_value = 'http://localhost.test' WHERE option_name = 'home';
UPDATE wp_options SET option_value = 'http://localhost.test' WHERE option_name = 'siteurl';
```
- Copy over any media from `wp-content/uploads`
- Navigate to `localhost.test` to get started

## New Project
- Download latest wordpress and generate fresh config
```
cli_wp core download
cli_wp config create --dbhost='$DB_HOST' --dbname='$DB_NAME' --dbuser='$DB_USER' --dbpass='$DB_PASSWORD'
```
- Install wordpress, this replaces the `wordpress setup wizard`
```
cli_wp core install --url='http://localhost.test' --title='localhost.test' \
  --admin_email='$HOST_USER@localhost.test' --admin_user='$HOST_USER' --admin_password='$HOST_USER#$(id -u)'
```
- Install woocommerce and other plugins
```
cli_wp plugin install woocommerce disable-emails --activate
```
- Navigate to `localhost.test/wp-admin` in your browser, login window will load
- Default admin `user / password` are your host machine `user / user#uid`
- Once logged in, navigate to `WooCommerce/Home` and complete the `setup wizard` to get started

# Extra Docs
- To deploy sample data follow this official [guide](https://woocommerce.com/document/importing-woocommerce-sample-data)
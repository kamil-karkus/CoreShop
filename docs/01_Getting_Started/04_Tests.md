# Tests

CoreShop comes with a full test-suite. We use [Behat](https://docs.behat.org/en/latest/) for our Domain-Tests and
Frontend Tests. It also comes with [Psalm](https://psalm.dev/) and [PHPStan](https://phpstan.org/) for Static Analysis.

## Running Tests Locally

### Psalm

```
vendor/bin/psalm
```

### PHPStan

```
SYMFONY_ENV=test vendor/bin/phpstan analyse -c phpstan.neon src -l 3 --memory-limit=-1
```

### BEHAT

- create database `coreshop4__behat`

#### Install Pimcore and CoreShop in Test Env

```
APP_ENV=test PIMCORE_TEST_DB_DSN=mysql://root:ROOT@coreshop-4/coreshop4___behat PIMCORE_INSTALL_ADMIN_USERNAME=admin PIMCORE_INSTALL_ADMIN_PASSWORD=admin PIMCORE_INSTALL_MYSQL_HOST_SOCKET=coreshop-4 PIMCORE_INSTALL_MYSQL_USERNAME=root PIMCORE_INSTALL_MYSQL_PASSWORD=ROOT PIMCORE_INSTALL_MYSQL_DATABASE=coreshop4___behat PIMCORE_INSTALL_MYSQL_PORT=3306 PIMCORE_KERNEL_CLASS=Kernel vendor/bin/pimcore-install --env=test --skip-database-config -n
APP_ENV=test PIMCORE_CLASS_DIRECTORY=var/tmp/behat/var/classes PIMCORE_TEST_DB_DSN=mysql://root:ROOT@coreshop-4/coreshop4___behat bin/console coreshop:install
```

#### BEHAT Domain

```
CORESHOP_SKIP_DB_SETUP=1 PIMCORE_TEST_DB_DSN=mysql://root:ROOT@coreshop-4/coreshop4___behat vendor/bin/behat -c behat.yml.dist -p default
```

#### BEHAT UI

```
vendor/bin/bdi detect drivers

# OUTSIDE CONTAINER
# Run Symfony Server
APP_ENV=test PIMCORE_TEST_DB_DSN=mysql://root:ROOT@127.0.0.1:3306/coreshop4___behat symfony server:start --port=9080 --dir=public --no-tls

# Run Behat
CORESHOP_SKIP_DB_SETUP=1 PANTHER_EXTERNAL_BASE_URI=http://127.0.0.1:9080/index_test.php PANTHER_NO_HEADLESS=0 PIMCORE_TEST_DB_DSN=mysql://root:ROOT@127.0.0.1:3306/coreshop4___behat php -d memory_limit=-1 vendor/bin/behat -c behat.yml.dist -p ui -vvv 
```
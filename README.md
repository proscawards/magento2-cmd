# magento2 (Commands Only)

## Magento Installation
```
php bin/magento setup:install --base-url="http://localhost/magento2/" --db-host="localhost" --db-name="magento" --db-user="root" --db-password="root" --admin-firstname="admin" --admin-lastname="admin" --admin-email="admin@magento.com" --admin-user="admin" --admin-password="admin123" --language="en_US" --currency="MYR" --timezone="Asia/Kuala_Lumpur" --use-rewrites="1" --backend-frontname="admin" --search-engine=elasticsearch7 --elasticsearch-host="localhost" --elasticsearch-port=9200
```

## Enable Rewrite in Database (change value to 0)
```
SELECT * FROM core_config_data WHERE path = 'web/seo/use_rewrites'
```

## Classic Magento stuff
```
php bin/magento setup:upgrade
php bin/magento setup:static-content:deploy -f
php bin/magento indexer:reindex
php bin/magento cache:flush
```
## Set Developer Mode
```
php bin/magento deploy:mode:set developer
```
## Disable 2FA
```
php bin/magento module:disable Magento_TwoFactorAuth
```
## Enable Product Image Upload
```
php bin/magento module:disable Magento_ProductVideo
php bin/magento module:disable --clear-static-content
```
## Set limit
```
max_execution_time = 18000
max_input_time = 1800
memory_limit = 4G
```
## Enable mod rewrite
```
LoadModule version_module modules/mod_version.so
```
## Flush Cache
```
rm -rf var/cache/*
rm -rf var/generation/*
```
## Modify required files
C:\xampp\htdocs\magento2\vendor\magento\framework\Image\Adapter\Gd2.php
```
private function validateURLScheme(string $filename) : bool
  {
      $allowed_schemes = ['ftp', 'ftps', 'http', 'https'];
      $url = parse_url($filename);
      if ($url && isset($url['scheme']) && !in_array($url['scheme'], $allowed_schemes) && !file_exists($filename)) {
          return false;
      }

      return true;
  }
```

C:\xampp\htdocs\magento2\vendor\magento\framework\View\Element\Template\File\Validator.php
```
 protected function isPathInDirectories($path, $directories) {
     if (!is_array($directories)) {
         $directories = (array)$directories;
     }
     $realPath = $this->fileDriver->getRealPath($path);
     $realPath = str_replace('\\', '/', $realPath); // extra code added
     foreach ($directories as $directory) {
         if (0 === strpos($realPath, $directory)) {
             return true;
         }
     }
     return false; }
```

C:\xampp\htdocs\magento2\app\etc\di.xml
```
<item name="view_preprocessed" xsi:type="object">Magento\Framework\App\View\Asset\MaterializationStrategy\Copy</item>
```

# Linux Environment (Git Bash)
```
ssh vagrant@born.telco-dev.local -p 2222
cd /vagrant/
mysql -u root -p
mag3nt0
show databases;
use borntelco;
select * from core_config_data where path like '%url%' \G;
select * from admin_user \G;
UPDATE admin_user SET password = CONCAT(SHA2('xxxxxxxxadmin@123456', 256), ':xxxxxxxx:1') WHERE username = 'borntelco';	
admin@123456
php bin/magento
php bin/magento admin:user:create --admin-user=born --admin-password=lovemagento123 --admin-email=test@born.com --admin-firstname=born --admin-lastname=family
sudo service nginx status
sudo service php-fpm status
sudo vi /etc/hosts
:q!
```

## Fresh Install
```
rm -rf magentodev/
sudo mkdir magentodev
sudo COMPOSER_MEMORY_LIMIT=-1 composer create-project --repository-url=https://repo.magento.com/ magento/project-community-edition magentodev
php bin/magento setup:install --base-url="http://amgento.dev.local/" --db-host="localhost" --db-name="magento" --db-user="root" --db-password="root" --admin-firstname="admin" --admin-lastname="admin" --admin-email="admin@magento.com" --admin-user="admin" --admin-password="admin123" --language="en_US" --currency="MYR" --timezone="Asia/Kuala_Lumpur" --use-rewrites="1" --backend-frontname="admin" --search-engine=elasticsearch7 --elasticsearch-host="localhost" --elasticsearch-port=9200
```
## Create user in newly created database (To prevent stuck on initializing installation)
```
create database;
create user 'magento'@'localhost' identified by 'root';
grant all privileges on magento.* to 'magento'@'localhost' with grant option;
flush privileges;
```
## Installing php sodium (To prevent stuck on step 1309)
```
cd /usr/src/
sudo yum install php-sodium
```

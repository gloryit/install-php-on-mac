## XCode Command Line Tools

    xcode-select --install

## Homebrew Installation

    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    brew --version

## Install PHP 7.3

    brew install php@7.3

## Apache Installation
Unload apachectl

    sudo apachectl stop
    sudo launchctl unload /System/Library/LaunchDaemons/org.apache.httpd.plist 2>/dev/null

Install httpd

    brew install httpd
    sudo brew services start httpd

## Apache PHP Setup
    
You have successfully installed PHP, but you need to tell Apache to use it. 
Edit the httpd.conf file. 

    vi /usr/local/etc/httpd/httpd.conf 
    
Find **Listen 8080** and change it to port 80:
   
    Listen 80
    
Uncomment the following lines.
	
    LoadModule socache_shmcb_module lib/httpd/modules/mod_socache_shmcb.so
    LoadModule ssl_module lib/httpd/modules/mod_ssl.so
    LoadModule vhost_alias_module lib/httpd/modules/mod_vhost_alias.so
    LoadModule userdir_module lib/httpd/modules/mod_userdir.so
    LoadModule rewrite_module lib/httpd/modules/mod_rewrite.so

Add the following entry at the end of the LoadModules section:
    
    LoadModule php7_module /usr/local/opt/php@7.3/lib/httpd/modules/libphp7.so

Update user and group.

    User username
    Group staff
    
Servername is disabled by default, set it to **localhost**:
    
    #ServerName www.example.com:8080
    ServerName localhost

Modify httpd.conf a bit more.

    <Directory "/usr/local/var/www">
        AllowOverride None
        
    to
    
    <Directory "/usr/local/var/www">
        AllowOverride All

Check that directive DirectoryIndex includes <code>index.php</code>.

    DirectoryIndex index.php index.html
    
And we need to add the FilesMatch directive so that Apache will now process PHP files.

```
<FilesMatch \.php$>
    SetHandler application/x-httpd-php
</FilesMatch>
```
    
Uncomment to enable User home directories, Virtual hosts and Secure (SSL/TLS) connections.

    Include /usr/local/etc/httpd/extra/httpd-userdir.conf
    Include /usr/local/etc/httpd/extra/httpd-vhosts.conf
    Include /usr/local/etc/httpd/extra/httpd-ssl.conf
    
Restart apache.

    sudo apachectl -k restart
    
Run a configuration file syntax test to verify/validate the configuration. It reports Syntax Ok or detailed information about the particular syntax error. This is equivalent to <code>apachectl -t</code>.

    $ sudo apachectl configtest

<blockquote>If it says "Syntax OK" open browser using http://127.0.0.1. You should see a message saying, “It works!”</blockquote>

## SSL/Virtual Hosts

Change default 8443 ports to 443 in the SSL configuration file.

    $ vi /usr/local/etc/httpd/extra/httpd-ssl.conf

Replace all lines that say '8443' with '443'.

    ServerName www.example.com:443

    <VirtualHost _default_:443>
    
In Terminal, restart Apache.

    sudo apachectl restart

## MySQL Installation

Install MySQL with Homebrew.

    brew install mysql@8

Have MySQL start on boot.

    brew services start mysql
    
Finally, let's improve the security of your installation and add a password.

    mysql_secure_installation
    
Restart the MySQL server.

    brew services restart mysql
    
After MySQL Server is started, you can log in:

```sql
mysql -uroot -p'[password]'
```
    
Add to [mysqld] section of my.cnf file (found in /usr/local/etc/ for Homebrew's installation):

    default-authentication-plugin=mysql_native_password
    
At shell prompt:

```sql
mysql -u root -p
    
> ALTER USER 'root'@'localhost'
   IDENTIFIED WITH mysql_native_password
   BY '[password]';

> exit
```
    
Reboot server

    brew services restart mysql

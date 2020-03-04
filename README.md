# The Perfect Web Development Environment for Mac - Homebrew + Apache + PHP + MySQL

### Homebrew Installation
First let's install Homebrew.

    $ /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

###

Homebrew can self-diagnose. Let's see if everything works the way it should.

    $ brew doctor

###
	
### Apache Installation
macOS comes with Apache pre-installed, so we will use it.

    $ sudo apachectl start

Check installation.

    $ which apachectl
    /usr/local/bin/apachectl
    
Start Apache, open browser with http://127.0.0.1 and you should see a message saying “It works!”

    $ sudo apachectl start
	
Remember useful commands.

    $ sudo apachectl start
    $ sudo apachectl stop
    $ sudo apachectl restart
    $ sudo apachectl configtest

### DNSmasq Installation

    $ brew install dnsmasq
    $ cd $(brew --prefix); mkdir etc; echo 'address=/.local.com.br/127.0.0.1' > etc/dnsmasq.conf
    $ sudo cp -v $(brew --prefix dnsmasq)/homebrew.mxcl.dnsmasq.plist /Library/LaunchDaemons
    $ sudo launchctl load -w /Library/LaunchDaemons/homebrew.mxcl.dnsmasq.plist
    $ sudo mkdir /etc/resolver
    $ sudo bash -c 'echo "nameserver 127.0.0.1" > /etc/resolver/local.com.br'

### Apache PHP Setup
    
macOS comes with PHP pre-installed too, but we need to tell Apache to use them. 
We need to edit the /private/etc/apache2/httpd.conf and uncomment these lines...
	
    LoadModule vhost_alias_module libexec/apache2/mod_vhost_alias.so
    LoadModule negotiation_module libexec/apache2/mod_negotiation.so
    LoadModule dir_module libexec/apache2/mod_dir.so
    LoadModule userdir_module libexec/apache2/mod_userdir.so
    LoadModule alias_module libexec/apache2/mod_alias.so
    LoadModule rewrite_module libexec/apache2/mod_rewrite.so
    LoadModule php7_module libexec/apache2/libphp7.so

    Include /private/etc/apache2/extra/httpd-userdir.conf
    Include /private/etc/apache2/extra/httpd-vhosts.conf

Modify httpd.conf more...
    
Check DirectoryIndex includes index.php.

    DirectoryIndex index.php index.html
    
Chage default user to fix permission issues.

    User username
    Group staff

Servername is disabled by default, set it to **localhost**:
    
    #ServerName www.example.com:8080
    ServerName localhost:80
    
Restart apache
    
    $ sudo apachectl -k restart

### Create Document Root

Goal is to create a folder /Users/<username>/Sites If the login name is "teste" then the folder will be /Users/teste/Sites Note that System Root is at /Library/WebServer/Documents/

Create a folder "Sites" under ~/ . Alternatively, use Mac's Finder to create the folder /Users/teste/Sites

    $ cd ~/
    $ mkdir Sites

Creating username.conf

Go to /private/etc/apache2/users/ and create the file

    $ cd /private/etc/apache2/users/
    $ sudo nano username.conf

Edit the file to add following lines -

    $ sudo nano username.conf

    <Directory /Users/<username>/Sites>
        Options Indexes MultiViews FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

Fix the permission of the file

    $ sudo chmod 644 username.conf

Now we need to edit the /private/etc/apache2/extra/httpd-userdir.conf to check the UserDir...

    UserDir Sites

and uncomment this line.

    Include /private/etc/apache2/users/*.conf 

Restart apache
    
    $ sudo apachectl -k restart

### Virtual Hosts

We need to edit the /private/etc/apache2/extra/httpd-vhosts.conf to add the general setup for the virtual host. 

        <VirtualHost *:80>
            VirtualDocumentRoot "/Users/<username>/Sites/%1/wwwroot"
            ServerName local.com.br
            ServerAlias *.local.com.br
            UseCanonicalName Off
        </VirtualHost>

Restart apache
    
    $ sudo apachectl -k restart

### MySQL Installation

This is technically optional. You may prefer another kind of DB, or no DB at all. Feel free to skip this if you don’t need it, but really, you probably do. MySQL no longer comes pre-installed on Mountain Lion as it did with previous versions. [Download it here](http://dev.mysql.com/downloads/mysql/). Pick the latest version of MySQL for the latest version of OS X. There is currently no version specifically for 10.9, but the 10.7 one works fine. Choose the 64bit “DMG Archive” one. You will need to install the main package, the startup item package, and the preferences pane package. All will be included in the DMG. Once this is done, you can start up MySQL from your system preferences.

#### Fonts

https://gist.github.com/offsky/3707280
https://archive.mallinson.ca/os-x-web-development-environment-10-8/
https://mallinson.ca/posts/5/the-perfect-web-development-environment-for-your-new-mac

https://gist.githubusercontent.com/karlhillx/b2ccb9ea1f30ff59505311e1908c3dc4/raw/c2f6ac5d1be1030268613f361f50a19789c02943/macos_high_sierra_apache_php_brew_2018.md

https://gist.githubusercontent.com/w00fz/142b6b19750ea6979137b963df959d11/raw

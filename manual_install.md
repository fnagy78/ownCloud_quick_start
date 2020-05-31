Installing the ownCloud server manually on Linux
===

This quick start procedure is a short version of [Manual installation](https://doc.owncloud.org/server/10.4/admin_manual/installation/manual_installation.html).

To install the ownCloud server, follow these steps:

1. Install the required packages, see the [prerequisite list](https://doc.owncloud.org/server/10.4/admin_manual/installation/manual_installation.html#prerequisites).   
To prepare Ubuntu 18.04 server for ownCloud, follow [this procedure](https://doc.owncloud.org/server/10.4/admin_manual/installation/server_prep_ubuntu_18.04.html).
2. Download the latest tarball or zip archive and its corresponding checksum file from the [download section of the ownCloud website](https://owncloud.org/download/#owncloud-server).
3. Verify the sum of the downloaded archive. Optionally, you can also verify the PGP signature.   
For example:
   ```
   md5sum -c owncloud-x.y.z.tar.bz2.md5 < owncloud-x.y.z.tar.bz2
   wget https://download.owncloud.org/community/owncloud-x.y.z.tar.bz2.asc
   wget https://owncloud.org/owncloud.asc
   gpg --import owncloud.asc
   gpg --verify owncloud-x.y.z.tar.bz2.asc owncloud-x.y.z.tar.bz2
   ```
4. Extract the archive.  
    For example:
    ```
    tar -xjf owncloud-x.y.z.tar.bz2
    unzip owncloud-x.y.z.zip
    ```
    The archive extracts into a separate directory.
5. Copy the ownCloud directory to its final destination.

    **Note:** If you use the Apache HTTP server, you can safely install ownCloud in your Apache document root. For example:
    ```
    cp -r owncloud /var/www
    ```
    For other web servers, install ownCloud outside of the document root.
6. Configure the Apache web server. See [Configuring the Apache web server](#Configuring-the-Apache-web-server). 
7. Run the installation wizard. See [Running the installation wizard](#running-the-installation-wizard).  
8. Set strong directory permissions. Follow [this procedure](https://doc.owncloud.org/server/10.4/admin_manual/installation/installation_wizard.html#post-installation-steps).
9. White-list all URLs used to access your ownCloud server in your `config.php` file, under the trusted_domains setting.

## Configuring the Apache web server

1. For Apache On Debian, Ubuntu, and their derivatives, create an `/etc/apache2/sites-available/owncloud.conf` file with the following content. Replace file paths with your own paths.
    ```
    Alias /owncloud "/var/www/owncloud/"

    <Directory /var/www/owncloud/>
        Options +FollowSymlinks
        AllowOverride All
    
        <IfModule mod_dav.c>
        Dav off
        </IfModule>
    </Directory>
    ```
2. Create a symlink to `/etc/apache2/sites-enabled`:
     ```
     ln -s /etc/apache2/sites-available/owncloud.conf /etc/apache2/sites-enabled/owncloud.conf
     ```
3. Enable the following Apache modules using the `a2enmod` command:
     * Required module: `mod_rewrite`
     * Recommended modules:
       * `mod_headers` - Required if you want to use the OAuth2 app.
       * `mod_env`
       * `mod_dir`
       * `mod_mime`
       * `mod_unique_id`   
         **Note:** After you enable `mod_unique_id`, ensure that the value set for `UNIQUE_ID` in the output of `phpinfo()` is the same as the value in ownCloud’s log file. 
4. Disable any server-configured authentication for ownCloud, as it uses Basic authentication internally for DAV services. If you have turned on authentication on a parent folder, you can disable the authentication specifically for the ownCloud entry. For example, add `Satisfy Any` to the `<Directory>` section of the `owncloud.conf` file.
5. If you use SSL, specify `ServerName` in the server configuration, and in the `CommonName` field of the certificate. If you want your ownCloud to be reachable on the Internet, then set both of these to the domain you want to reach your ownCloud server.
6. Restart Apache:
     ```
     service apache2 restart
     ```
7. If you’re running ownCloud in a sub-directory and want to use CalDAV or CardDAV clients, make sure you have configured the correct `Service Discovery` URLs.
8. Enable SSL:
     ```
     a2enmod ssl
     a2ensite default-ssl
     service apache2 reload
     ```
     **Note:** Apache installed under Ubuntu comes already set-up with a simple self-signed certificate. However, consider getting a certificate signed by a commercial signing authority.
9. Use the Apache prefork Multi-Processing Module (MPM). Do not use a threaded MPM, such as `event` or `worker` with `mod_php`, because PHP is currently not thread safe.

## Running the installation wizard

**Important:** Protect the installation wizard with authentication and access control.
 
To run the graphical installation wizard, follow these steps:

1. Open your web browser, and navigate to http://localhost/owncloud.
2. Enter the administrator’s user name and password that you want to use.
3. Click **Finish Setup**.

To use command line installation, follow these steps:

1. Set your webserver user to be the owner of your unpacked owncloud directory, as in the example below.
    ```
    $ sudo chown -R www-data:www-data /var/www/owncloud/
    ```

2. Run `occ` as your HTTP user. For example, if you have unpacked the source to `/var/www/owncloud/`:
    ```
    $ cd /var/www/owncloud/
    $ sudo -u www-data php occ maintenance:install \
   --database "mysql" --database-name "owncloud" \
   --database-user "root" --database-pass "password" \
   --admin-user "admin" --admin-pass "password"
   ```
3. When the command completes, apply the correct permissions to your ownCloud files and directories.


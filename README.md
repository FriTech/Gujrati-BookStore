# Gujrati-BookStore

Home page for Gujarati Book Store!

-- Steps after installing Opencart (Install with www, e.g. http://www.some-domain.com) --

1. Delete 'install' directory

2. Rename 'admin' directory to '0ff!ce'

3. Update references to "admin" with "0ff!ce" in '0ff!ce/config.php' file

4. Installation of 'vqmod' and accommodating changes related to 'admin' folder renaming:

    * Update "$admin = '0ff!ce'" in 'vqmod/install/index.php'
    * Add following line in 'vqmod/pathReplaces.php':
        // START REPLACES //
        $replaces[] = array('~^admin\b~', '0ff!ce');
        // END REPLACES //
    * Open 'http://www.some-domain.com/vqmod/install' to install vqmod
    * (-Skip this step if vQMod extensions are needed-)
      After installation is successful, revert changes in 'index.php' and '0ff!ce/index.php':
        Comment out:
            // VirtualQMOD
            // require_once('./vqmod/vqmod.php');
            // VQMod::bootup();

            // VQMODDED Startup
            // require_once(VQMod::modCheck(DIR_SYSTEM . 'startup.php'));
        Add:
            // Startup
            require_once(DIR_SYSTEM . 'startup.php');
      # Undo these changes when need vQMod extensions.

5. Perform below changes after logging into admin panel (http://www.some-domain.com/0ff!ce/):

    * Navigate to 'System->Settings->Store->Edit->Server'
    * Select 'Yes' for 'Use SEO URLs' option
    * Change 'Output Compression Level' to 4.
    * Select 'Yes' for 'Use SSL' option
    * Change 'Encryption key' to:
        Xz1AUoWr6FwG4nEHldbAaYxuv4c0WBlqiKweS2A3qXvIcc6R4RadFE56rkA7mM5yYbe2kWhemMpun1rOLyBr4lvC44tNthIddENDem5R0sGnwwBuL9Oe0YaHLEdxmQop2MlUFtfZl11ZI4tMmM25ocHWd82FBia4ogrDpX0TUJh5QVz1oU2rAdnMFOgdH27xpH5Lxu6iLdE8IsKqESLS1AwSJw40aJX6qoO2mlqSgVkYHa4HVmhNgJ9cCcbvoVzyNFz21Nxqiqhn87Ps0LcFDQUKpgkYd4vonCvblfmqEGXJFok4RXqsVtMY1VXebUINiShEeec1MSkkMB6nZnd9nhca0FZw2IPzRnaYT5jxejdsHdcNYlRsVEcIxbwzNeOisxXB9HNleFUhlInbyeupxe9pIC5E90bEjzULDzD2GbomfkrJn4BBvdBXoPRguDAXs3kkJp76PiOIrCwTLnJ20WvRlJr2hfmzJMpdNpNCD36xrmGRpcXuPvzUUsjZBld8mWnaMekRcpKFroqMg2pEy6FDIVyhEiXu7UDgALFdLOlDiTPXQGU1m0oBjnrGnxSDnfM4sCU707f2qzgahNBAwt2gNYzlgvKSFiEMw8SKfDslSQnDh0nIei9GhE8LZa7hFlSvJMPfOfXMAkJtKEU8JTgKiZvyDriPxfLzoEGtI57501yav5lLxREieuyGcI79N9irs7MX9855PqpAdNXXYmCZMRUJGWt2XJOIn6e6PbCNCaeOzQIbJeVR8kvUZbUmo8Ys4u08lByzh1ZEG1ba2HSwa5UXJiHE6jS2gIhHvB5sLEM4xTGStoSAFuLwXjAS85kzvuKWst0053LFE1Mt9LsFJcMlPzU9jefNd1wgCtiA9CJOIkKWiW7c1zGUrugdRjyI78CBUppcQl1ARc3DCr8fe8B329hUIY5X8gFgs4yHiLgKpIj2uAQdsh9Tg4TRmxkf4YqSCE8hwqFWVbRa2hINlcV5btv3BP616cNdTXWgZuru8ojdeXR5r8l5xDAs
    * Select 'No' for 'Display Errors' option, 'Yes' for 'Log Errors' and Rename Log Filename to something less obvious.

6. Make sure apache module "mod_rewrite" is enabled. Rename '.htaccess.txt' file to '.htaccess'. Update '.htaccess' file as below:

    # Prevent Direct Access to files
    <FilesMatch "(?i)(((?<!index)\.php|\.sql|\.tpl|\.xml|\.ini|\.log|(?<!robots)\.txt))">
     Order deny,allow
     Deny from all
    </FilesMatch>

    <Files cache.*>
    Order Deny,Allow
    Deny from all
    </Files>

7. Create a sub-domain for '0ff!ce' directory: 'www.office.some-domain.com'. Setup SSL on this sub-domain. Peform following changes:

    * Update '0ff!ce/config.php':
        define('HTTP_SERVER', 'https://www.office.some-domain.com/');
        define('HTTPS_SERVER', 'https://www.office.some-domain.com/');

    * Add following lines to '.htaccess' file:
        # Redirect admin directory access to admin sub-domain
        RedirectMatch 301 ^/0ff\!ce/(.*)$ https://www.office.some-domain.com/$1

        # Redirect Non-HTTPS OR Non-WWW requests for admin sub-domain to HTTPS, WWW
        RewriteCond %{HTTP_HOST} office\.some-domain\.com$ [NC]
        RewriteCond %{HTTPS} off [OR]
        RewriteCond %{HTTP_HOST} !^www\. [NC]
        RewriteRule .* https://www.office.some-domain.com%{REQUEST_URI} [L,R=301]

        # Redirect Non-WWW requests for store to HTTP, WWW
        RewriteCond %{HTTP_HOST} !office\.some-domain\.com$ [NC]
        RewriteCond %{HTTP_HOST} !^www\. [NC]
        RewriteRule .* http://www.%{HTTP_HOST}%{REQUEST_URI} [L,R=301]

8. Add Apache Authentication for admin panel:

   Create a '.htaccess' file under '0ff!ce' folder. Add these lines to it:
        AuthType Basic
        AuthName "Password Protected Area"
        AuthUserFile C:/wamp/www/opencart/0ff!ce/.htpasswd  #mention full path (refer config.php)
        Require valid-user

   Create an encrypted password by using 'htpasswd' utility by Apache. Go to '{WAMP_DIR}/bin/apache/Apache2.x.x/bin/' and execute:
        > htpasswd -m -n Username
   Provide password when prompted.
   Save the output to '.htpasswd' file inside '0ff!ce' directory

9. Modify file permissions  to 444/644 for below files:

    * config.php
    * index.php
    * admin/config.php
    * admin/index.php
    * system/startup.php


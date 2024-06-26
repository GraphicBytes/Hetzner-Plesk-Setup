_Last Update: **May 2022**_

In this guide we are going to go over a fresh Plesk install to get it ready to host websites and be secure.

### Starting point
 
I’m going to assume that you can connect to the server in question via SSH and have access to Plesk installed on that same server.

# Updates

Plesk will handle all system and OS updates for us, so before we get started make sure the server is up to date. Goto:

    Tools & Settings -> Server Management -> System Updates
###

    Update all
###

I would wait for Plesk to complete the updates, but you don’t have to. But next goto the system updates settings tab;

    Check - Automatically install Plesk updates (Recommended);

    Check - Automatically install updates for third-party components shipped by Plesk

    Uncheck - Automatically install system package updates

    Check - Notify about available and installed system package updates

    Put bright system email in.

    Set Notification email frequency to Weekly

Next, goto:

    Tools & Settings -> Plesk -> Updates

That should have opened up in a new tab.

    Click on “Add/Remove Components” option

This is where we can tell Plesk what we want to use on this server. Go though each tree and make sure the following are all set to be installed, if it’s not mentioned on this list, set to uninstall.

* Plesk
* BIND DNS server – Plesk needs it
* All language localization for Plesk 
* Git
* Plesk Migrator
* MySQL server
* Mail hosting
  * SMTP servers (Postfix) – Needed for plesk to email out
* Web hosting
  * ProFTPD
  * Webalizer
  * AWStats
  * Apache
    * mod_fcgid
    * mod_python
    * Apache
  * PHP interpreter versions
    * PHP 8.1
    * PHP 8.0 – needed for WP installs (as of may 2022).
    * PHP from OS vendor
* Plesk extensions
  * Plesk Web Server Configuration Troubleshooter
  * Plesk Firewall
  * Let's Encrypt
  * ImunifyAV
  * SSL It!

If it wasn’t on the list, mark it to be removed/not installed. 

Next goto:

    Tools & Settings -> General Settings -> Apache Web Server

You should see a long list of checkboxes and a few other options.

    MPM mode: Event

Make sure the following module are checked, all are mostly on by default.

    * access_compat
    * authn_core
    * rewrite
    * status
    * dir
    * filter
    * include
    * proxy_wstunnel
    * headers
    * python
    * suexec
    * auth_basic
    * autoindex
    * expires – NOT SET BY DEFALT
    * proxy_http
    * cgi
    * http2 – NOT SET BY DEFALT
    * reqtimeout
    * socache_shmcb
    * userdir
    * authz_core
    * deflate
    * fcgid
    * negotiation
    * setenvif
    * speling – NOT SET BY DEFALT & USED BY WORDPRESS

    Hit Apply

Next go to:

    Tools & Settings -> General Settings -> PHP Settings

Leave the php versions with “OS vendor” in their name alone, but for the others do the following per PHP version

    Uncheck “CGI application” versions of PHP
    Uncheck “Dedicated FPM application” versions of PHP
    Uncheck “FastCGI application” versions of PHP
    Check “FPM application” versions of PHP

Remember to leave all OS vender versions in their default states.

Next, click on the label of an activated “FPM application” version of PHP, the checkbox labels double up as links.

It should open up on the Extensions tab with a list of checkboxes.

    Uncheck “snmp”
    Uncheck “xdebug”
    Check all other options.

Next goto the php.ini tab, which will give you the raw php config file. Find then set the following specifically, the rest is fine on default:

    ignore_user_abort = On

    max_execution_time = 120

    max_input_time = 300

    memory_limit = 256M – if this isn’t enough, change your code.

    display_errors = Live: Off | Dev: On (ALWAYS FOR DEV)

    post_max_size = Live: 256M | Dev: 9999999M

    file_uploads = On

    upload_max_filesize = 128M

    max_file_uploads = 50

    session.name = SESSID

    opcache.enable=1

    opcache.memory_consumption=512

    opcache.validate_timestamps=1

    opcache.revalidate_freq= Live: 2 | Dev 0

Hit OK and save

If the server is for Live and Dev, use the live values. What we just did was set the server’s defaults, but each individual domain’s PHP settings can be changed if needed for development.

Next goto:

    Tools & Settings -> Security -> Security Policy

The defaults here aren’t ideal so set the following:

    FTPS usage policy: Allow only secure FTPS connections

    Password strength: Strong

Next, go to: 

    Tools & Settings -> Security -> Firewall

If you get an error about Iptables not being installed, log in via SSH and install it with the following:

    plesk installer add --components psa-firewall

    apt-get install iptables

    apt-get install iptables-persistent

Once in, make sure the firewall in enabled, the default settings are good.

Next, go to: 

    Tools & Settings -> Security -> TLS versions and ciphers management -> TLS versions and ciphers by Mozilla

    Check to enable

    Select “modern”



### From here, the server should be good to go Plesk wise, but you will need to use the Server Hardening guide on top of this before going Live.

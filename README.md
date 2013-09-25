Drupal 8 beta on OpenShift
===================

This Git repository helps you get up and running quickly w/ a Drupal
8 beta installation on OpenShift. We do NOT recommend using this in production 
just yet, but hope you will use it to test & give feedback to the Drupal Community.
It defaults to using MySQL, so when creating the application you'll want to select 
and install both MySQL and Cron (for running scheduled tasks). 

    rhc app create drupal8 https://cartreflect-claytondev.rhcloud.com/reflect?github=phase2/openshift-php-fpm mysql cron https://cartreflect-claytondev.rhcloud.com/reflect?github=phase2/openshift-community-drush-master

The first time you push changes to OpenShift, the build script
will download the latest stable version of Drupal (currently 7.x) and
install it into the 'downloads' data directory.  It will then create and
deploy a default profile for your application, using MySQL into your
'sites' directory. Any new modules you add or files uploaded to the site
will be placed under this directory. If you want to reconfigure Drupal
from a clean state, delete the 'sites' directory (you may need to add
write permissions to the sites/default directory, which Drupal
automatically makes readonly) and push a non-significant change to your
application Git repo.

Because none of your application code is checked into Git and lives
entirely in your data directory, if this application is set to scalable
the new gears will have empty data directories and won't serve requests
properly.  If you'd like to make the app scalable, you'll need to:

1. Check the contents of php/* into your Git repository (in the php/*
   dir)
2. Only install new modules via Drush from the head gear, and then
   commit those changes to the Git repo
3. Use a background task to copy file contents from gear to gear

All of the scripts used to deploy and configure Drupal are located in
the [build](.openshift/action_hooks/build) and [deploy](.openshift/action_hooks/deploy) hooks.

Using Drush
-----------

The Drush management tool for Drupal is automatically installed
and you can simply use it while ssh'd into your gear.

    rhc ssh drupal
    drush --help

Drush has many helpful commands for managing your installation.


Running on OpenShift
--------------------

Create an account at http://openshift.redhat.com/

Create a PHP 5.4 application with MySQL and Cron support and the latest Drush.

    rhc app create drupal8 https://cartreflect-claytondev.rhcloud.com/reflect?github=phase2/openshift-php-fpm mysql cron https://cartreflect-claytondev.rhcloud.com/reflect?github=phase2/openshift-community-drush-master --from-code=git://github.com/phase2/drupal-quickstart.git

That's it, you can now checkout your application at:
    http://drupal-$yournamespace.rhcloud.com

Default Credentials
-------------------
<table>
<tr><td>Default Admin Username</td><td>admin</td></tr>
<tr><td>Default Admin Password</td><td>openshift_changeme</td></tr>
</table>

Updates
-------

You can use Drupal's module management UI to download new versions of
modules into your data directory.

Repo layout
-----------

php/ - At deploy time, the build script will symlink this directory to a
directory containing Drupal  
../data - For persistent data  
../data/sites - The data for your Drupal site, including settings.php,
downloaded modules, and uploaded files  
../data/downloads - The most recent version of Drupal.  
deplist.txt - list of pears to install  
.openshift/action_hooks/build - Script that gets run every push, just prior to starting your app  


Notes about layout
------------------

If you create a php directory in your Git repo, it will be served
instead of Drupal.  The build hook automatically symlinks the installed
version of Drupal from your data directory every time you push.  The
runtime directory in your application is automatically recreated each
push, so anything persistent must be in your Git repo or saved and
retrieved from the data directory.


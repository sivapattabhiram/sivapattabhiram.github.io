---
layout: post
title: Drush Installation
---

Today I had to install Drush in my Ubuntu server and setup a cron job to clear the caches every hour. Drush can be installed using apt-get in Ubuntu servers but looking at the issues reported in some forums, I decided to install it by direct download from the git repository.

**Download and extract the Drush package in a local directory.**

```
cd /usr/local/lib/
sudo wget https://github.com/drush-ops/drush/archive/6.6.0.zip
sudo unzip 6.6.0.zip
```

**Give executable permissions to Drush.**

```
    sudo chmod +x /usr/local/lib/drush-6.6.0/drush
```

**Add a symbolic link in the /usr/bin directory.**

```
sudo ln -s /usr/local/lib/drush-6.6.0/drush /usr/bin/drush
```

**Navigate to the site folder and check Drush status.**

```
drush status
```

The cron job to clear the Drupal cache every hour was set as:

```
0 */1 * * * drush --root={path-to-site-folder} cc all
```

Now Drush is executed every hour to clear the caches in the Drupal site. The [Drush repository](https://github.com/drush-ops/drush) in git has excellent documentation on Drush installation.

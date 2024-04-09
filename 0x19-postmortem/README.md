# Postmortem

After investigating a problem with a Ubuntu 14.04 container running an Apache web server, I discovered that GET requests were resulting in 500 Internal Server Error responses instead of the expected HTML file for a Holberton WordPress site.

## Debugging Process

I encountered the issue upon opening the project and being, well, instructed to
address it.

1)Checked running processes with ps aux and found two apache2 processes running correctly.
2)Examined the sites-available folder in /etc/apache2/ and confirmed that the server was serving content from /var/www/html/.
3)Used strace on the root Apache process's PID in one terminal while curling the server in another, but strace didn't provide useful information.
4}Tried strace on the www-data process's PID instead and discovered an -1 ENOENT (No such file or directory) error when trying to access /var/www/html/wp-includes/class-wp-locale.phpp.
5)Searched through files in /var/www/html/ using Vim to find the erroneous .phpp extension, locating it in wp-settings.php (specifically line 137).
6)Removed the trailing p from the line, tested with another curl, and created a Puppet manifest to automate fixing the error.


## Summation

In essence, the issue was caused by a typo in wp-settings.php, where it tried to load class-wp-locale.phpp instead of class-wp-locale.php from the wp-content directory.

## Prevention

Thoroughly test applications before deploying them to catch errors like this sooner.
Implement uptime monitoring services like UptimeRobot.
I also created a Puppet manifest (link) to automatically fix similar errors by replacing phpp extensions with php in /var/www/html/wp-settings.php

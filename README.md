# baikal-ical-to-googlecal
How to import Baikal ical files into google calendar
---
The information in here is based on [this issue](https://github.com/sabre-io/Baikal/issues/875) in the Baikal repository. I just summarize it.

## export a calendar as ical file

1. Login as admin in baikal
2. Create a new user (mail-address can be fictional)
3. Delete default calendar of this user
3. Open a new browser window in "incognito mode". Share the calendar with this user as "read-only".
     1. login with the user, whos calendar should be shared
     2. go to https://your-baikal-url.com/dav.php/calendars/user/calendername/
     3. at the very bottom is an option to share calendars ("share this ressource")
     4. You need to enter email adress of the new user created in step 2. Enter the email including the mailto: prefix.
5. Reload the admin panel. Open user/calendar options (Users and resources), if not already open
6. Click the info button (i) in the calendar section of the new user and copy the URI
7. Add "?export" (sans quotation marks) at the very end of this URL to enable export of ICS. It will look like this: https://your-baikal-url.com/dav.php/calendars/user/calendarname?export

You may now pass the export-URL and the login credentials of the new user to anyone who wants to view the calendar.


## automate the export with php and a cron job

For this step, you need to ensure that the login method of Baikal is set to "basic". Anything else will result in a login error using the script.

export.php
```php
<?php
$source = "https://username:password@your-baikal-url.com/dav.php/calendars/user/calendarname?export";
$destination = "calendarname.ics";
file_put_contents($destination, file_get_contents($source));
?>
```

* username:passwort are the credidential of the new user created above (the one with the read only access to the calendar).
* Do not add a second "https://" after the @.
* calendarname.ics can be any name. The file will be saved in the same folder, where export.php is located.
* export.php can be uploaded/saved in a folder like https://your-baikal-url.com/export on your webserver

Now, create a new crontab either on your own server (if supported), a public provider for crontabs, or any other pc/server you have access to that can run a crontab.
```
# export the calendar to an ical file every 30 minutes
*/30 * * * * curl --request GET 'https://your-baikal-url.com/export/export.php'
```

## import the ical file into google

1. Go to your google account and into your calendar
2. On the left side, find the option to add additional calendars
3. Click the plus symbol and select "By URL"
4. Enter the URL of your Baikal ics file, e.g. https://your-baikal-url.com/export/calendarname.ics";
5. Add the calendar. You can apply colors and give it another name afterwards on thr main google calendar page.

Google will regularly (no idea how often exactly) check the ical file and reload any changes into your calendar.
You will not be able to make changes to your Baikal calendar via this method. **It is read only.**

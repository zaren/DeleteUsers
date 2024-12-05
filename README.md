![DeleteUsers](https://github.com/zaren/DeleteUsers/assets/2085312/68b7a31e-24ff-4de0-9a1e-e30d01573529)
# DeleteUsers

DeleteUsers is available as both a script and an application that will delete user account data off of a macOS machine, excluding any pre-defined users.

## Why?

Managed lab machines have a tendency to run low on space, as users will occasionally leave large amounts of content behind. I put this application together to help streamline the deletion process. Having seen several requests for how to do something like this in the MacAdmins Slack, I decided to make my version of this tool public as sample code. Feel free to tinker with it and modify it to fit your needs. 

This application can be manually run at any time to delete user accounts, while the script can be used to automate the process.

### Disclaimer:

The following code is provide as a community resource and as sample code for testing purposes, and is licensed under the terms of Apache license 2.0. No warranty is expressed or implied. Any results from the execution of the following code are 100% the responsiblity of the downloader and executor.

## Installation

The application can be stored anywhere on the machine, but it is recommended that it be kept in a location only accessible by those with admin access. 

The script should be placed in `/usr/local/bin` by default, as is it managed with the `launchd` utility. The script is also dependant on your systems being managed with Jamf, as it calls `/usr/local/bin/jamf` to perform its work. (This is the only tool I have found to work with Apple's secure token and volume ownership processes.)

**VERY IMPORTANT!** Make sure you edit the script / application BEFORE first run!

DeleteUsers - as the name implies - will delete ALL user accounts on the machine, with the exceptions of the currently logged in user, and those accounts whitelisted within the script. If you have any accounts on the machine that you want to keep, make sure to whitelist them before the first run! 

By default, the line in the script that whitelists those accounts is:

`exclusion_pattern="admin|.localized|Shared|root|loginwindow" ### <-- whitelist users here`

You need to add your admin / testing / other accounts of value to this line by adding additional `account_name` entries to that line with a `|` separating them.

Changes can be made within the executable by editing `/DeleteUsers.app/Contents/Resources/script`.

## Usage - application:

When you launch the application, you will be prompted for admin rights:

<img width="162" alt="admin rights" src="https://github.com/zaren/DeleteUsers/assets/2085312/d0a744c3-e1c8-48bd-a90a-65371fc54cc3">

From there, you will get a pop-up window reminding you to make sure your admin accounts and other important users are whitelisted:

<img width="340" alt="pop-up" src="https://github.com/zaren/DeleteUsers/assets/2085312/465e532c-0a21-4574-896c-0334714d1eac">


Next, the main app window will appear (the "Details" window is closed on first launch, but can be toggled open to see a list of deleted users):

<img width="269" alt="main window" src="https://github.com/zaren/DeleteUsers/assets/2085312/4f30cd1d-9f17-4728-a05e-accf26aecbc4">

Once the application finds a user to delete, it will prompt you again for permission to admin your computer:

<img width="160" alt="admin" src="https://github.com/zaren/DeleteUsers/assets/2085312/d53176a4-a029-4b00-967d-b56972074797">

After all users have been deleted, the script will finish, prompting you to quit the application:

<img width="265" alt="finished" src="https://github.com/zaren/DeleteUsers/assets/2085312/86340a91-c914-4b29-bdde-733cafe50fd0">

----- 

## Usage - script:

The script is managed via `launchd`, so there is some setup involved to get it running. After copying the script to its preferred location (`/usr/local/bin` is a good location) and confirming the whitelist in the script, a .plist file needs to be created to schedule the script's execution. In the example below, the .plist is located in `/Library/LaunchDaemons` and is currently configured to run daily at 3 am:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.example.DeleteUsers</string>

    <key>ProgramArguments</key>
    <array>
        <string>/usr/local/bin/DeleteUsers.sh</string>
    </array>

    <key>StartCalendarInterval</key>
    <dict>
        <key>Hour</key>
        <integer>3</integer>
        <key>Minute</key>
        <integer>0</integer>
    </dict>

    <key>StandardOutPath</key>
    <string>/var/log/DeleteUsers.log</string>
    <key>StandardErrorPath</key>
    <string>//var/log/DeleteUsers.err</string>

    <key>RunAtLoad</key>
    <true/>
</dict>
</plist>
```

Once the .plist is created, the script needs to be actived, which is done by issuing the following command:

`sudo launchctl start /usr/local/bin/DeleteUsers.sh`

If changes need to be made to the script after it has started, its launchd process must be stopped:

`sudo launchctl stop /usr/local/bin/DeleteUsers.sh`

After that, changes can be made, and the script re-activated with the previous `launchctl start` command.

---

The `DeleteUsers.sh` script will delete all non-whitelisted user accounts with no interaction needed once a drive space usage percentage is reached. That percentage is defined in the script under the `limit` variable. 

If the script executes and deletes users, it will send an email through `sendmail` on the local machine. Subject, recipients, and content are defined in the following line:

`echo "To: admin@example.com\ncc: other_admin@example.com\nSubject: Disk usage $usep% on $(hostname)\nLocal storage has exceeded $limit%. The following users were automatically deleted: $deletedUsers" | sendmail -f admin@example.com -t admin@example.com other_admin@example.com`

### Fiddly bits

This is a very blunt object of a script. Once you give it permission to run, accounts WILL be deleted, and will continue to be deleted until /Users has been scanned completely. Please be sure there is no data that is needed to be saved from those accounts, because it won't be there once you set the script loose.

Log files can be found in `/var/log/DeleteUsers.log` for actions performed by the script, and in `/var/log/DeleteUsersGUI.log` for actions performed by the application.

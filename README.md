![DeleteUsers](https://github.com/zaren/DeleteUsers/assets/2085312/68b7a31e-24ff-4de0-9a1e-e30d01573529)
# DeleteUsers

DeleteUsers is available as both a script and an application that will delete user account data off of a macOS machine, excluding any pre-defined users.

## Why?

Managed lab machines have a tendency to run low on space, as users will occasionally leave large amounts of content behind. I put this application together to help streamline the deletion process. Having seen several requests for how to do something like this in the MacAdmins Slack, I decided to make my version of this tool public as sample code. Feel free to tinker with it and modify it to fit your needs. 

This application can be manually run at any time to delete user accounts, while the script can be used to automate the process.

### Disclaimer #1

The following code is provide as a community resource and as sample code for testing purposes, and is licensed under the terms of Apache license 2.0. No warranty is expressed or implied. Any results from the execution of the following code are 100% the responsiblity of the downloader and executor.

### Disclaimer #2

The automated version of this script does not function as intended, due to a permissions issue with user tokens. It will delete user data, but not remove those accounts from Users & Groups, leading to login failures. Running the script manually, or running the GUI app, works as expected. I am continuing to work on a solution to this, but for further assistance in the meantime, see Disclaimer #1.

## Installation

The application can be stored anywhere on the machine, but it is recommended that it be kept in a location only accessible by those with admin access. 

The script is intended to run automatically by way of the `periodic` utility - placing it in /etc/periodic/daily would be good for a higher traffic lab. (See https://www.alansiu.net/2020/08/26/running-daily-weekly-and-monthly-scripts-in-macos-using-periodic/ for more info.)

**VERY IMPORTANT!** Make sure you edit these scripts BEFORE first run! 

This script will delete ALL user accounts on the machine, with the exceptions of the currently logged in user, and those accounts whitelisted within the script. By default, the line in the script that declares those accounts is

`exclusion_pattern="admin|.localized|Shared|root|loginwindow" ### <-- whitelist users here`

You may add your admin / testing / other accounts of value to this line by adding additional `account_name` entries to that line with a `|` separating them.

Changes can be made within the executable by editing `/DeleteUsers.app/Contents/Resources/script`

## Usage

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

The `periodic` script will delete user accounts once a drive space usage percentage is reached, with no interaction needed. That amount is defined under the `limit` variable. 

If the script executes and deletes users, it will send an email through `sendmail` on the local machine. Subject, recipients, and content are defined in the following line:

`echo "To: admin@example.com\ncc: other_admin@example.com\nSubject: Disk usage $usep% on $(hostname)\nLocal storage has exceeded $limit%. The following users were automatically deleted: $deletedUsers" | sendmail -f admin@example.com -t admin@example.com other_admin@example.com`

### Fiddly bits

This is a very blunt object of a script. Once you give it permission to run, accounts WILL be deleted, and will continue to be deleted until /Users has been scanned completely. Please be sure there is no data that is needed to be saved from those accounts, because it won't be there once you set the script loose.

As of version 1.1.0, app activity is now logged for later review.

Log files can be found in `/var/log/555.DeleteUsers.log` for actions performed by the `periodic` script, and in `/var/log/DeleteUsersGUI.log` for actions performed by the application.

As of version 1.2.0, the application will pop up a warning window after granting admin rights, confirming that you actually want to delete users.

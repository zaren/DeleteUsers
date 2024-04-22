![DeleteUsers](https://github.com/zaren/DeleteUsers/assets/2085312/68b7a31e-24ff-4de0-9a1e-e30d01573529)
# DeleteUsers

DeleteUsers is available as both a script and an application that will delete user account data off of a macOS machine, excluding any pre-defined users.

## Why?

Managed lab machines have a tendency to run low on space, as users will occasionally leave large amounts of content behind. This script can be manually run at any time to 
delete user accounts, quickly freeing up space. (An automated version of this is in testing, and may be included in future updates.)

## Installation

The application can be stored anywhere on the machine, but it is recommended that it be kept in a location only accessible by those with admin access. 

The script is intended to run automatically by way of the `periodic` utility - placing it in /etc/periodic/daily would be good for a higher traffic lab.

**VERY IMPORTANT!** Make sure you edit these scripts BEFORE first run! 

This script will delete ALL user accounts on the machine, with the exceptions of the currently logged in user, and those accounts delcared within the script. By default, the line in the script that declares those accounts is

`for home in $(ls /Users | grep -v -e admin -e Shared -e root -e loginwindow)`

You may add your admin / testing / other accounts of value to this line by adding additional `-e account_name` entries to that line.

Changes can be made within the executable by editing `/DeleteUsers.app/Contents/Resources/script`

## Usage

When you launch the application, you will be prompted for admin rights:

<img width="162" alt="admin rights" src="https://github.com/zaren/DeleteUsers/assets/2085312/d0a744c3-e1c8-48bd-a90a-65371fc54cc3">

From there, the main app window will appear (the "Details" window is closed on first launch, but can be toggled open to see a list of deleted users):

<img width="269" alt="main window" src="https://github.com/zaren/DeleteUsers/assets/2085312/4f30cd1d-9f17-4728-a05e-accf26aecbc4">

Once the application finds a user to delete, it will prompt you again for permission to admin your computer:

<img width="160" alt="admin" src="https://github.com/zaren/DeleteUsers/assets/2085312/d53176a4-a029-4b00-967d-b56972074797">

After all users have been deleted, the script will finish, prompting you to quit the application:

<img width="265" alt="done" src="https://github.com/zaren/DeleteUsers/assets/2085312/5b5196f9-9d89-4a07-a51f-d2bab0ef4b96">


The `periodic` script will delete user accounts once a drive space usage percentage is reached, with no interaction needed. That amount is defined under the `limit` variable. 

### Fiddly bits

This is a very blunt object of a script. Once you give it your admin password, accounts WILL be deleted with no further prompting, and will continue to be deleted until /Users has been scanned completely. Please be sure there is no data that is needed to be saved from those accounts, because it won't be there once you set the script loose.

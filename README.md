![DeleteUsers](https://github.com/zaren/DeleteUsers/assets/2085312/68b7a31e-24ff-4de0-9a1e-e30d01573529)
# DeleteUsers

DeleteUsers is available as both a script and a GUI-based executable that will delete user account data off of a macOS machine, excluding any pre-defined users.

## Why?

Managed lab machines have a tendency to run low on space, as users will occasionally leave large amounts of content behind. This script can be manually run at any time to 
delete user accounts, quickly freeing up space. (An automated version of this is in testing, and may be included in future updates.)

## Installation

The application can be stored anywhere on the machine, but it is recommended that it be kept in a location only accessible by those with admin access. 

The script is intended to run automatically bvy way of the periodic utility - placing it in /etc/periodic/daily would be good for a higher traffic lab.

## Usage

By default, this script will delete ALL user accounts on the machine, with the exceptions of the currently logged in user, and those accounts delcared within the script. The line in the script that declares those accounts is

```
for home in $(ls /Users | grep -v -e admin -e Shared -e root -e loginwindow)
```

You may add your admin / testing / other accounts of value to this line by adding 
> -e account_name

to that line.

Changes can be made within the executable by editing 
```
/DeleteUsers.app/Contents/Resources/script
```

The periodic script will delete user accounts once a drive space usage limit is reached. That is defined under the "limit" variable. 

The application just goes to work once admin access is granted, not concerning itself with storage limit checking.

### Fiddly bits

This is a very blunt object of a script. Once you give it your admin password, accounts WILL be deleted with no further prompting, and will continue to be deleted until /Users has been scanned completely. Please be sure there is no data that is needed to be saved from those accounts, because it won't be there once you set the script loose.

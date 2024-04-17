# DeleteUsers

DeleteUsers is available as both a script and a GUI-based executable that will delete user account data off of a macOS machine, excluding any pre-defined users.

## Why?

Managed lab machines have a tendency to run low on space, as users will occasionally leave large amounts of content behind. This script can be manually run at any time to 
delete user accounts, quickly freeing up space. (An automated version of this is in testing, and may be included in future updates.)

## Installation

The application and script can be stored anywhere on the machine, but it is recommended that it be kept in a location only accessible by those with admin access.

By default, this script will delete ALL user accounts on the machine, with the exception of those delcared within the script. The line in the script that declares those accounts is

```
for home in $(ls /Users | grep -v -e admin -e Shared -e root -e loginwindow)
```

You may add your admin and / or testing account to this line by appending 
> -e account_name

to that line.

Changes can be made within the executable by editing 
```
/DeleteUsers.app/Contents/Resources/script
```

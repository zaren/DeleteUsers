#!/bin/sh

echo "Users deleted:"
echo " "
for home in $(ls /Users | grep -v -e admin -e Shared -e root -e loginwindow)
do
    echo $home
    sysadminctl -deleteUser $home
    #sysadminctl -adminUser admin -adminPassword admin_pw -deleteUser $home 
done
echo " "
echo "Process complete."

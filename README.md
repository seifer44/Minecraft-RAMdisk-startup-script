# Minecraft-RAMdisk-startup-script
Run Minecraft from a RAMdisk while preserving the files on a HDD.

This script has been built off of the Minecraft Wiki startup script (located here: http://minecraft.gamepedia.com/Tutorials/Server_startup_script ). This script also backs up the server by pausing saves in the temporary location, backing up from the permanent location, then resuming saves after the backup.

**It is recommended to have your tmpfs mounted on boot via /etc/fstab**

# Variables

*$MCPATH* - Your RAMdisk location where the game exists temporarily.

*$ORIGPATH* - The permanent location where game files are stored.

*$BACKUPPATH* - Where you wish to have archived backups stored.

# Optimal functionality
* In the $USERNAME's crontab, ensure a ramsave is ran regularly.

# Issues
* Backups do not appear to function correctly when executed from a user other than $USERNAME. I'm working on a fix for that still.
* If you wish to have the game start from boot, I would recommend launching the service via crontab instead of in chkconfig. This ensures that the tmpfs has been mounted. I have yet to engineer a workaround into the script. This also means that, upon shutdown, the game is not closed appropriately. Please shut down the game manually before reboot.
* Relies upon the online.sh script for checking if users are online.

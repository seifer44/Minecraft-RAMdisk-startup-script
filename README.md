# Minecraft-RAMdisk-startup-script
Run Minecraft from a RAMdisk while preserving the files on a HDD.

This has been ran on CentOS 6.5+. This should work on other Linux distributions.

This script has been built off of the Minecraft Wiki startup script (located here: http://minecraft.gamepedia.com/Tutorials/Server_startup_script ).

**It is recommended to have your tmpfs mounted on boot via /etc/fstab.** I would also highly recommend a monitoring solution to watch the tmpfs's free space, such as with Nagios, to ensure that disk space is not filled.

# Features
* This script has a permanent backup to a second location. The primary location can be to the RAMdisk or another harddrive.
* Backups are capable of being ran without shutting down the game server. Backups can still be ran if the server is offline.
* The Minecraft server log spam is minimal when paired with the mconline.sh script (located here https://github.com/seifer44/mconlineusers ). In-game saves are not disabled and re-enabled during the rsync process if 0 users have logged in since the last RAMsave. **Note: this may need to be modified for servers that are not running vanilla jars that have extra log output. Review lines 137-143 & 147.**

# Extra Variables

*$MCPATH* - Your RAMdisk location where the game exists temporarily.

*$ORIGPATH* - The permanent location where game files are stored.

*$BACKUPPATH* - Where you wish to have archived backups stored.

# Optimal functionality
* In the $USERNAME's crontab, ensure a ramsave is ran regularly. Example for hourly saves:
  0 * * * * /sbin/service minecraft ramsave > /var/log/minecraft/ramsave.log
* In the $USERNAME's crontab, ensure a full backup is ran regularly (REFER TO ISSUE #1). Example of daily saves:
  5 5 * * * /sbin/service minecraft backup > /var/log/minecraft/backup.log
* You may need to edit SELinux permissions for scheduling rsync in the crontab if it's active in your distribution.

# Issues
1. Backups do not appear to function correctly when executed from a user other than $USERNAME. I'm working on a fix for that still.
2. If you wish to have the game start from boot and you're mounting via fstab, I would recommend launching the service via crontab under the user instead of in chkconfig. This ensures that the tmpfs has been mounted before starting the server. I have yet to engineer a workaround into the script. If goign this route, this means that, upon shutdown, the game is not closed appropriately. Please shut down the game manually before reboot.
3. Relies upon the mconline.sh script (located here https://github.com/seifer44/mconlineusers ) for checking if users are online.
4. The rsync command with the --delete switch can cause issues if the server has a severe crash and data is lost in either location. This switch can be removed if necessary, but should be re-enabled during server upgrades.

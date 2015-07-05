# Installation Procedure

**Note:** This is crafted specifically for Ubuntu 14.04. This should work on Ubuntu 12.04 without too much modifying.
More work may be required for other Linux distributions.

# Service user

1. Log into your Ubuntu server as the user that will be running the Minecraft service. This is likely **minecraft**.
2. Create a main folder, a harddrive folder, a RAMdisk folder, a backup folder, and a log file. Ensure that the
   service user has ownership and read/write/execute permissions on these folders/files.
   Defaults for the script are:

        /opt/minecraft                  <- Root folder
        /opt/minecraft/origpath         <- Harddrive path
        /opt/minecraft/service          <- RAMdisk path
        /opt/minecraft/backups          <- Backup path
        /var/log/minecraft              <- Log file for this script

3. Download minecraft_server.jar, place it in the original location, and execute it manually once.
   Then edit and accept the EULA. If this is not done, you may have lock files for a process that
   terminated automatically!
4. Put the following entries into the cron table by executing *crontab -e*.
   By default, this will run RAMsave once an hour and will backup at 5:05 AM daily.

        0 * * * * /etc/init.d/minecraft ramsave
        5 5 * * * /etc/init.d/minecraft backup
  
   You may want to log this output to a different file on the chance you need it, since it's not
   sent to mail by default like it is in CentOS. If you use the example below, be sure that it's already
   present and that the $serviceusername has permissions for it.
  
        0 * * * * /etc/init.d/minecraft ramsave >> /var/log/minecraftcron.log
        5 5 * * * /etc/init.d/minecraft backup >> /var/log/minecraftcron.log


# Root user

1. Log into your CentOS server as the **root** user.

2. Edit /etc/fstab and add the following for the RAMdisk.
   Be sure to edit the RAMdisk path and size as appropriate for your server needs. Also update
   the user ID and group ID if not using the script defaults.
   **BE SURE YOU HAVE THIS AMOUNT OF RAM AVAILABLE AT ALL TIMES.**

        tmpfs                   /opt/minecraft/service          tmpfs   defaults,size=2560m,uid=minecraft,gid=minecraft 0 0

3. Create */etc/init.d/minecraft* and populate it with the Minecraft RAMdisk startup script. Ensure it has execute permissions.
4. Create the log rotation file under /etc/logrotate.d/minecraft and populate it with the following:

         /var/log/minecraft {
            compress
            size 20k
            daily
            create 0600 minecraft minecraft
         }

5. OPTIONAL: Have the service kick on during system startup by running *update-rc.d minecraft defaults 30*.

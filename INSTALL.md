# Installation Procedure

**Note:** This is crafted specifically for CentOS 6.5+. This should work on CentOS 5 without too much modifying.
More work may be required for other Linux distributions.

# Service user

1. Log into your CentOS server as the user that will be running the Minecraft service. This is likely **minecraft**.
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

        0 * * * * /sbin/service minecraft ramsave
        5 5 * * * /sbin/service minecraft backup


# Root user

1. Log into your CentOS server as the **root** user.
2. Add the following lines to your sudoers file by executing *visudo*.

        minecraft       ALL=(ALL)       NOPASSWD: /bin/touch /var/lock/subsys/minecraft
        minecraft       ALL=(ALL)       NOPASSWD: /bin/rm -f /var/lock/subsys/minecraft

3. To configure SELinux appropriately, (you **ARE** using SELinux right?) execute the following.
   Update the origpath location if you're not using the script default.

        semanage fcontext -a -t public_content_rw_t '/opt/minecraft/origpath(/.*)?'
        setsebool -P allow_rsync_anon_write on
        setsebool -P rsync_export_all_ro on
        restorecon -R /opt/minecraft/origpath/

4. Edit /etc/fstab and add the following for the RAMdisk. This includes SELinux permissions.
   Be sure to edit the RAMdisk path and size as appropriate for your server needs. Also update
   the user ID and group ID if not using the script defaults.
   **BE SURE YOU HAVE THIS AMOUNT OF RAM AVAILABLE AT ALL TIMES.**

        tmpfs                   /opt/minecraft/service          tmpfs   defaults,size=2560m,uid=minecraft,gid=minecraft,fscontext=system_u:object_r:public_content_rw_t:s0 0 0  

5. Create */etc/init.d/minecraft* and populate it with the Minecraft RAMdisk startup script. Ensure it has execute permissions.
6. OPTIONAL: Have the service kick on during system startup by running *chkconfig minecraft on*.

# Minecraft-RAMdisk-startup-script
Run Minecraft from a RAMdisk while preserving the files on a HDD.

This has been ran on CentOS 6.5+. This should work on other Linux distributions with a
bit of modification, as the majority of the script is written in bash.

This script has been built off of the Minecraft Wiki startup script
(located here: http://minecraft.gamepedia.com/Tutorials/Server_startup_script ).

**It is recommended to have your tmpfs mounted on boot via /etc/fstab.** I would also
***highly*** recommend a monitoring solution to watch the tmpfs's free space, such as with Nagios,
to ensure that disk space is not filled.

# Features
* Two types of locks are used in this script. 1) Prevent startup/shutdowns if the script appears to
  be running/offline already, and 2) prevent other jobs from running if a job is already executing. This is
  to prevent an rsync between both folders if you're already in the middle of shutting down the server,
  for example (in this instance, you could've lost game data!).
* Engineered for RAMdisks/tmpfs. Paired with a cronjob that keeps a backup copy on a HDD in case of an
  OS failure.
* Graceful shutdown and (if needed) process kills to prevent systems from hanging during shutdown procedures.
* Engineered with paired SELinux rules (Refer to INSTALL.md).
* Verbose logging paired with logrotation (Refer to INSTALL.md).
* Easy printout of the number of users online via *service minecraft status*.
* Easy service restarts to prevent from emptying the RAMdisk if the game needs to simply be reloaded.
* Minimal in-game Minecraft logspam by not saving if no users have been online recently. RAMsaves can be
  forced if necessary.
* Full offline and online backups in .tar.gz format.
* Minecraft server command execution from system CLI.
* A few other integrity checks.

# Extra Variables

*$mcpath* - Your RAMdisk location where the game exists temporarily.

*$origpath* - The permanent location where game files are stored.

*$backuppath* - Where you wish to have archived backups stored.

*$lockfile* - OS recognized lock file.

*$pidfile* - PID file for Minecraft kills (if necessary).

*$job_lock* - Lock file to prevent cron entries (or other users) from executing jobs during another job.

# Recommended Software
It is **highly** recommended to have an alert configured if your RAMdisk is low on disk space! A service such as
Nagios is recommended, although you could also write a cron job to email alerts as well.

# Issues
1. The rsync command with the --delete switch can cause issues if the server has a severe crash and data is lost
   in either location. This switch can be removed if necessary, but should be re-enabled during server upgrades.
2. Need integrity checks for users that don't have appropriate permissions to su to the $gameservice user.
3. Need to thoroughly test script for users that don't have appropriate permissions for service files.
4. Backup cleanup task needs to be added.

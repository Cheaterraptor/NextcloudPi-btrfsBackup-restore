# NextcloudPi BTRFS Snapshot restore guide

This is just a quick reminder for myself on how to restore a btrfs Snapshot on Nextcloudpi.

Our setup is the following: 

- Raspberry pi 4b+ 8gb, 
- 2x2tb hard drive connected via usb (They reside in a usbdock)
- 32gb microsd card

Nextcloud for raspberry pi from https://github.com/nextcloud/nextcloudpi/releases/

Name of hard drives (mounted to /media): 
- NextcloudHDD1 (The data directory and database directory) 
- NextcloudHDDBackUp (Backup for the btrfs snapshots, and ncp config)

![Alt text](img\system-info.png "Title")


This configuration assumes that the nextcloudpi installation is broken and the first hard drive NextcloudHDD1 is still working.

Steps to take:
First, setup a new Nextcloudpi installation. Then restore ncp config from NextcloudHDD1. 
Then (?) 

2.	Nc-restore 

<h3> To restore the nextcloud data directory, do the following: </h3>

1.	Create a read-only snapshot:
``` 
btrfs subvolume snapshot -r /media/NextcloudHDDBackpu/snapshots/name_of_snapshot /media/NextcloudHDDBackpu/snapshots/data-ro
```
2. Delete data on NextcloudHDD1 

3. Copy the snapshot: 
```
btrfs send /media/NextcloudHDDBackpu/snapshots/data-ro| btrfs receive /media/NextcloudHDD1
```

4. Make snapshot Read write Snapshot 
```
btrfs subvolume snapshot /media/NextcloudHDD1/data-ro /media/NextcloudHDD1/data 
```

4.	Turn maintenance mode off from web ui
5.	Scan for the files 
```
sudo -u www-data php /var/www/nextcloud/occ files:scan --all
```


Some notes:



Btrfs subvolume snapshot move etc. 
https://www.reddit.com/r/btrfs/comments/qfcmpe/moving_subvolume_to_different_drive/
scan for the files 
https://help.nextcloud.com/t/files-and-directories-not-displaying-solved/12865
general manual for backup and restore 
https://help.nextcloud.com/t/how-to-backup-and-restore-using-nc-snapshot/126377


An exception occurred while executing a query: SQLSTATE[40001]: Serialization failure: 1213 Deadlock found when trying to get lock; try restarting transaction

The logfile of Nextcloud is located in the data directory /var/www/nextcloud/data/nextcloud.log.

/etc/cron.d/ncp-snapsync-auto 
Htop display cpu ram 

# Filesystem and Disk Management Automation
In this project, i automate disk and filesystem management, implement dynamic disk management using Logical Volume Manager (LVM), set up backups, and monitor disk usage with alerts.This is a comprehensive system administration task that enhance efficiency and scalability for disk and filesystem management on Linux servers.

 Objective:
 
 The goal of this project is to:
 
 1- Automate the creation, management, and monitoring of disk partitions and filesystems.
 
 2- Set up automated backups using rsyn and cron.
 
 3- Monitor disk usage and recieve alerts when critical thresholds are reached.
 
 4- Implement LVM for flexible and dynamci disk management.
 
 Prerequisites
 * Basic knowledge of Linux commands and shell scripting.
 * Root or sudo privileges on the server to make system-level changes.
 * Ensure rsync, cron and lvm2 (if using LVM) are installed:
 
 -> sudo apt updatee
 -> sudo apt install rsync cron lvm2
 
 Step 1: I write a script to Manage Disk partitions and Filsystem
 
 In this step, i create a shell script to automate the creation and management of partitions, as well as mount them as rrequired.
 
 1- I identify the Disk for partitioning:
 List all disks to determine the target disk:
 
 -> lsblk
 
 2- I create the script to partition the  Disk:
 
 * I use fdisk or parted to automate partition creation.Here's an example using parted:
 
 #!/bin/bash
 
 DISK="/dev/sdb" # specify the disk to partition
 
 echo "Creating partition on $DISK..."
 parted $DISK mklabel gpt
 parted -a optimal $DISK mkpart primary ext4 0% 100%
 
 # Format the partition
 mkfs.ext4 ${DISK}1
 
 # Create a mount point and mount the partition
 
 mkdir -p /mnt/data
 mount ${DISK}1 /mnt/data
 echo "${DISK}1 /mnt/data ext4 defaults 0 0" >> /etc/fstab  # make it persisst on reboot
 echo "partition created and mounted at /mnt/data."
 
 
 3- Run the script:
 -> chmod +x partition_script.sh
 -> sudo ./partition_script.sh
 
 
 Step 2: I automate backups using rsyn and cron
 
 Using rsync with cron, i can automate file backups to a designated backup directory.
 
 Details steps
   * I create a script to back up /mnt/data (my newly mounted partition) to /backup/data:
   
   #!/bin/bash
   
   SRC="/mnt/data"
   DEST="/backup/data"
   LOG"/var/log/backup.log"
   
   mkdir  -p $DEST
   rsync -av --delete $SRC $DEST >> $LOG 2>&1
   echo "Backup completed on $(date)" >> $LOG
   
   
   2 - I set up a Cron job for automated Backups:
   
   * Open the cron file to schedule the backup:
   
   -> crontab -e
   
   * Schedule the backup to run daily at midnight:
   -> 0 0 * * *  /path/to/backup_script.sh
   
   
   3- Test the script:
   * Run the script manually to ensure it works as expected:
   
   -> sudo ./backup_script.sh
   
   
   step 3: I monitor Disk usage and send alerts
I can use df and du commands to monitor disk usage and set up email alerts if usage exceeds a certain treshold.

   
   Details steps:
   
   1- I create a Disk Monitoring script:
     * I set up a treshold (e.g., 80%) for the disk space usage warning.
     
   Here's an example script:
   
   #!/bin/bash
   
   THRESHOLD=80
   EMAIL="admin@example.com"
   
   # Check disk usage
   USAGE=$(df /mnt/data | awk 'NR==2 {print $5}' | sed 's/%//')
   
   if [ "$USAGE" -gt "THRESHOLD" ]; then
      echo "DISK usage on /mnt/data has reached $USAGE%." | mail -s "Disk Space Alert" $EMAIL
    
   fi
   
   
   2- I set up a cron job for Disk MOnitoring:
   * Schedule the script to run every hour:
   
   -> crontab -e
   
   Add the following line:
   
   -> 0 * * * * /path/to/disk_monitoring_script.sh
   
   3 - I test the Disk Monitoring script:
   * Manually run the script and check my email for any alerts:
   
   -> sudo ./disk_monitoring_script.sh
   
   
   Step 4: I implement LVM for dynamic Disk Management
 
 LVM allows me to manage disk space more flexibly, adding and resizing volumes as needed.
 
   
   Detailed steps
   1- Initialize physical volumes (PVs)
   
    * I select the disk (e.g., /dev/sdb) to convert into a PV:
    -> sudo pvcreate /dev/sdb
    
    2- I create a volume Group (VG):
    * i create a VG named data_vg from the physical volume:
    
    -> sudo vgcreate data_vg /dev/sdb
    
    
    3 - I create a LOgical Volume (LV):
    * i create an LV named data_lv from the volume group, allocating, for example, 20GB:
    
    -> sudo lvcreate -L 20G -n data_lv data_vg
    
    
    4- Frmat and Mount the Logical Volume:
    * Format the LV and mount it to  /mnt/lvm_data:
    
    sudo mkdfs.ext4 /dev/ddata_vg/data_lv
    sudo mkdir -p  /mnt/lvm_data
    sudo mount /dev/data_vg/data_lv  /mnt/lvm_data
    
    echo "/dev/data_vg/data_lv  /mnt/lvm_data ext4 defaults 0 0"
    
    5 - Expanding the Logical Volume:
    
    * To increase the size of the LV, i use the following commands:
    
    sudo lvextend -L +10G /dev/data_vg/data_lv
    sudo resize2fs  /dev/data_vg/data_lv
    
    
    6 - I verify LVM configuration:
    * I confirm the configuration by running:
    
    sudo lvs
    sudo vgs
    sudo pvs
    
    
    
    Summary
    
    * Partitioning & Filesystem: Scripted partition creation, formatting, and mounting.
    
    * Backup Automation: Automated rsync backups via cron.
    
    * Disk usage monitoring: scripted disk usage alerts with email notifications.
    
    * Dynamic Disk management with LVM: LVM setup for flexible storage management.
    
  With this project, i have established an automated, flexible, and secure disk management sytem suited for production environments, ready to handle storage expansion, automated backups, and disk health monitoring. THis setup ensure that my storage resources are utilized efficiently and that administrators are notified before any issues become critical.
      
   
   


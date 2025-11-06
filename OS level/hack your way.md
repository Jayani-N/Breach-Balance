# hack your way(400 pts)


## Desc:

    connect to B&B, ip will be notified later!
    
    username-player
    pswd- breach&balance
    
    ip-192.168.231.200



    hint-1	
    port scanning helps!!
    hint-2	
    there are cron jobs running
    /opt/scripts
    hint-3	
    scripts have some access permissions


## Solution:

     The player runs ls -l /opt/scripts/backup.sh and sees they have write access. They edit the file and add a reverse
    
    shell one-liner ( bash -c '...' ). They start a netcat listener, wait one minute, and catch a root shell, letting them read /root/flag.txt

## Flag

    bab{CRON_JOB_HIJACK}

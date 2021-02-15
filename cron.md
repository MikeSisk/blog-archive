---
title: "Cron and Sewing Needles"
date: 2011-06-13
draft: false
---

Sometimes, even after decades of experience, you still screw up.

Consider this cron entry I put in last night:

```
    # Backup MongoDB every 6 hours, zip it up, and rsync it.
    * */6 * * * ~/bin/backup_mongo.sh
```

I wanted this to run the backup script for MongoDB every six hours. Instead, 
I got it running every minute for an hour every six hours. You’d think I’d 
know better considering I put the next cron in correctly:

```
    # Remove MongoDB backups that are more than 24-hours old.
    00 02 * * * find /db/backup -mtime +1 -exec rm -f {} \;
```

What I meant to do is this:

```
    # Backup MongoDB every 6 hours, zip it up, and rsync it.
    00 */6 * * * ~/bin/backup_mongo.sh
```

Luckily we host our infrastructure at Engine Yard and their staff noticed the 
CPU spike on this server at midnight and fixed the cron.

Which brings up another point: name your scripts appropriately. In this case 
a quick scan of cron revealed this script was running a backup and doing that 
every six hours makes sense. If the script was just named mongo, it’s 
conceivable it could have been a metric collection script that runs every 
minute for an hour every six hours.

So what do sewing needles have to do with cron? I’m working from home this 
week and had just finished that MongoDB backup script and was putting it in 
cron when my daughter came running (Ok, make that hopping) into my office 
with a large sewing needle in-bedded about 1/4″ deep in the arch of her foot. 
I quickly saved the cron entry to take care of that problem and didn’t go 
back to check my work.

Moral of the story: whenever you set up a new cron job it’s a good idea to 
watch it run and see if it’s doing what you think it is. Especially if you 
think you know what you’re doing.

&#x269B;

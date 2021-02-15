---
title: "s3cmd with Multiple AWS Accounts"
date: 2011-08-09
draft: false
---

Awhile back I was doing a lot of work involving Amazon's Simple Storage Service 
(aka Amazon S3).

And while tools like Panic's Transmit, the Firefox S3Fox extension, or even Amazon's 
own S3 Management Console make it easy to use, sometimes you really just want a 
command-line tool.

There's a lot of good tools out there, but the one I've been using is s3cmd. This 
tool is written in Python and is well documented. Installation on Linux or OS X is 
simple as is its configuration. And as a longtime Unix command-line user it's 
syntax is simple. Some examples:

To list your buckets:

```
$ s3cmd ls
2010-04-28 23:50 s3://g5-images
2011-01-21 06:42 s3://g5-mongodb-backup
2011-03-21 21:23 s3://g5-mysql-backup
2010-06-03 17:45 s3://g5-west-images
2010-09-02 15:57 s3://g5engineering
```

List the size of a bucket with "human readable" units:

```
$ s3cmd du -H s3://g5-mongodb-backup
1132G s3://g5-mongodb-backup/
```

List the contents of a bucket:

```
$ s3cmd ls s3://g5-mongodb-backup
2011-08-08 14:43 3273232889 s3://g5-mongodb-backup/mongodb.2011-08-08-06.tar.gz
2011-08-08 21:12 3290592536 s3://g5-mongodb-backup/mongodb.2011-08-08-12.tar.gz
2011-08-09 03:16 3302734859 s3://g5-mongodb-backup/mongodb.2011-08-08-18.tar.gz
2011-08-09 09:09 3308369423 s3://g5-mongodb-backup/mongodb.2011-08-09-00.tar.gz
2011-08-09 14:51 3285753739 s3://g5-mongodb-backup/mongodb.2011-08-09-06.tar.gz
```

Show the MD5 hash of an asset:

```
$ s3cmd ls --list-md5 s3://g5-mongodb-backup/mongodb.2011-08-09-06.tar.gz
2011-08-09 14:51 3285753739 07747e3de16138799d9fe1846436a3ce \
s3://g5-mongodb-backup/mongodb.2011-08-09-06.tar.gz
```

Transferring a file to a bucket uses the get and put commands. And if you forget 
an option or need a reminder of usage the very complete `s3cmd --help` output 
will likely be all the help you need.

One problem I have with most tools for AWS is managing multiple accounts. Most 
of these tools assume you have just one account, but I work with multiple accounts 
and switching between them can be cumbersome.

Here's how I work with multiple AWS accounts using s3cmd.

By default s3cmd puts its configuration file in ``~/.s3cfg``, but you can 
override this and specify a configuration file with the -c option.

What I do is create a separate config file with the appropriate credentials 
for each account I work with and give them unique names:

```
$ ls -1 .s3cfg*
.s3cfg-g5
.s3cfg-tcp
```

Another option is to keep the credentials for the account you use most often 
in the standard `~/.s3cfg` file and use the -c option when/if you need 
another account. I don't like this option because it's too easy to mistakenly 
use the wrong account. For example, without a `~/.s3cfg` this is what happens 
when I use s3cmd without specifying a configuration:

```
$ s3cmd ls
ERROR: /Users/mike/.s3cfg: No such file or directory
ERROR: Configuration file not available.
ERROR: Consider using --configure parameter to create one.
```

So, what to do? Using the -c all the time is a PITA. Answer: use Bash aliases!

Here's a subset of the s3cmd aliases I have in my `~/.bashrc` file:

```
alias s3g5='s3cmd -c ~/.s3cfg-g5'
alias s3tcp='s3cmd -c ~/.s3cfg-tcp'
```

Now, to list the buckets in my personal account I just do:

```
$ s3tcp ls
2011-07-01 06:10 s3://mikesisk-img
2011-07-05 23:16 s3://www.tcpipranch.com
2011-07-01 22:55 s3://www.watch4rocks.com
```

And I can still pass arguments:

```bash
$ s3tcp -H --list-md5 ls s3://mikesisk-img/me.jpg
2011-07-01 06:09 5k 13d7c86bccd8915dd93b085985305394 s3://mikesisk-img/me.jpg
```

Just keep in mind that calls to bash aliases from scripts and cronjobs might 
not work. Plus it's bad form and *will* come back to bite you one of these days. 
Just use the long form with -c in these places and keep the aliases for your 
own interactive command-line usage.

&#x269B;

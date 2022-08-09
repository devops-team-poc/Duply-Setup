# Task Description :

- Duply setup for automatic backup 

---

## Install Duply and setup :

```sh
# Duplicity Installation
sudo apt-get update -y
sudo apt-get install -y duplicity
apt-cache policy duplicity

# Duply Install
sudo apt update
sudo apt -y install duply
```
## Profile creating 

It's time to create a new duply profile.

```sh
duply ongraph create
```
Here `ongraph` is the profile name.
It will create `~/.duply/ongraph` folder where all the configuration will be stored.
Here we can see the `conf` file here.
Here is my configuration file.

```sh
#GPG_KEY='disabled'
GPG_PW='duply@og'
GPG_OPTS=''
# s3 location to store the backups
TARGET='s3://s3.amazonaws.com/duply-akki-latest/server_data'
# aws creds
TARGET_USER='AKIAZQCRK****6H3'
TARGET_PASS='BziWRR9MZ+g5*****gJDWpbO0YW2Wqbsv'
# source is the path which we want to take backup of
SOURCE='/var/www/html'
MAX_AGE=14D
MAX_FULL_BACKUPS='1'
MAX_FULLBKP_AGE=1M
DUPL_PARAMS="$DUPL_PARAMS --full-if-older-than $MAX_FULLBKP_AGE " 
VOLSIZE=250
DUPL_PARAMS="$DUPL_PARAMS --volsize $VOLSIZE "
VERBOSITY=3
```

Here `TARGET` is the location where our all the backups will be stored.
Now we need to install some more dependencies for authentication

```sh
sudo apt install python3-pip
pip3 install boto
```
Now we are good to use duply.
Now to check the status or to take backup we need to export the creds of aws

```sh
export AWS_ACCESS_KEY_ID="AKI******WHER6H3"
export AWS_SECRET_ACCESS_KEY="BziWRR**********pbO0YW2Wqbsv"

# Check duply status
duply ongraph status

# Take incremental backup
duply ongraph backup

# To take full backup
duply ongraph pre_full_purge
```
Now let's create cronjob for automatic backups :
- Incremental backup every day
- Full backup on every Saturday

For that i created two files :
`data_full` and `data_increment` and added them in `cron.d/backup` file.

# Content of `data_full` file:

```sh
#!/usr/bin/bash
export AWS_ACCESS_KEY_ID=AKIA*******6H3
export AWS_SECRET_ACCESS_KEY=BziWRR9******2Wqbsv
duply ongraph pre_full_purge
unset AWS_ACCESS_KEY_ID
unset AWS_SECRET_ACCESS_KEY
```

# Content of `data_increment` file:

```sh
#!/usr/bin/bash
export AWS_ACCESS_KEY_ID=AKIA*******6H3
export AWS_SECRET_ACCESS_KEY=BziWRR9******2Wqbsv
duply ongraph backup
unset AWS_ACCESS_KEY_ID
unset AWS_SECRET_ACCESS_KEY
```

Here is the content of `/etc/cron.d/backup` file :

```sh
0 0 * * Sat root  bash /root/duply/data_full;
0 0 * * Sun-Fri root bash /root/duply/data_increment;
```

---

# **Thank You**

I hope you find it useful. If you have any doubt in any of the step then feel free to contact me.
If you find any issue in it then let me know.

<!-- [![Build Status](https://img.icons8.com/color/452/linkedin.png)](https://www.linkedin.com/in/choudharyaakash/) -->


<table>
  <tr>
    <th><a href="https://www.linkedin.com/in/choudharyaakash/" target="_blank"><img src="https://img.icons8.com/color/452/linkedin.png" alt="linkedin" width="30"/><a/></th>
    <th><a href="mailto:choudharyaakash316@gmail.com" target="_blank"><img src="https://img.icons8.com/color/344/gmail-new.png" alt="Mail" width="30"/><a/>
</th>
  </tr>
</table>



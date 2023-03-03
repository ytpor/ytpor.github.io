---
title: 'Various Backup Scripts'
date: 2016-03-21T12:00:00.000+08:00
draft: false
url: /2016/03/various-backup-scripts.html
tags:
- backup
---

Here are some sample backup scripts that I've used to back up various things in our network. These scripts uses Amazon S3 as the storage, and also s3cmd.

### Application

```
#!/bin/sh
# Site Backup script
#
# application_backup.sh

# Initialize variables specific for this server
# To exclude directory, update in code under Exclude directory
LOG_FILE="/var/log/site-backup.log"
SITE_PATH="/var/www/"
SITE_BACKUP_PATH="/var/script/backup"
SITE=(
 name_of_directory_one
 name_of_directory_two
)

# Definition
TIMESTAMP=`date "+%Y-%m-%d %H:%M:%S"`
CURRENT_YEAR=`date "+%Y"`
CURRENT_MONTH=`date "+%Y-%m"`
TODAY_DATE=`date "+%Y-%m-%d"`
S3_PATH=s3://name-of-s3-bucket/application/${CURRENT_YEAR}/${CURRENT_MONTH}

# Remove Site Backup older than 15 days
/usr/bin/find ${SITE_BACKUP_PATH} -type f -mtime +15 -delete

echo "Site Backup Log: " ${TIMESTAMP} >> ${LOG_FILE}
echo -e "--------------------------------------------" >> ${LOG_FILE}
echo -e "" >> ${LOG_FILE}

# Loop through the Site Repository
for i in ${SITE[@]}
do
 # Exclude directory
 EXCLUDE=''
 case $i in
  account.fxprimus.com) exclude="--exclude api --exclude assets \
   --exclude nfiles --exclude nimages \
   --exclude nlanguages --exclude ntemplates";;
 esac

 # Backup Site
 cd ${SITE_PATH}
 tar -zcf ${SITE_BACKUP_PATH}/$i-${TODAY_DATE}.tar.gz . ${EXCLUDE}

 # Transfer the file to Amazon S3
 s3cmd put --acl-private --guess-mime-type \
  ${SITE_BACKUP_PATH}/$i-${TODAY_DATE}.tar.gz \
  ${S3_PATH}/$i-${TODAY_DATE}.tar.gz >> ${LOG_FILE} 2>&1

 if [ "$?" -eq 1 ]
 then
  echo -e "***SITE BACKUP JOB, THERE WERE ERRORS***" >> ${LOG_FILE} 2>&1
 else
  echo -e "Script Completed Successfully!" >> ${LOG_FILE} 2>&1
 fi
done

```

### MySQL

```
#!/bin/sh
# MySQL Backup script
#
# db_backup.sh

# In summary, this is what is going to happen.
# make directory
# change directory
# dump file
# compress directory
# remove directory
# upload compressed file

# Initialize variables specific for this server
MYSQL_SOURCE_HOST=192.168.3.100
MYSQL_DATABASE=this_is_my_database
MYSQL_SOURCE_USER=i_am_db_user
MYSQL_SOURCE_PASS=i_am_db_password
SRC_CONN="-h${MYSQL_SOURCE_HOST} -u${MYSQL_SOURCE_USER} -p${MYSQL_SOURCE_PASS}"
MYSQL_TABLE=(
 tbl_one
 tbl_two
)

LOG_FILE=/var/log/mysql-backup.log
MYSQL_BACKUP_PATH=/var/script/backup

# Definition
TIMESTAMP=`date "+%Y-%m-%d %H:%M:%S"`
CURRENT_YEAR=`date "+%Y"`
CURRENT_MONTH=`date "+%Y-%m"`
TODAY_DATE=`date "+%Y-%m-%d"`
EPOCH=`date +%s`
S3_PATH=s3://name-of-s3-bucket/db/${CURRENT_YEAR}/${CURRENT_MONTH}/${TODAY_DATE}

# Remove MySQL Backup older than 3 days
/usr/bin/find ${MYSQL_BACKUP_PATH} -type f -mtime +3 -delete

echo "MySQL Backup Log: " ${TIMESTAMP} >> ${LOG_FILE}
echo -e "--------------------------------------------" >> ${LOG_FILE}
echo -e "" >> ${LOG_FILE}

MYSQLDUMP_OPTIONS="--hex-blob --compress --lock-tables=false"
# Loop through the tables
for TBL in "${MYSQL_TABLE[@]}"
do
 FILENAME=${TBL}-${TODAY_DATE}-${EPOCH}
 # Backup MySQL
 mysqldump ${SRC_CONN} ${MYSQLDUMP_OPTIONS} ${MYSQL_DATABASE} ${TBL} \
  > ${MYSQL_BACKUP_PATH}/${FILENAME}.sql
 # Compress today's directory
 tar -zcf ${MYSQL_BACKUP_PATH}/${FILENAME}.tar.gz -C ${MYSQL_BACKUP_PATH} ${FILENAME}.sql
 # Transfer the file to Amazon S3
 s3cmd put --acl-private --guess-mime-type \
  ${MYSQL_BACKUP_PATH}/${FILENAME}.tar.gz \
  ${S3_PATH}/${FILENAME}.tar.gz >> ${LOG_FILE} 2>&1
done

if [ "$?" -eq 1 ]
then
 echo -e "***MySQL BACKUP JOB, THERE WERE ERRORS***" >> ${LOG_FILE} 2>&1
else
 echo -e "Script Completed Successfully!" >> ${LOG_FILE} 2>&1
fi
```

### SVN Repository

```
#!/bin/sh
# SVN Off Site Backup script
#
# svn_backup.sh

# Input from command line
SVN_REPOSITORY=($1)

# Definition
TIMESTAMP=`date "+%Y-%m-%d %H:%M:%S"`
CURRENT_YEAR=`date "+%Y"`
CURRENT_MONTH=`date "+%Y-%m"`
TODAY_DATE=`date "+%Y-%m-%d"`
EPOCH=`date +%s`
LOG_FILE=/var/log/svn-backup.log
SVN_BACKUP_PATH=/var/script/backup
SVN_PATH=/var/svn/repos
S3_PATH=s3://name-of-s3-bucket/svn/${CURRENT_YEAR}/${CURRENT_MONTH}

# Remove SVN Backup older than 7 days
/usr/bin/find ${SVN_BACKUP_PATH} -type f -mtime +7 -delete

echo "SVN Offsite Backup Log: " ${TIMESTAMP} >> ${LOG_FILE}
echo -e "--------------------------------------------" >> ${LOG_FILE}
echo -e "" >> ${LOG_FILE}

# Loop through the SVN Repository
for i in ${SVN_REPOSITORY[@]}
do
 FILENAME=$i-${TODAY_DATE}-${EPOCH}
 # Backup SVN
 svnadmin dump ${SVN_PATH}/$i | gzip > ${SVN_BACKUP_PATH}/${FILENAME}.svndump.gz

 # Transfer the file to Amazon S3
 s3cmd put --acl-private --guess-mime-type \
  ${SVN_BACKUP_PATH}/${FILENAME}.svndump.gz \
  ${S3_PATH}/${FILENAME}.svndump.gz >> ${LOG_FILE} 2>&1

 if [ "$?" -eq 1 ]
 then
        echo -e "***SVN OFFSITE BACKUP JOB, THERE WERE ERRORS***" >> ${LOG_FILE} 2>&1
 else
         echo -e "Script Completed Successfully!" >> ${LOG_FILE} 2>&1
 fi
done
```

### SVN Repository to Backup

```
#!/bin/sh
# SVN Repository to Backup
#
# call_backup.sh

svn_repository=(
 my-project-number-one
 my-project-number-two
 my-project-number-three
)

/var/script/svn_backup.sh "${svn_repository[*]}"
```
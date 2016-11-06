# phpMySQLAutoBackup

To upgrade an existing installation:
---------------------------------------
BACKUP your original files. 
Follow the install details below (note: you can use your original run.php file, but you must add the new variables detailed below, if missing from your original run.php file).
You can use the details within your existing /phpMySQLAutoBackup/run.php file to complete section 2 below.

To install:
---------------------------------------
- Unzip the files and upload to your server.
	You should upload the files and folders retaining the original names 

- To configure your installation, add your details to the following variables within file /phpMySQLAutoBackup/run.php:
	- $db_server
		"localhost" is normally fine - if not then please contract your web host
	- $db
		set to your MySQL database name
	- $mysql_username
		set to your MySQL username
	- $mysql_password
		set to your MySQL password
	- $to_emailaddress
		set to your email address to send backup files to
	- $report_emailaddress
		set to your email address to send reports (not the actual backups)
	- $from_emailaddress
		set to a different email address than above, else some spam detectors will bin the email
	- $send_email_backup
		set to 1 to send the backup via email to the address above
	- $send_email_report
		set to 1 to send the report via email to the address above
	- $time_interval, interval between backups, stops malicious attempts at bringing down your server by making multiple requests to run the backup. By default this is set to one hour (3600 seconds), will only allow the backup to run once each hour. If DEBUG is set to 1 (see below) then $time_interval is also set to 1.
	- DEBUG, SET this to 0 (zero) when you are happy it is working 
	set to 1 to print output to screen, displays any errors, and shows report
	- FTP, push the backup file to a remote server. PHP CURL is required and ensure the local and remote backups folders are writeable.

		The correct format is:
		$ftp_username="your-ftp-username";
		$ftp_password="yoursecure-pass"; 
		$ftp_server="ftp.your--domain.com";
		$ftp_path="/public_html/secure-folder/";

		Uncomment the variables, add the correct ftp details and it will copy the backup to the remote server.

		The remote folder you are uploading to should be secure and already exist. Ensure your backups are not uploaded to a folder open for public access.

	- $save_backup_zip_file_to_server
		If you set the variable $save_backup_zip_file_to_server to 1 then the backup files will be saved in the folder: /phpmysqlautobackup/backups/ 
		(ensure you chmod [777] the backups folder for write access to allow for file creation)
	- TOTAL_BACKUP_FILES_TO_RETAIN, the total number of backup files to be retained (only relevant if $save_backup_zip_file_to_server is set to 1 ). 


------It is likely that the settings below will only be used by the more advanced user----- 


	- $newline
		fix for email attachment issue (the backup file is included within email body). If your backup emails arrive in your mailbox with no attachment but loads of strange characters within the body of the email try setting $newline to: "\r\n" 
	- $table_select - leave commented out if you need to backup the whole database.
		If you uncomment the variable $table_select then only the specified named tables will be selected for backup.  The table names must be correct else the backup will not select any tables.
		
			The correct format is:
			$table_select[0]="MyFirstTableName";
			$table_select[1]="MySecondTableName";
			$table_select[2]="MyThirdTableName";
			$table_select[x]="MylastTableName";

			You can add any number of table names.

			If you specify distinct tables names, as above, then ONLY those tables will be included in your backup.

			For a list of minimum number of table names to backup for popular applications including phpBB and phpAMA see:
			forum - how to use phpMySQLAutoBackup table_select

	- $table_exclude, leave commented out if you need to backup the whole database.
		If you uncomment the variable $table_exclude then the specified named tables will be excluded from the backup.

			The correct format is:
			$table_exclude[0]="FirstTableName";
			$table_exclude[1]="SecondTableName";
			$table_exclude[2]="ThirdTableName";
			$table_exclude[x]="lastTableName";

			You can add any number of table names.

			Should not be used in conjunction with $table_select.
			For more details see:
			forum - how to use phpMySQLAutoBackup table_exclude

	- $limit_from
		record number to start from, use this if you have a large table to backup.  Should be used with a single table setting like:
			$table_select[0]="MyFirstTableName";
	- $limit_to
		total rows to export 
		
		The two variables above are used in the SQL query:
			SELECT * FROM tablename LIMIT $limit_from , $limit_to
		These variables have been added to allow for the backup of just a section of a very large table with millions of records.
 
TIP:  If you need to backup several different databases or tables at different backup times you can copy the run.php file, and rename it appropriately.

- To run the backup:
		Enter in your address bar the url:

		http://www.[Your--domain].com/phpmysqlautobackup/run.php
		(runs silently [no output] when DEBUG is set to zero, unless there are errors, OHhh no.)

	If you have entered the above details correctly this will export your database, compress it into gzip format and email it to you.

	When run correctly for the first time it will create a new table within your database called: phpmysqlautobackup, which contains a single record to monitor the last time it was run (used to stop abuse), and phpmysqlautobackup_log which holds the reports.

	To schedule it to run hourly/daily/monthly add the above url to a cron job or use the excellent phpJobScheduler
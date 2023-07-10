# MySQL8+安装后修改大小写敏感

I can get it to work with a workaround (I originally posted on askubuntu): by re-initializing MySQL with the new value for lower_case_table_names after its installation. The following steps apply to a new installation. If you have already data in a database, export it first to import it back later:

1. Install MySQL:

```bash
sudo apt-get update    
sudo apt-get install mysql-server -y
```

2. Stop the MySQL service:

```sudo service mysql stop```

3. Delete the MySQL data directory:

```sudo rm -rf /var/lib/mysql```

4. Recreate the MySQL data directory (yes, it is not sufficient to just delete its content):

```bash
sudo mkdir /var/lib/mysql
sudo chown mysql:mysql /var/lib/mysql
sudo chmod 700 /var/lib/mysql
```

Add `lower_case_table_names = 1` to the `[mysqld]`section in `/etc/mysql/mysql.conf.d/mysqld.cnf`.

5. Re-initialize MySQL with `--lower_case_table_names=1`:

`sudo mysqld --defaults-file=/etc/mysql/my.cnf --initialize --lower_case_table_names=1 --user=mysql --console`

6. Start the MySQL service:

`sudo service mysql start`

7. Retrieve the new generated password for MySQL user root:

`sudo grep 'temporary password' /var/log/mysql/error.log`

8. Change the password of MySQL user root either by:

`sudo mysql -u root -p`  
and executing:

`ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPa$$w0rd';`

9. afterwards, OR by calling the "hardening" script anyway:  
`sudo mysql_secure_installation`  
After that, you can verify the lower_case_table_names setting by entering the MySQL shell:

`sudo mysql -u root -p`

and executing:

`SHOW VARIABLES LIKE 'lower_case_%';`
Expected output:

+------------------------+-------+  
| Variable_name          | Value |  
+------------------------+-------+  
| lower_case_file_system | OFF   |  
| lower_case_table_names | 1     |  
+------------------------+-------+  

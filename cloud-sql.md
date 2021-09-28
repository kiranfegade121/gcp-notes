# Cloud SQL Demo

## Create a MYSQL Instance

- Instance ID => mysql-instance
- Password => Provide password for root user
- Select MySQL 8.0 version
- Choose region and select Multiple Zone (High Availability option)
- Click on More configuration otpions
  - Machine Type => Lightweight => 1 vCPU, 3.75GB RAM
  - Storage => SSD => 20GB and Enable automatic storage increases.
  - Connection => Public IP
- Click on "CREATE INTSANCE"

## Connect to MySQL Instance from Cloud Shell.

- Start cloud shell

```
   $ gcloud sql instance list
   $ gcloud sql connect mysql-instance --user=root
```

## Connect to mysql instance from VM

- Create a new VM
  - Name => mysql-client-vm
    OS => Ubuntu
    Startup Scirpt =>
    ```
       #! /bin/bash
       sudo apt-get update && apt-get install mysql-client -y
    ```
- SSH into VM

  Execute below command:

  ```
    $ mysql -h <public_ip_of_mysql_instance> -u root -p
    Enter Password:
  ```

  It won't connect to mysql-instance. We have to configure mysql-instance to give access to VM

## Configure mysql-instance so that VM can access / connect to it.

- Click on connections from left side menubar.
- Under Networking => Public IP => Click on Add Network => Provide name as "VM intance" and IP address of VM in CIDR notation. for eg. 34.132.140.19/32
  -- Click on Save.

## Connect from VM to mysql-instance

```
  $ mysql -h <public_ip_of_mysql_instance> -u root -p
  Enter Password:
  mysql> SHOW SCHEMAS;
  mysql> CREATE DATABASE hr;
  mysql> SHOW SCHEAMS;
  mysql> USE hr;
  mysql> CREATE TABLE employees
         (
             employee_id INT(5) PRIMARY KEY,
             name VARCHAR(20),
             department VARCHAR(20),
             salary INT(10)
         );
  mysql> SHOW TABLES;
  mysql> DESC employees;
  mysql> INSERT INTO employees VALUES(101, 'Alex Browning', 'HR', 33000);
  mysql> SELECT * FROM employees;
```

## How to connect over SSL?

- Generate server-ca.pem, client-cert.pem and client-key.pem files
- Go to connection from left side menu
- Under Security => Manage certificates => Create a new certificate => Download that certificate (server-ca.pem)
- Under Manage Client Certificates => Create client certificate => Download client-cert.pem and client-key.pem file

## Connect to mysql-instance from VM over SSL

- SSH into VM
- Copy all the certificates to VM (server-ca.pem, client-cert.pem and client-key.pem)
- Execute below command.

```
  $ mysql -uroot -p -h <public_ip_of_mysql_instance> --ssl-ca=server-ca.pem --ssl-cert=client-cert.pem --ssl-key=client-key.pem
```

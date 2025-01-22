# Basic Operations on HBase Data
This exercise describes how to use HBase Shell commands to create, delete, modify the user_tag_value table, as well as add, delete, query, and modify table data.

## 1 Presetting the Lab Environment
Click Preset Lab Environment on the top of the sandbox.

![alt text](image.png)

## 2 Starting the Xfce terminal and logging in to the ECS using SSH

Open Google Chrome, go to the ECS service list, and copy the ECS EIP.

![alt text](image-1.png)

Double-click Xfce Terminal on the desktop. Log in to the ECS
```
ssh root@EIP

```
![alt text](image-2.png)

## 3 Starting the Hadoop cluster

Switch to user zker.

``` su - zker ```
![alt text](image-3.png)

Find start-cluster.sh and execute it.

![alt text](image-4.png)

Run jps to check whether all processes are executed.

![alt text](image-5.png)

>![CAUTION]
>If not all the preceding processes are displayed, for example, HMaster and HRegionServer processes are missing, HBase fails >to be started. In this case, you need to manually start HBase.

```
cd /usr/local/bigdata/hbase-2.3.4/bin
./start-hbase.sh
```
## 4 Preparing Data

Log in to the ECS and enter the ECS login password as user root.

![alt text](image-7.png)

Download the data file.
```
wget https://koolabsfiles.obs.ap-southeast-3.myhuaweicloud.com/bigdata/user_tag_value
```
![alt text](image-8.png)

Downloading code files
```wget https://sandbox-expriment-files.obs.cn-north-1.myhuaweicloud.com:443/20220930/exp5/load_to_user_tag_value.py
```
![alt text](image-9.png)

Create the data folder in the /root/ directory to store data sets and run the ls command to view the data sets.
```
mkdir -p /root/data/user_tag_value
ls /root/
mkdir /opt/python_file
ls /opt
```
![alt text](image-10.png)

Move the downloaded data file to the /root/data directory,then
Move the downloaded code file to the /opt/python_file directory.
```
mv user_tag_value /root/data/user_tag_value/
mv load_to_user_tag_value.py /opt/python_file
```
![alt text](image-11.png)

## Procedure
### 5.1 Operations on HBase database tables
Starting the HBase clientLoad HBase environment variables.
``` source /etc/profile ```
Then, start HBase client by :
```
hbase shell
```

### 5.2 Creating the user_tag_value table
```
create_namespace 'onlinelearning'
create 'onlinelearning:user_tag_value',{NAME=>'user'}
```
Run the ```list``` command to check whether the tablespace and table are created.
![alt text](image-12.png)

### 5.3 Modifying the table structure (change the value of VERSIONS from 1 to 3)
Run the describe command to view the table structure after a table is created.
``` 
describe 'onlinelearning:user_tag_value'
```
![alt text](image-13.png)

Enter the alter command to change the structure of an existing table.
```
alter 'onlinelearning:user_tag_value', NAME => 'user', VERSIONS => 3
```
![alt text](image-14.png)
Run describe command.
```
describe 'onlinelearning:user_tag_value'
```
![alt text](image-15.png)

### 5.4 Starting HBase Thrift
Retain the existing Xfce terminal, open another Xfce terminal, and run the ssh root@EIP command to log in to the ECS. Run the following command to start HBase Thrift.
![alt text](image-16.png)

``` hbase thrift start-port:9090 ```
![alt text](image-17.png)

## 6 Uploading a file
Open a third terminal use ssh to login to root, then install thrift.
```
pip3 install thrift
```
![alt text](image-18.png)

Install the hbase-thrift-0.20.4.patch (supporting Python 3.x).
```
wget http://dl.cpp.la/Archive/hbase-thrift-0.20.4.patch.tgz
```
![alt text](image-19.png)

>![CAUTION]
>If did not downloaded the file add ```--no-check-certificate ``` at the  end of the command.

![alt text](image-20.png)

Decompress the package.
Switch the path.
```
tar -zxvf hbase-thrift-0.20.4.patch.tgz
cd hbase-thrift-0.20.4.patch
```
![alt text](image-21.png)
Install the setup.py file.

```
python3 setup.py install
```
![alt text](image-22.png)

Check whether hbase-thrift is installed.
``` pip3 list | grep hbase-thrift ```
![alt text](image-23.png)

Run the following Python script to upload the data sets in user_tag_value to HBase table user_tag_value:
```
python3 /opt/python_file/load_to_user_tag_value.py
```
![alt text](image-24.png)

Checking the first five rows of data in user_tag_valueUse the first Xfce terminal, run the scan command to query the entire table data. You only need to specify the table name.
```
scan 'onlinelearning:user_tag_value', {LIMIT=>5, FORMATTER=>'toString'}
```
![alt text](image-25.png)

The following figure shows the command output and the first five lines of data in user_tag_value.
![alt text](image-26.png)

Clearing data tablesRun the truncate command to clear all data in a specified table.
```
truncate 'onlinelearning:user_tag_value'
```
![alt text](image-27.png)

Querying data whose rowkey is 1 in user_tag_value
``` get 'onlinelearning:user_tag_value', '1' ```
![alt text](image-28.png)

add the data whose rowkey is 1:
``` 
put 'onlinelearning:user_tag_value','1','user: userid','001'
put 'onlinelearning:user_tag_value','1','user: username','zhansgan'
put 'onlinelearning:user_tag_value','1','user: birthdate','1997-11-20'
put 'onlinelearning:user_tag_value','1','user: gender','male'
put 'onlinelearning:user_tag_value','1','user: district','hubei'
put 'onlinelearning:user_tag_value','1','user: last_login_time','2020-04-11 '
put 'onlinelearning:user_tag_value','1','user: course_id','002'
put 'onlinelearning:user_tag_value','1','user: term_id','003'
put 'onlinelearning:user_tag_value','1','user: select_date','2019-09-07'
```
![alt text](image-29.png)

Querying data whose rowkey is 1
``` get 'onlinelearning:user_tag_value','1',{FORMATTER=>'toString'} ```
![alt text](image-30.png)

Modifying dataRun the following commands to change select_data (course selection time) to 2019-10-11 and check the data before and after the modification:
``` get 'onlinelearning:user_tag_value','1',{FORMATTER=>'toString'} ```
![alt text](image-31.png)

```put 'onlinelearning:user_tag_value','1','user: select_date','2019-10-11'```
![alt text](image-32.png)

``` get 'onlinelearning:user_tag_value','1',{FORMATTER=>'toString'} ```
![alt text](image-33.png)

Deleting data from user_tag_value:
Delete a logical row from the table. Run the deleteall command to delete the data whose rowkey is 1, and then check the data before and after the deletion.
```
get 'onlinelearning:user_tag_value','1',{FORMATTER=>'toString'}
deleteall 'onlinelearning:user_tag_value','1'
get 'onlinelearning:user_tag_value','1',{FORMATTER=>'toString'}
```
![alt text](image-34.png)


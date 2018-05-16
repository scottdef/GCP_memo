# Memo for Google Cloud Platform (GCP)

Useful commands/scripts for use on Google Cloud Platform.

## Clean your Cloud resources

Whatever you build on GCP, it might cost a lot if you forget to delete some compute resources.

Here is a way to do it from the shell:

```shell
gcloud config list
gcloud config set project <your_project_name>
gcloud projects delete <your_project_name>
```


## Create a Virtual Machine from CloudShell

```shell
#!/bin/bash

# Remove "_" from USER because "_" can not be used for instance name
export INSTANCE_NAME=datalabvm-${USER//_/}
export ZONE=us-central1-a
export MACHINE_TYPE=n1-standard-1

echo "Creating instance name=$INSTANCE_NAME in zone=$ZONE of type=$MACHINE_TYPE"
gcloud compute instances create $INSTANCE_NAME \
   --image-family=container-vm --image-project=google-containers \
   --zone $ZONE --machine-type $MACHINE_TYPE \
   --metadata "google-container-manifest=$(cat datalab.yaml)" \
   --scopes cloud-platform
```

## Check your CloudSQL instance from CloudShell

```shell
$ mysql --host=35.234.239.132 --user=root --password
MySQL [(none)]> show databases;
MySQL [(none)]> use recommendation_spark;
MySQL [recommendation_spark]> show tables;
MySQL [recommendation_spark]> DESCRIBE Accommodation;
MySQL [recommendation_spark]> SELECT id, count(*) FROM Accommodation GROUP BY id;
MySQL [recommendation_spark]> SELECT DISTINCT userId, count(*) FROM Rating GROUP BY userId;
MySQL [recommendation_spark]> SELECT count(*) FROM Rating;
MySQL [recommendation_spark]> SELECT * FROM Recommendation LIMIT 5;
MySQL [recommendation_spark]> SELECT count(*) FROM Recommendation;
MySQL [recommendation_spark]> select r.userid, r.accoid, r.prediction, a.title, a.location, a.price, a.rooms, a.rating, a.type from Recommendation as r, Accommodation as a where r.accoid = a.id and r.userid = 10;
```

## Authorizing CloudShell to access a CloudSQL instance

Assuming your CloudSQL instance is named `rentals`:

```shell
#!/bin/bash
gcloud sql instances patch rentals \
    --authorized-networks `wget -qO - http://ipecho.net/plain`/32
```

## Dataproc

### Create a firewall rule to access Dataproc tools (HDFS, Datalab, Hadoop) from your browser

This can be done before you create the cluster.

* find you IP with [http://ip4.me/](http://ip4.me/) and use it followed by `/32` in the `--source-ranges` option.
* allow TCP port on 8088 (**Hadoop**), 9870 (**HDFS**), 8080 (**Datalab**) 


```shell
gcloud compute --project=coursera-gcp-course02 firewall-rules create <name-your-default-allow-dataproc-access> \
--description="description here" \
--direction=INGRESS --priority=1000 --network=default --action=ALLOW \
--rules=tcp:8088,tcp:9870,tcp:8080 \
--source-ranges=95.130.218.4/32
```

### Creating Dataproc clusters with the CLI/CloudShell

This command creates a Dataproc cluster named **my-second-cluster** in the **us-central1-a** zone. It creates a master node with 1 CPU and a 50 GB disk and 2 worker nodes with the same resources:

```shell
gcloud dataproc clusters create my-second-cluster --zone us-central1-a \
        --master-machine-type n1-standard-1 --master-boot-disk-size 50 \
        --num-workers 2 --worker-machine-type n1-standard-1 \
        --worker-boot-disk-size 50 
```

or 

```shell
gcloud dataproc clusters create my-cluster --zone us-central1-a \
        --master-machine-type n1-standard-1 --master-boot-disk-size 50 \
        --num-workers 2 --worker-machine-type n1-standard-1 \
        --worker-boot-disk-size 50 --network=default
```


### Create a Cloud Storage bucket for your Dataproc cluster

In **Google Cloud Shell**, enter the following command to create a Cloud Storage bucket **with the same name** as your project ID **in the same region as your cluster**. Both Cloud Storage buckets and Project ID's have to be unique, so unless you are very unlucky your project ID would not have been previously used for a bucket name.

```shell
gsutil mb -c regional -l us-central1 gs://$DEVSHELL_PROJECT_ID
```

### Copying source code and input files to your bucket

In Google Cloud Shell, enter the commands below to copy some pre-created files into your bucket (make sure to plug in your bucket name).

```shell
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
cd training-data-analyst/courses/unstructured
./replace_and_upload.sh <YOUR-BUCKET-NAME>
# or
# ./replace_and_upload.sh $DEVSHELL_PROJECT_ID
```

### Run jobs on your cluster

#### Run a simple PySpark job

Type `pyspark` at the command prompt to open the PySpark shell.

```shell
patrick_merlot@my-cluster-m:~$ pyspark
Python 2.7.9 (default, Jun 29 2016, 13:08:31) 
[GCC 4.9.2] on linux2
Type "help", "copyright", "credits" or "license" for more information.
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
ivysettings.xml file not found in HIVE_HOME or HIVE_CONF_DIR,/etc/hive/conf.dist/ivysettings.xml will be used
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version 2.2.1
      /_/
Using Python version 2.7.9 (default, Jun 29 2016 13:08:31)
SparkSession available as 'spark'.
>>> 
>>> 
>>> data = [0, 1, 2, 3, 4, 5]  # range(6)
>>> distData = sc.parallelize(data)
>>> squares = distData.map(lambda x : x*x)
>>> res = squares.reduce(lambda a, b : a + b)
>>> print res                                                
>>> 55
>>>
>>> # Write a PySpark program to compute the square root of the sum of the first 1000 terms of this series starting
 at k=0: 8.0/((2k+1)(2k+1))
... 
>>> import numpy as np
>>> data = range(1000)
>>> distData = sc.parallelize(data)
>>> terms = distData.map(lambda k : 8.0/((2*k+1)*(2*k+1)))
>>> res = np.sqrt(terms.sum())
>>> print res
3.1412743276
>>> 
>>> quit()
patrick_merlot@my-cluster-m:~$ 
```
#### Pig Job that reads from HDFS

Enter the following command to copy a data file and a pig script into the folder you just created. Make sure to plug in your actual bucket name.

```shell
gsutil -m cp gs://<YOUR-BUCKET-NAME>/unstructured/pet-details.* .
```

```shell
patrick_merlot@my-cluster-m:~$ mkdir lab2
patrick_merlot@my-cluster-m:~$ cd lab2
patrick_merlot@my-cluster-m:~/lab2$ gsutil -m cp gs://coursera-gcp-course02/unstructured/pet-details.* .
Copying gs://coursera-gcp-course02/unstructured/pet-details.pig...
Copying gs://coursera-gcp-course02/unstructured/pet-details.txt...              
/ [2/2 files][  687.0 B/  687.0 B] 100% Done                                    
Operation completed over 2 objects/687.0 B.                                      
patrick_merlot@my-cluster-m:~/lab2$ cat pet-details.txt 
Dog,Noir,Schnoodle,Black,21
Dog,Bree,MaltePoo,White,10
Dog,Pickles,Golden Retriever,Yellow,30
Dog,Sparky,Mutt,Gray,13
Cat,Tom,Alley,Yellow,11
Cat,Cleo,Siamese,Gray,22
Frog,Kermit,Bull,Green,1
Pig,Bacon,Pot Belly,Pink,30
Pig,Babe,Domestic,White,150
Dog,Rusty,Poodle,White,20
Cat,Joe,Mix,Black,15patrick_merlot@my-cluster-m:~/lab2$ 
```

```shell
patrick_merlot@my-cluster-m:~/lab2$ cat pet-details.pig 
rmf /GroupedByType
x1 = load '/pet-details' using PigStorage(',') as (Type:chararray,Name:chararray,Breed:chararray,Color:chararray,We
ight:int);
x2 = filter x1 by Type != 'Type';
x3 = foreach x2 generate Type, Name, Breed, Color, Weight, Weight / 2.24 as Kilos:float;
x4 = filter x3 by LOWER(Color) == 'black' or LOWER(Color) == 'white';
x5 = group x4 by Type;
store x5 into '/GroupedByType';
patrick_merlot@my-cluster-m:~/lab2$
```

let's copy the text file into HDFS:

```shell
patrick_merlot@my-cluster-m:~/lab2$ hadoop fs -mkdir /pet-details
18/05/14 13:01:04 INFO gcs.GoogleHadoopFileSystemBase: GHFS version: 1.6.5-hadoop2
patrick_merlot@my-cluster-m:~/lab2$ hadoop fs -put pet-details.txt /pet-details
18/05/14 13:01:09 INFO gcs.GoogleHadoopFileSystemBase: GHFS version: 1.6.5-hadoop2
patrick_merlot@my-cluster-m:~/lab2$
```

Go back to the Web console and the details of your master node. Find the master node's external IP address and copy it to the clipboard. Then, open a new tab in your browser, paste in the ip address and then add **:9870**. This will open the Hadoop management site. From the **Utilities** menu on the right select **Browse the file system**.

Verify that you have a folder called **pet-details** and inside it you should have a file called **pet-details.txt**.


Run the following command to run Pig:

```shell
pig < pet-details.pig
```
Click **Submit** to start the job. It will take about a minute to run. Wait until it completes.

You will get something like:

```shell
...
HadoopVersion   PigVersion      UserId  StartedAt       FinishedAt      Features
2.8.3   0.16.0  patrick_merlot  2018-05-14 13:10:17     2018-05-14 13:10:51     GROUP_BY,FILTER
Success!
Job Stats (time in seconds):
JobId   Maps    Reduces MaxMapTime      MinMapTime      AvgMapTime      MedianMapTime   MaxReduceTime   MinReduceTime   AvgReduceTime   MedianReducetime        Alias   Feature Outputs
job_1526300672789_0004  1       1       5       5       5       5       5       5       5       5       x1,x2,x3,x5     GROUP_BY        /GroupedByType,
Input(s):
Successfully read 11 records (668 bytes) from: "/pet-details"
Output(s):
Successfully stored 3 records (249 bytes) in: "/GroupedByType"
Counters:
Total records written : 3
Total bytes written : 249
Spillable Memory Manager spill count : 0
Total bags proactively spilled: 0
Total records proactively spilled: 0
Job DAG:
job_1526300672789_0004
2018-05-14 13:10:51,911 [main] INFO  org.apache.hadoop.yarn.client.RMProxy - Connecting to ResourceManager at my-cluster-m/10.128.0.3:8032
2018-05-14 13:10:51,920 [main] INFO  org.apache.hadoop.mapred.ClientServiceDelegate - Application state is completed. FinalApplicationStatus=SUCCEEDED. Redirecting to job history server
2018-05-14 13:10:51,999 [main] INFO  org.apache.hadoop.yarn.client.RMProxy - Connecting to ResourceManager at my-cluster-m/10.128.0.3:8032
2018-05-14 13:10:52,010 [main] INFO  org.apache.hadoop.mapred.ClientServiceDelegate - Application state is completed. FinalApplicationStatus=SUCCEEDED. Redirecting to job history server
2018-05-14 13:10:52,101 [main] INFO  org.apache.hadoop.yarn.client.RMProxy - Connecting to ResourceManager at my-cluster-m/10.128.0.3:8032
2018-05-14 13:10:52,113 [main] INFO  org.apache.hadoop.mapred.ClientServiceDelegate - Application state is completed. FinalApplicationStatus=SUCCEEDED. Redirecting to job history server
2018-05-14 13:10:52,206 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success!
grunt> 2018-05-14 13:10:52,254 [main] INFO  org.apache.pig.Main - Pig script completed in 40 seconds and 957 milliseconds (40957 ms)
```

Go back to the tab with the Hadoop management site and again browse the file system. The output from this Pig job should be in a folder called **GroupedByType**. If you look in that folder you should see a file named **part-r-00000**.

Let's look at the output file.

First you have to get the file off the HDFS file system. Go back to your SSH session where you are connected to the master node. You should currently be in the folder **lab2**.

Make a directory below it and move into by entering the following commands, and enter the following command to get the output file from HDFS and copy it into this folder.

```shell
patrick_merlot@my-cluster-m:~/lab2$ mkdir ~/lab2/output
patrick_merlot@my-cluster-m:~/lab2$ cd ~/lab2/output
patrick_merlot@my-cluster-m:~/lab2/output$ hadoop fs -get /GroupedByType/part* .
18/05/14 13:15:35 INFO gcs.GoogleHadoopFileSystemBase: GHFS version: 1.6.5-hadoop2
patrick_merlot@my-cluster-m:~/lab2/output$ 
```

Finally, enter the following command to view the results.

```shell
patrick_merlot@my-cluster-m:~/lab2/output$ cat *
Cat     {(Cat,Joe,Mix,Black,15,6.696428571428571)}
Dog     {(Dog,Rusty,Poodle,White,20,8.928571428571427),(Dog,Bree,MaltePoo,White,10,4.4642857142857135),(Dog,Noir,Schnoodle,Black,21,9.374999999999998)}
Pig     {(Pig,Babe,Domestic,White,150,66.96428571428571)}
patrick_merlot@my-cluster-m:~/lab2/output$
```

### Authorizing Dataproc cluster to access a CloudSQL instance

Assuming that:

* your CloudSQL instance is named `rentals`
* your Dataproc cluster has:
  * name: `cluster-1`
  * located in zone: `us-east1-b`
  * nb. workers: `2`
  
```shell
#!/bin/bash

if [ "$#" -ne 3 ]; then
    echo "authorize_dataproc.sh clustername  zone  numworkers"
    echo "  e.g.  ./authorize_dataproc.sh cluster-1 us-east1-b 2"
    exit
fi

CLUSTER=$1
CLOUDSQL=rentals
ZONE=$2
NWORKERS=$3

machines="$CLUSTER-m"
for w in `seq 0 $(($NWORKERS - 1))`; do
   machines="$machines $CLUSTER-w-$w"
done
echo "Machines to authorize: $machines in $ZONE ... finding their IP addresses"

ips=""
for machine in $machines; do
    IP_ADDRESS=$(gcloud compute instances describe $machine --zone=$ZONE --format='value(networkInterfaces.accessConfigs[].natIP)' | sed "s/\[u'//g" | sed "s/'\]//g" )/32
    echo "IP address of $machine is $IP_ADDRESS"
    if [ -z  $ips ]; then
       ips=$IP_ADDRESS
    else
       ips="$ips,$IP_ADDRESS"
    fi
done

echo "Authorizing [$ips] to access cloudsql=$CLOUDSQL"
```


### Delete your Dataproc cluster at once

```shell
gcloud dataproc clusters delete <my-cluster-name>
```


## Big Data and ML Fundamentals using Datalab

### Work from a given project (billing credentials)

Check that you are already authenticated and that the project is already set to your PROJECT_ID:

```shell
$ gcloud auth list
```

```shell
$ gcloud config list project
[core]
project = api-project-56998XXXXXX

Your active configuration is: [cloudshell-XXXXX]
```

If it is not, you can set it with this command:


```shell
$ gcloud config set project <PROJECT_ID>
```

### Start an instance of Cloud Datalab

Pick a zone in a geographically closeby region.

```shell
$ gcloud compute zones list
NAME                       REGION                   STATUS  NEXT_MAINTENANCE  TURNDOWN_DATE
us-east1-b                 us-east1                 UP
us-east1-c                 us-east1                 UP
us-east1-d                 us-east1                 UP
...

$ datalab create bdmlvm --zone europe-west2-b
...
Creating the network datalab-network
Creating the firewall rule datalab-network-allow-ssh
Creating the disk bdmlvm-pd
Creating the repository datalab-notebooks
...
```

Datalab will take about 5 minutes to start :sleeping:

### Open your "Jupyter" notebooks from Cloud Datalab

From CloudShell, click on the Web Preview icon on the top-left corner of the Cloud Shell ribbon. Switch to port 8081.

![Web Preview](https://run-qwiklab-website-prod.s3.amazonaws.com/instructions/documents/42935/original/img/7eb159ad9b4d3d2d.png)

> Note: The connection to your Datalab instance remains open for as long as the datalab command is active. If the cloud shell used for running the datalab command is closed or interrupted, the connection to your Cloud Datalab VM will terminate.

### Checkout any git repository into Cloud Datalab

In Datalab, click on the icon for "Open ungit" in the top-right ribbon. ![Open ungit](https://run-qwiklab-website-prod.s3.amazonaws.com/instructions/documents/42935/original/img/fc5c4f63c40f83f.png)

In the Ungit window, select the text that reads `/content/datalab/notebooks` and remove the notebooks so that it reads `/content/datalab`, then hit enter. ![Datalab ungit](https://run-qwiklab-website-prod.s3.amazonaws.com/instructions/documents/42935/original/img/18f8d226fe047e84.png)

In the panel that comes up, type the following as the GitHub repository to **Clone from** (e.g. `https://github.com/GoogleCloudPlatform/training-data-analyst`).

Then, click on **Clone repository**.

### Get API key(s) to enable Services [optional] 

Depending on what you develop, you will need to enable Services or APIs. 

From the GCP console menu, select **APIs and services** and select **Library** as submenu.

E.g. In the search box, type **vision** to find the Google **Cloud Vision API** and click on the hyperlink and **Enable** if necessary


If you do not already have an API key, from the GCP console menu, select **APIs and services** and select **Credentials**.

Click the **Create credentials** button and select **API key**. Once created, click close. You will need this API key in the notebook later.


### Invoking ML APIs from Datalab

You can find a demo notebook under [ML APIs](./labs/lab04_BigQuery_ML_APIs).


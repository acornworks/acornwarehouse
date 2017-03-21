# Acorn Warehouse

## Overview
Acorn Warehouse is a central server for Acorn Conveyer. It consists of following functions:

* Embedded Message Queue System (Apache MQ) for allocating job requests to conveyer servers.
* Embedded Version Control System (GitBucket) for managing execution resources.
* Administration Console
  * Job allocation status
  * Execution resource management
  * Centralized log collectors for System Errors and Conveyer Logs

## How to compile
Acorn Warehouse is able to compile on following environments:

* [JDK 1.8 or above](http://java.oracle.com)
* [Apache Maven](http://http://maven.apache.org)

If those applications are installed, run this command:

```bash
mvn package
```

Packaged JAR file is located in **target** folder with the name of **warehouse-[version]-SNAPSHOT.jar**.

## Overall Folder Structure

* warehouse-[version]-SNAPSHOT.jar
* application.properties
* **ext** folder

## Application Settings

**application.properties** file is aim to config specific settings. Details are below:

* server.port=9001
  > Administration console port (Default: 9001)
* message.rebalance.interval=60000
  > Workload rebalancing inrerval (ms) (Default: 60,000 ms)
* message.resource.default-weight=60000
  > Average time to complete the job (ms) (Default: 60,000 ms)
* message.status-cleaning-interval=30000
  > Fixed Rate of timer to check the server resource pool (ms) (Default: 30,000 ms)
* message.status-timeout=60000
  > No-response time to delete from the server resource pool (ms) (Default: 60,000 ms) 
* message.log.size=10000
  > Maximum count of storing log (Default: 10,000)
* spring.http.multipart.max-request-size=100MB
  > Maximym total request size for a multipart/form-data (Default: 100 MB)
* spring.http.multipart.max-file-size=100MB
  > Maxmimum total file size (MB) (Default: 100 MB)
* system.message.errror.save-period=7
  > Error log storing period (Days) (Default: 7 Days)




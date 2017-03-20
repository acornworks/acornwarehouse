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



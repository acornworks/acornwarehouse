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

Packaged JAR file is located in **target** folder with the name of **warehouse-[version].jar**.

## Overall Folder Structure

* warehouse-[version].jar
* application.properties
* **ext** folder

**To run:**
```bash
java -jar warehouse-[version].jar
```

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

## Send a job

To submit a job to Acorn Conveyer System, there are three protocols to pass a job: OpenWire, STOMP and REST. All the protocols are purposed to submit to the message queue system. **"acornwarehouse.queue.job"** is a dedicated queue to receive jobs. It also requires to set a destination to receive job results.

* **OpenWire**
  > OpenWire protocol is for Java and .Net. 
  >
  > For .Net, you can submit a job through [NMS API](http://activemq.apache.org/nms/).  
  > For Java, you can submit a job through [ActiveMQ Client Library](https://mvnrepository.com/artifact/org.apache.activemq/activemq-client/5.14.1)
  > 
  > Destination should be set as "**queue://acornwarehouse.queue.job**".  
  
* **STOMP**
  > STOMP protocol is for Ruby, Perl, Python and PHP.
  >
  > It is same approach as OpenWire Protocol with setting for destination and replyTo properties.
  >
  > Here is an example for **Python**:
  > ```python
  > from stompest.config import StompConfig
  > from stompest.protocol.spec import StompSpec
  > from stompest.sync import Stomp
  > import uuid
  > 
  > CONFIG = StompConfig('tcp:/[server-address]:61612', version=StompSpec.VERSION_1_1)
  > queue = 'acornwarehouse.queue.job'
  > 
  > client = Stomp(CONFIG)
  > client.connect()
  > correlationId = str(uuid.uuid4())
  > 
  > headers = {}
  > headers['persistent'] = False
  > 
  > client.send(queue, message, headers)
  >
  > ```

* **REST**
  > REST protocol is for all languages, which support REST API. 
  > To Send a message, please call following URL with POST method. 
  > 
  > http://[server-address]:8161/api/message?destination=acornwarehouse.queue.job
  >
  > For more details, please refer to [this link](http://activemq.apache.org/rest.html)
  >

## Request body format
**net.acornworks.commons.messages.ExecutionMessage** class explains a description of the job executions. It is also written in JSON file format and following JSON test is an example of the converted ExecutionMessage class.

* **Sample**
```JSON
{
	"jobId": "test-job-01",
	"destination": "test.result",
	"executorType": "DEPENDENT_PROGRAM|SYSTEM_PROGRAM|SHELL_SCRIPT",
	"executorAlias": "TEST_DEPENDENCY",
	"executorPath": "C:\\Windows\\System32\\find.exe",
	"isReturnWithInput": false,
	"commandArgs": ["/?"],
	"outputType": "FILE|CONSOLE_MESSAGE",
	"outputFormat": "a_%s_%s.txt",
	"outputArgs": ["test", "output"],
}
```

* **JSON Description**
  * jobId: this is an unique ID for the job. If it is not defined, the system assigns a random ID.
  * executorType: There are three type of executions: DEPENDENT_PROGRAM, SYSTEM_PROGRAM, SHELL_SCRIPT
    * DEPENDENT_PROGRAM: This is defined in **acorn warehose**. Each of dependency programms has a "conveyer.json" file and it describes a detail of information for the execution. It is managed in embedded Git repository.
    * SYSTEM_PROGRAM, SHELL_SCRIPT: It runs a program|script which should be in **conveyer servers' file systems**.
  * executionAlias: Optional. It is only for "DEPENDENCY_PROGRAM" type. It will be defined in managed dependency resources.
  * executorPath: Optional. It is for "SYSTEM_PROGRAM" and "SHELL_SCRIPT" types. It defines an absolute path to execute a program/script.
  * isReturnWithInput: Optional. It is defined as **true** or **false**. If it is true, a result message contains the execution message.
  * commandArgs: 
    

# Managing systems
---

The Agave API provides a way to access and manage the data storage and compute resources you already use (or maybe the systems you want to use), but first you have to tell Agave where they are, how to login, and how to communicate with that system.  That is done by giving Agave a short JSON description for each system.  

## Storage Systems

Storage systems tell Agave where data resides.  You can store files for running compute jobs, archive results, share files with collaborators, and maintain copies of your Agave apps on storage systems.  Agave supports lots of communication protocols and the permissions models that go along with them, so you can work privately, collaborate with individuals, or provide an open community resource.  It's up to you.  Here is an example of a simple data storage system accessed via SFTP:

```json
{
  "site": "hawaii.edu",
  "id": "",
  "default": false,
  "status": "UP",
  "description": "UH HPC Lustre storage system using SFTP at the University of Hawaii",
  "name": "UH HPC1 SFTP Lustre Storage System",
  "available": true,
  "public": false,
  "type": "STORAGE",
  "storage": {
    "mirror": false,
    "port": 22,
    "homeDir": "/lus/scratch/USERNAME",
    "protocol": "SFTP",
    "host": "uhhpc1.its.hawaii.edu",
    "proxy": null,
    "rootDir": "/",
    "auth": {
      "type": "PASSWORD"
    }
  }
}
```

Within Agave ToGo, click on `Systems` in the navigation bar on the left.

![Agave Systems](../img/agave-togo-systems.png)


### Hands-on

As a hands on exercise, register a data storage system that you use.  For this workshop, we will add the UH HPC Lustre filesystem, but this could also be a remote VM, a storage space on a cluster at a university, or something in the commercial cloud like an S3 bucket.  Other examples of storage systems are here: http://agaveapi.co/documentation/tutorials/system-management-tutorial/#storage-systems

---
## Execution Systems

Execution systems in Agave are very similar to storage systems.  They just have additional information for how to launch jobs.  In this example, we are using a HCP system, so we have to give scheduler and queue information.  This system description is long, but most of it is because we are registering different queues.

```json
{
  "maxSystemJobs": 50,
  "workDir": "",
  "scratchDir": "/lus/scratch/USERNAME/",
  "type": "EXECUTION",
  "id": "",
  "description": "Execution system using ssh to submit jobs to the UH ITS HPC. By default, it uses the Sandbox queue.",
  "name": "UH ITS HPC SSH Execution Host",
  "login": {
    "port": 22,
    "protocol": "SSH",
    "host": "uhhpc1.its.hawaii.edu",
    "proxy": null,
    "auth": {
      "type": "PASSWORD"
    }
  },
  "maxSystemJobsPerUser": 10,
  "site": "hawaii.edu",
  "status": "UP",
  "scheduler": "SLURM",
  "environment": null,
  "executionType": "HPC",
  "queues": [{
    "maxProcessorsPerNode": 20,
    "default": false,
    "maxMemoryPerNode": "120GB",
    "mappedName": null,
    "description": "",
    "name": "community.q",
    "maxRequestedTime": "72:00:00",
    "maxJobs": 25,
    "customDirectives": null,
    "maxNodes": 5,
    "maxUserJobs": 5
  }, {
    "maxProcessorsPerNode": 20,
    "default": false,
    "maxMemoryPerNode": "120GB",
    "mappedName": null,
    "description": "",
    "name": "kill.q",
    "maxRequestedTime": "72:00:00",
    "maxJobs": 25,
    "customDirectives": null,
    "maxNodes": 5,
    "maxUserJobs": 5
  }, {
    "maxProcessorsPerNode": 20,
    "default": true,
    "maxMemoryPerNode": "120GB",
    "mappedName": null,
    "description": "",
    "name": "sb.q",
    "maxRequestedTime": "01:00:00",
    "maxJobs": 25,
    "customDirectives": null,
    "maxNodes": 2,
    "maxUserJobs": 5
  }, {
    "maxProcessorsPerNode": 20,
    "default": true,
    "maxMemoryPerNode": "120GB",
    "mappedName": null,
    "description": "",
    "name": "bioworkshop",
    "maxRequestedTime": "72:00:00",
    "maxJobs": 25,
    "customDirectives": null,
    "maxNodes": 2,
    "maxUserJobs": 5
  }, {
    "maxProcessorsPerNode": 20,
    "default": false,
    "maxMemoryPerNode": "120GB",
    "mappedName": null,
    "description": "",
    "name": "exclusive.q",
    "maxRequestedTime": "72:00:00",
    "maxJobs": 25,
    "customDirectives": null,
    "maxNodes": 5,
    "maxUserJobs": 5
  }],
  "public": false,
  "storage": {
    "mirror": false,
    "port": 22,
    "homeDir": "/home/USERNAME",
    "protocol": "SFTP",
    "host": "uhhpc1.its.hawaii.edu",
    "proxy": null,
    "rootDir": "/",
    "auth": {
      "type": "PASSWORD"
    }
  }
}
```

We covered what some of these keywords are in the storage systems section.  Below is some commentary on the new fields:

* **executionType** - Either HPC, Condor, or CLI.  Specifies how jobs should go into the system. HPC and Condor will leverage a batch scheduler. CLI will fork processes.
* **scheduler** - For HPC or CONDOR systems, Agave is "scheduler aware" and can use most popular schedulers to launch jobs on the system.  This field can be LSF, LOADLEVELER, PBS, SGE, CONDOR, FORK, COBALT, TORQUE, MOAB, SLURM, UNKNOWN. The type of batch scheduler available on the system.
* **environment** - List of key-value pairs that will be added to the Linux shell environment prior to execution of any command.
* **scratchDir** - Whenever Agave runs a job, it uses a temporary directory to cache any app assets or job data it needs to run the job.  This job directory will exist under the "scratchDir" that you set.  The path in this field will be resolved relative to the rootDir value in the storage config if it begins with a "/", and relative to the system homeDir otherwise.

Complete reference information is located here: 
http://developer.agaveapi.co/#systems

### Hands-on
As a hands on exercise, register an execution system that you use.  This could be a remote VM, a cluster at a university, or something in the commercial cloud.

[Back](index.md) | [Next: Creating an App](rscript-app.md)


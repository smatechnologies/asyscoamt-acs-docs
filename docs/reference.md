---
title: AsyscoAMT ACS Reference
description: "Reference information for the AsyscoAMT ACS connector, including completion codes, administration, security considerations, and frequently asked questions."
tags:
  - Reference
  - System Administrator
  - Automation Engineer
  - Agents
sidebar_label: 'Reference'
---

# AsyscoAMT ACS Reference

**Theme:** Troubleshoot | **Audience:** System Administrator, Automation Engineer

## What is it?

This page provides reference information for the AsyscoAMT ACS connector, including completion codes returned by the AMT Batch Server, administration and security guidance, and troubleshooting information.

Use this page when:

- You need to look up the meaning of an AMT completion code returned in a job log.
- You are administering the integration or reviewing security requirements.
- You are troubleshooting a failed or unexpected job result.

## Failure criteria

OpCon receives only a running, finished OK or failed status for an AsyscoAMT job. It does not receive the AMT completion code, which is written to the job log along with any error information. The **OpCon result** column shows how each code affects the job.

| Code | Status | Description | OpCon result |
|---|---|---|---|
| `0` | `IDLE` | Job is idle | Keeps running |
| `1` | `QUEUED` | Job is queued for future start in current timeframe | Keeps running |
| `2` | `RUNNING` | Job was started manually or by the scheduler | Keeps running |
| `3` | `KILLED` | Job was terminated by a 'kill' command | Shown in the job log after a kill from OpCon |
| `4` | `DONE` | Job completed normally | **Finished OK** |
| `5` | `SUSPENDED` | Queued job has not started on time | Keeps running |
| `6` | `SKIPPED_BY_OPS` | Suspended job has been skipped by control center | Keeps running |
| `7` | `RUN_MANUAL` | Job was started by control center | Keeps running |
| `8` | `RUN_FORCED` | Forced start manual; start this job even when job server halted | Keeps running |
| `9` | `RUN_DEBUG` | When a report is started from Visual Studio | Keeps running |
| `10` | `DEL_QUEUE` | Job is deleted from queue | Keeps running |
| `11` | `ERROR` | Job ended in error. When running a script and the script does not exist, an error code of 11 is returned with a description of 'File not found' | **Failed** |
| `12` | `ABORTED` | The job was aborted on purpose in the business-logic | **Failed** |
| `13` | `WAIT FOR FILE` | Waiting for a file | Keeps running |
| `14` | `WAIT FOR INPUT REQUEST` | Waiting for request | Keeps running |
| `15` | `WAIT FOR JOB` | Waiting for another job to finish | Keeps running |
| `16` | `WAIT FOR REPORT` | Waiting for a report to finish; for future use | Keeps running |
| `18` | `RECOVER_CP` | This job (report) is a request to recover from a saved critical point | Keeps running |
| `19` | `UNDEFINED` | State is undefined | Keeps running |
| `20` | `WAIT_FOR QUEUE` | Waiting for queue start time | Keeps running |
| `21` | `WAIT_FOR_DEBUGGER` | Waiting for a LION Debugger to start debug session for the job | Keeps running |
| `22` | `ABORTED_WITH_RECOVER` | Aborted with recover | **Failed** |
| `23` | `WEB_SERVER_ERROR` | — | **Failed** |
| `24` | `AUTHENTICATION_ERROR` | — | **Failed** |
| `99` | `INVALID AMT USER` | When running an AMT script with a named AMT user and the user is invalid | — |

Jobs that the AMT side skips (SKIPPED_BY_OPS) or deletes from the queue (DEL_QUEUE) keep running in OpCon until the job is ended there.

### Common errors

**Error 11 (ERROR)** — The job ended in error. When running a script, this often means the script file was not found on the AMT Batch Server. Verify the path specified in the **Script Name** field exists on the server.

**Job fails at start with only the job information in the log** — The connector could not log on to the AMT Batch Server: the server is unreachable, or the Batch User was rejected. The reason is recorded in the SMANetCom or relay service log. Verify the **Batch Server URL**, that the server is running and reachable, and the **Batch User** identifier and password. The agent's communicating status confirms only that the server answers; it does not test the Batch User.

**Error 23 (WEB_SERVER_ERROR)** — Reported by the AMT Batch Server for the job, which fails.

**Error 24 (AUTHENTICATION_ERROR)** — Reported by the AMT Batch Server for the job, which fails. If the Batch User itself is rejected at logon, the job fails at start instead, as described above.

**Error 99 (INVALID AMT USER)** — The user specified in the **User** field of the script task does not exist in the AMT environment. Verify the user name or leave the field blank to use the Application user.

## Administration

### Enable and disable the connection

To enable the AsyscoAMT Batch Server connection, select **Change Communication Status** on the agent and select **Enable Full Comm**. To disable the connection, select **Disable Full Comm**. When disabled, OpCon cannot submit jobs to the AMT Batch Server.

### Required roles

Configuring the AsyscoAMT agent connection and Batch User requires OpCon role-based access to the **Library** administration area. Defining and editing AsyscoAMT jobs requires access to **Master Jobs** within **Library**.

### Maintenance

- **Log retention:** Job log files are retained for the number of days specified in the **Retain Log Files** field on the agent connection. The default is 30 days.
- **Credential updates:** If the AMT API user password changes, update the Batch User record in OpCon to restore authentication.

### Kill job

The Asysco AMT ACS integration supports the ability to terminate a task within the Asysco AMT Batch Server. When the **Kill** job status is selected within OpCon, an immediate kill request for the task is submitted to the AsyscoAMT Batch Server. OpCon shows the job as killed whether or not the AMT Batch Server accepted the request; check the completion code in the job log.

## Security considerations

**Authentication** — The AsyscoAMT connector authenticates with the AMT Batch Server using an API user name and password stored as a Batch User record in OpCon. The password is stored encrypted in the OpCon database.

**Authorization** — Access to the agent connection configuration and Batch User records is controlled by OpCon role-based permissions. Only users with appropriate Library administration access can configure the integration.

**Data security** — Job parameters, task values, and script parameters are transmitted to the AMT Batch Server through the REST-API. Use an HTTPS Batch Server URL in production environments to encrypt data in transit.

**Sensitive data** — The Batch User credentials provide API access to the AMT Batch Server. Store them using the OpCon Batch User mechanism and do not embed credentials in job parameters or task values.

## Operations

### Monitoring

OpCon shows each AsyscoAMT job as running, finished OK or failed. The AMT completion code is written to the job log, so check the log of a failed job to see whether it ended with ERROR (11), ABORTED (12), AUTHENTICATION_ERROR (24) or WEB_SERVER_ERROR (23).

The agent connection status in Solution Manager indicates whether the AMT Batch Server answers. It does not test the Batch User, so wrong credentials show up only when a job starts.

### Alerts

Configure OpCon notifications for AsyscoAMT jobs that fail, and use the job log to see which completion code ended the job:

- **11 (ERROR)** — Job ended in error; review the job log for details.
- **12 (ABORTED)** — Job was aborted by application business logic.
- **23 (WEB_SERVER_ERROR)** — Connectivity issue with the AMT Batch Server.
- **24 (AUTHENTICATION_ERROR)** — API credential failure.

### Performance and scaling

The connector communicates with the AMT Batch Server REST-API synchronously. High concurrency of simultaneous AMT jobs may be subject to the AMT Batch Server's own queue and throughput limits. Job log files accumulate over time; the **Retain Log Files** setting controls disk usage.

### Job log

When a task is running within the AMT environment, the job log is written to the database. This includes the information about the task as well as any children of the task. When the task completes, the ACS Integration retrieves the information from the database and adds it to the OpCon job log, making it available via JORS. JobLogs for AsyscoAMT ACS integration tasks can only be viewed within Solution Manager.

## Frequently asked questions

**Why must Submit User remain set to BATCH and Station remain set to OPCON?**

These values are required by the AMT Batch Server to correctly identify and route requests from OpCon. Changing them causes job submission to fail.

**Can I define multiple AsyscoAMT agent connections for different AMT environments?**

Yes. Each connection points to a different Batch Server URL. Jobs select their target connection through the Integration Selection field in the task definition.

**How long are job logs retained?**

Job log files are retained for the number of days specified in the Retain Log Files field on the agent connection. The default is 30 days.

**What happens if the AsyscoAMT Batch Server is unreachable when a job runs?**

The connector cannot log on, so the job fails at start. Its log shows only the job information header, and the reason is in the SMANetCom or relay service log. Check the Batch Server URL and network connectivity, then restart the job.

**Can AsyscoAMT job logs be viewed in the OpCon Legacy Enterprise Manager?**

No. JobLogs for AsyscoAMT ACS integration tasks can only be viewed within Solution Manager.

## Examples

Each job log starts with the job information. For a Batch Job it lists **Task Values**, for a Script job **Parameters**, and a **RunAS User** line appears when **User** is set. The connector then records the AMT batch request number.

**Scenario:** A Batch Job named REPORTTEST01 runs successfully in the DEMO2 application, completing in approximately 30 seconds. The job log shows progress messages and a final `Done` status.

```
---------------------------------------------------------------------------
Job Information -----------------------------------------------------------
Server          : http://<batch-server>:9001
Application     : DEMO2
Submit User     : BATCH
Station         : OPCON
Queue Name      : 
Batch Job       : REPORTTEST01
Task Values     :
StartTask : BatchRequestId nnnn
---------------------------------------------------------------------------
Completion Code : DONE
---------------------------------------------------------------------------
Job Log -------------------------------------------------------------------
"Started Rev: 1.3 AMT: 8.0.24223.0. Params: LOADINBATCHCONTROLLER /R:4280 /USEWORKINGDIR"
"Report test 01 is started"
"Report test 01 user BATCH"
"Report test 01 Value param []"
"Report test 01 is running 0 seconds"
"Report test 01 is running 10 seconds"
"Report test 01 is running 20 seconds"
"Report test 01 test is ended"
"Report execution time: 00:00:30:229"
"Done"
---------------------------------------------------------------------------

```

**Scenario:** A Script Job fails because the PowerShell executable (`pwsh`) is not found on the AMT Batch Server. The job log returns ERROR (11) with a file not found message. Verify that PowerShell is installed on the server at the expected path.

```
---------------------------------------------------------------------------
Job Information -----------------------------------------------------------
Server          : http://<batch-server>:9001
Application     : DEMO2
Submit User     : BATCH
Station         : OPCON
Queue Name      : 
Script Job      : F:\Amt\Scripts\DEMO2\RUN_FILENAMETEST.ps1
Parameters      :
StartTask : BatchRequestId nnnn
---------------------------------------------------------------------------
Completion Code : ERROR
---------------------------------------------------------------------------
Job Log -------------------------------------------------------------------
"Error starting job []"
"[An error occurred trying to start process 'pwsh' with working directory 'F:\\AMT\\SCRIPTS\\DEMO2'. The system cannot find the file specified.]"
---------------------------------------------------------------------------

```

**Scenario:** A long-running Batch Job is terminated by a Kill command issued from OpCon. The job log shows a KILLED completion code with no additional log entries.

```
---------------------------------------------------------------------------
Job Information -----------------------------------------------------------
Server          : http://<batch-server>:9001
Application     : DEMO2
Submit User     : BATCH
Station         : OPCON
Queue Name      : 
Batch Job       : LONGRUNNINGREPORT
Task Values     :
StartTask : BatchRequestId nnnn
---------------------------------------------------------------------------
Completion Code : KILLED
---------------------------------------------------------------------------
Job Log -------------------------------------------------------------------
---------------------------------------------------------------------------
```

## Glossary

**AMT Batch Server** — The Asysco LION component that receives, queues, and runs batch jobs and scripts. The AsyscoAMT ACS connector communicates with the server through its REST-API.

**Batch User** — An OpCon credential object that stores the API user name and password used to authenticate with the AMT Batch Server.

**Completion Code** — A numeric status returned by the AMT Batch Server indicating the final state of a job. Codes are mapped to named statuses such as DONE, ERROR, or KILLED.

**JORS (Job Output Retrieval System)** — The OpCon service that provides access to job logs after a task completes. AsyscoAMT job logs are retrieved from the AMT database and made available through JORS.

**Task Values** — Name=value pairs passed to an AMT Batch Job to modify, override, or extend predefined task attributes at run time.

**Related topics:**

- [Operation](./operation.md)
- [Overview](./overview.md)

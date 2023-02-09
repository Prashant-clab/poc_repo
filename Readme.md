# SQL SERVER INTEGRATION WITH CDR FIO PLUGIN

## INTRODUCTION

    The project uses the Microsoft SQL Server, which consists of various tables that are related to each other.

## TABLE NAMES

    Project uses various tables for various purposes. The table names are as follows:
        1. tbl_cdr_platform_url
        2. tbl_users
        3. tbl_user_requests
        4. tbl_pre_processing
        5. tbl_pre_processing_stats
        6. tbl_processing_stats
        7. tbl_rescan_records
        8. tbl_jobs

    All these tables have their own set of columns and their own purpose in the whole flow of the project.

## TABLES WITH THEIR COLUMNS

### TABLE CDR PLATFORM URL
    This table contains the cdr-fio API URL for the project. It has the columns listed below:
		|--------------------------------------------------------------------------------------------------------------------|
        |"SNO."  | "Name"        | "Type"     | "Description"                                                                |
	    |:-------|---------------|------------|------------------------------------------------------------------------------|
		|1       | `cdr_api_url` | varchar    | This column is used to store the cdr API URL.                                |
		|2.      | `updated_at ` | DateTime   | This column is used to store the timestamp at which the URL has been updated.|
        |--------------------------------------------------------------------------------------------------------------------|
### TABLE USERS
    This table is used to record the data of users who has sent the request on cdr-fio. It has the columns listed below:
        |---------------------------------------------------------------------------------------------------------------------------------|
		|"SNO."  | "Name"         | "Type"       | "Description"                                                                          |
	    |:-------|----------------|--------------|----------------------------------------------------------------------------------------|
        |1.      | `fio_user_id`  |  varchar     | This column is used to store the unique user_id from which we got the request.         |
        |2.      | `created_at`   |  DateTime    | This column is used to store the timestamp on which this entry is created in the table.|
        |3.      | `updated_at`   |  DateTime    | This column is used to store the timestamp on which this user sent the last request.   |
        |---------------------------------------------------------------------------------------------------------------------------------|

### TABLE USER REQUEST
    This table is used to record the data of the user request and the status of the pre-processing and processing steps after the request is completed. It has the columns listed below:
        |------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
        |"SNO."  | "Name"                  | "Type"     | "Description"                                                                                                          |
	    |:-------|-------------------------|------------|------------------------------------------------------------------------------------------------------------------------|
        |1.      | `fio_user_id`           | varchar    | This column is used to store the unique user_id from which we got the request.                                         |
        |2.      | `run_id`                | varchar    | This column is used to store the unique ID (a system-generated UUID) for this request.                                 |
        |3.      | `request_receive_time`  | DateTime   | This column is used to store the timestamp on which the user made the request.                                         |
        |4.      | `pre_process_status`    | Integer    | This column is used to store the status of the pre-process step of the request.                                        |
        |5.      | `process_status`        | Integer    | This column is used to store the status of the process step of the request.                                            |
        |6.      | `final_status`          | Integer    | This column is used to store the final status of the request.                                                          |
        |7.      | `created_at`            | Datetime   | This column is used to store the timestamp on which this entry is created in the table.                                |
        |8.      | `updated_at`            | DateTime   | This column is used to store the timestamp at which the last step was executed and the stats of the step were returned.|
        |------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

    The values for status are used to indicate the pre-processing status of the request. The values and their significance are as follows:
        |-------------------------------------------------------------------------------------------------------------|
        |"values" | "significance"                                                                                    |
        |---------|---------------------------------------------------------------------------------------------------|
        |    `0`  | It indicates that pre-processing for this run_id is not available, PRE PROCESS STATUS NONE.       |
        |    `1`  | It indicates that pre-processing for this run_id is initialising, PRE PROCESS STATUS INITIALIZING.|
        |    `2`  | It indicates that pre-processing for this run_id is in progress, PRE PROCESS STATUS IN PROGRESS.  |
        |    `3`  | It indicates that pre-processing for this run_id failed, PRE PROCESS STATUS FAILED.               |
        |    `4`  | It indicates that pre-processing for this run_id is complete, PRE PROCESS STATUS COMPLETED.       |
        |    `5`  | It indicates that pre-processing for this run_id is stopping, PRE PROCESS STATUS STOPPING.        |
        |    `6`  | It indicates that pre-processing for this run_id has stopped, PRE PROCESS STATUS STOPPED.         |
        |-------------------------------------------------------------------------------------------------------------|

    The values for status are used to indicate the processing status of the request. The values and their significance are as follows:
        |---------------------------------------------------------------------------------------------------------|
        |"values" | "significance"                                                                                |
        |---------|-----------------------------------------------------------------------------------------------|
        |    `0`  | It indicates that pre-processing for this run_id is not available, PROCESS STATUS NONE.       |
        |    `1`  | It indicates that pre-processing for this run_id is initialising, PROCESS STATUS INITIALIZING.|
        |    `2`  | It indicates that pre-processing for this run_id is in progress, PROCESS STATUS IN PROGRESS.  |
        |    `3`  | It indicates that pre-processing for this run_id failed, PROCESS STATUS FAILED.               | 
        |    `4`  | It indicates that pre-processing for this run_id is complete, PROCESS STATUS COMPLETED.       |
        |    `5`  | It indicates that pre-processing for this run_id is stopping, PROCESS STATUS STOPPING.        |
        |    `6`  | It indicates that pre-processing for this run_id has stopped, PROCESS STATUS STOPPED.         |
        |---------------------------------------------------------------------------------------------------------|

    The possible values for the final status are used to indicate the status of the request. The values and their significance are as follows:
        |---------------------------------------------------------------------------------|
        |"values" | "significance"                                                        |
        |---------|-----------------------------------------------------------------------|
        |    `0`  | It indicates that the process is still in progress, FINAL STATUS NONE.|
        |    `3`  | It indicates that the request has failed, FINAL STATUS FAILED.        |
        |    `4`  | It indicates that the request is complete, FINAL STATUS COMPLETED.    |
        |    `5`  | It indicates that the request has stopped, FINAL STATUS STOPPED.      |
        |---------------------------------------------------------------------------------|

### TABLE PRE-PROCESSING
    This table is used to record the data about the pre-processing step and the details about the container on which the pre-processing step is executed. It has the columns listed below:
        |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
        |"SNO."  | "Name"                            | "Type"     | "Description"                                                                                                                             |
	    |:-------|-----------------------------------|------------|-------------------------------------------------------------------------------------------------------------------------------------------|
        |1.      | `fio_user_id`                     | varchar    | This column is used to store the unique user_id from which we got the request.                                                            |
        |2.      | `run_id`                          | varchar    | This column is used to store the unique ID (a system-generated UUID) for this request.                                                    |
        |3.      | `source_storage_account_conn_str` | varchar    | This column is used to store the source storage account connection string sent by the user in the request.                                |
        |4.      | `source_container`                | varchar    | This column is used to store the container name for which the process step is triggered by the user.                                      |
        |5.      | `dest_todo_queue`                 | varchar    | This column is used to store the destination of the do queue, same as run_id.                                                             |
        |6.      | `dest_storage_account_conn_str`   | varchar    | This column is used to store the destination store account connection string sent by the user in the request.                             |
        |7.      | `dest_clean_container`            | varchar    | This column is used to store the container name where the clean files are stored after the pre-processing step.                           |
        |8.      | `dest_audit_container`            | varchar    | This column is used to store the container name where the audit files are stored after the pre-processing step.                           |
        |9.      | `pre_processing_threads`          | Integer    | This column is used to store the number of threads created for pre-processing.                                                            |
        |10.     | `processing_acis_count`           | Integer    | This column is used to store the number of instances created for pre-processing.                                                          |
        |11.     | `created_at`                      | DateTime   | This column is used to store the timestamp on which this entry is created in the table.                                                   |
        |12.     | `updated_at`                      | DateTime   | This column is used to store the timestamp on which this entry was updated in the table.                                                  |
        |13.     | `job_name`                        | Varchar    | This column is used to store the job name assigned for that particular user request.                                                      |
        |14.     | `display_name`                    | Varchar    | This column is used to store the display name for that particular user request.                                                           |
        |15.     | `file_extension_detail`           | json       | This column is used to store the json, which has the count of all the file extensions in the source container.                            |
        |16.     | `scan_start_time`                 | DateTime   | This column is used to store the timestamp when the pre-process started scanning and listing all the files in the source container.       |
        |17.     | `scan_end_time`                   | DateTime   | This column is used to store the timestamp when the pre-process has completed scanning and listing all the files in the source container. |
        |18.     | `prev_scan_end_time`              | DateTime   | This column is used to store the timestamp of the previous scan end time if the rescan is requested for the same run_id and user_id.      |
        |19.     | `rescan_id`                       | Varchar    | This column is used to store the unique ID (a system-generated UUID) if the rescan is requested for the same run_id and user_id.          |
        |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|


### TABLE PRE-PROCESSING STATS
    This table is used to record the pre-processing statistics for each pre-processing step that is executed. It has columns as listed below:
        |----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
        |"SNO."  | "Name"                             | "Type"     | "Description"                                                                                                                           |
	    |:-------|------------------------------------|------------|-----------------------------------------------------------------------------------------------------------------------------------------|
        |1.      | `id`                               | Integer    |  This column is used to store the auto-incremented id for table indexing.                                                               |
        |2.      | `fio_user_id`                      | Varchar    |  This column is used to store the unique user_id from which we got the request.                                                         |
        |3.      | `run_id`                           | Varchar    |  This column is used to store the unique ID (a system-generated UUID) for this request.                                                 |
        |4.      | `start_time`                       | DateTime   |  This column is used to store the timestamp when the pre-process started for a particular run_id.                                       |
        |5.      | `num_source_files`                 | Integer    |  This column is used to store the number of files present in the source container, which was given by the user while making the request.|
        |6.      | `size_source_files`                | Integer    |  This column is used to store the total size of all the files present in the source container.                                          |
        |7.      | `number_of_files_pre_processed`    | Integer    |  The column is used to store the number of files for which the pre-process is complete.                                                 |
        |8.      | `number_of_files_left`             | Integer    |  The column is used to store the number of files left for which pre-processing is yet to be done.                                       |
        |9.      | `number_of_file_types_unsupported` | Integer    |  The column is used to store the number of unsupported files in the source container.                                                   |
        |10.     | `size_of_files_pre_processed`      | Integer    |  The column is used to store the total size of files for which the pre-process is complete.                                             |
        |11.     | `time_elapsed`                     | Integer    |  The column is used to store the total time spent completing the pre-processing.                                                        |
        |12.     | `status`                           | Integer    |  The column is used to indicate the status of the pre-processing.                                                                       |
        |13.     | `end_time`                         | DateTime   |  This column is used to store the timestamp when the pre-process completed for a particular run_id.                                     |
        |14.     | `created_at`                       | DateTime   |  This column is used to store the timestamp on which this entry is created in the table.                                                |
        |15.     | `updated_at`                       | DateTime   |  This column is used to store the timestamp on which this entry was updated in the table.                                               |
        |----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

    The values for status are used to indicate the pre-processing status of the request. The values and their significance are as follows:
        |------------------------------------------------------------------------------------------------------------|
        |"values"| "significance"                                                                                    |
        |--------|---------------------------------------------------------------------------------------------------|
        | `0`    | It indicates that pre-processing for this run_id is not available, PRE PROCESS STATUS NONE.       |
        | `1`    | It indicates that pre-processing for this run_id is initialising, PRE PROCESS STATUS INITIALIZING.|
        | `2`    | It indicates that pre-processing for this run_id is in progress, PRE PROCESS STATUS IN PROGRESS . |
        | `3`    | It indicates that pre-processing for this run_id failed, PRE PROCESS STATUS FAILED.               |
        | `4`    | It indicates that pre-processing for this run_id is complete, PRE PROCESS STATUS COMPLETED.       |
        | `5`    | It indicates that pre-processing for this run_id is stopping, PRE PROCESS STATUS STOPPING.        |
        | `6`    | It indicates that pre-processing for this run_id has stopped, PRE PROCESS STATUS STOPPED.         |
        |------------------------------------------------------------------------------------------------------------|

### TABLE PROCESSING STATS
    This table is used to record the processing statistics of each processing step that is executed. It has columns as listed below:
        |------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
        | "SNO." | "Name"                      | "Type"   | "Description"                                                                                                                          |
	    |:-------|-----------------------------|----------|----------------------------------------------------------------------------------------------------------------------------------------|
        |1.      | `id`                        | Integer  | This column is used to store the auto-incremented id for table indexing.                                                               |
        |2.      | `fio_user_id`               | Varchar  | This column is used to store the unique user_id from which we got the request.                                                         |
        |3.      | `run_id`                    | Varchar  | This column is used to store the unique ID (a system-generated UUID) for this request.                                                 |
        |4.      | `start_time`                | DateTime | This column is used to store the timestamp when the process started for a particular run_id.                                           | 
        |5.      | `end_time`                  | DateTime | This column is used to store the timestamp when the process completed for a particular run_id.                                         |
        |6.      | `time_elapsed`              | Integer  | The column is used to store the total time spent completing the processing.                                                            |
        |7.      | `num_source_files`          | Integer  | This column is used to store the number of files present in the source container, which was given by the user while making the request.|
        |8.      | `number_of_files_processed` | Integer  | This column is used to store the number of files on which the processing is executed.                                                  |
        |9.      | `number_of_files_success`   | Integer  | This column is used to store the number of files that were stored in a clean container after processing was completed.                 |
        |10.     | `number_of_files_failed`    | Integer  | This column is used to store the number of files that were stored in the audit container after processing was completed.               |
        |11.     | `status`                    | Integer  | The column is used to indicate the status of the processing.                                                                           |
        |12.     | `created_at`                | DateTime | This column is used to store the timestamp on which this entry is created in the table.                                                |
        |13.     | `updated_at`                | DateTime | This column is used to store the timestamp on which this entry was updated in the table.                                               |
        |14.     | `size_of_files_processed`   | Integer  | The column is used to store the size of all files on which processing is executed.                                                     |
        |------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

    The values for status are used to indicate the processing status of the request. The values and their significance are as follows:
        |----------------------------------------------------------------------------------------------------------|
        | "values" | "significance"                                                                                |
        |----------|-----------------------------------------------------------------------------------------------|
        | `0`      | It indicates that pre-processing for this run_id is not available, PROCESS STATUS NONE.       |
        | `1`      | It indicates that pre-processing for this run_id is initialising, PROCESS STATUS INITIALIZING.|
        | `2`      | It indicates that pre-processing for this run_id is in progress, PROCESS STATUS IN PROGRESS.  |
        | `3`      | It indicates that pre-processing for this run_id failed, PROCESS STATUS FAILED.               |
        | `4`      | It indicates that pre-processing for this run_id is complete, PROCESS STATUS COMPLETED.       |
        | `5`      | It indicates that pre-processing for this run_id is stopping, PROCESS STATUS STOPPING.        |
        | `6`      | It indicates that pre-processing for this run_id has stopped, PROCESS STATUS STOPPED.         |
        |----------------------------------------------------------------------------------------------------------|

### TABLE RESCAN RECORDS
    This table is used to record all the data about pre-processing and processing when a rescan of run_id is requested. It has columns as listed below:
        |---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
        | "SNO." | "Name"                                      | "Type"   | "Description"                                                                                                                               |
	    |:-------|---------------------------------------------|----------|---------------------------------------------------------------------------------------------------------------------------------------------|
        | 1.     | `id`                                        | Integer  | This column is used to store the auto-incremented id for table indexing.                                                                    |
        | 2.     | `run_id`                                    | Varchar  | This column is used to store the run_id for which this request was made.                                                                    |
        | 3.     | `rescan_id`                                 | Varchar  | This column is used to store the unique ID (a system-generated UUID) for this request.                                                      |
        | 4.     | `created_at`                                | DateTime | This column is used to store the timestamp on which this entry is created in the table.                                                     |
        | 5.     | `pp_pre_processing_threads`                 | Integer  | This column is used to store the number of threads created for pre-processing.                                                              |
        | 6.     | `pp_processing_acis_count`                  | Integer  | This column is used to store the number of instances created for pre-processing.                                                            |
        | 7.     | `pp_created_at`                             | DateTime | This column is used to store the timestamp on which the pre-process is created.                                                             |
        | 8.     | `pp_updated_at`                             | DateTime | This column is used to store the timestamp on which the pre-process is updated.                                                             |
        | 9.     | `pp_scan_start_time`                        | DateTime | This column is used to store the timestamp when the pre-process started scanning and listing all the files in the source container.         |
        | 10.    | `pp_scan_end_time`                          | DateTime | This column is used to store the timestamp when the pre-process has completed the scanning and listed all the files in the source container.|
        | 11.    | `pp_prev_scan_end_time`                     | DateTime | This column is used to store the timestamp of the previous scan end time for the same run_id and user_id.                                   |
        | 12.    | `pp_stats_start_time`                       | DateTime | This column is used to store the timestamp when the pre-process started for a particular run_id.                                            |
        | 13.    | `pp_stats_num_source_files`                 | Integer  | This column is used to store the number of files present in the source container, which was given by the user while making the request.     |
        | 14.    | `pp_stats_size_source_files`                | Integer  | This column is used to store the total size of all the files present in the source container.                                               |
        | 15.    | `pp_stats_number_of_files_pre_processed`    | Integer  | The column is used to store the number of files for which the pre-processing is complete.                                                   |
        | 16.    | `pp_stats_number_of_files_left`             | Integer  | The column is used to store the number of files left for which pre-processing is yet to be done.                                            |
        | 17.    | `pp_stats_number_of_file_types_unsupported` | Integer  | The column is used to store the number of unsupported files in the source container, while pre-processing.                                  |
        | 18.    | `pp_stats_size_of_files_pre_processed`      | Integer  | The column is used to store the total size of files for which the pre-process is complete.                                                  |
        | 19.    | `pp_stats_time_elapsed`                     | Integer  | The column is used to store the total time spent completing the pre-processing.                                                             |
        | 20.    | `pp_stats_status`                           | Integer  | The column is used to indicate the status of the pre-processing.                                                                            |
        | 21.    | `pp_stats_end_time`                         | DateTime | This column stores the timestamp at which the pre-process for a specific run_id was completed.                                              |
        | 22.    | `pp_stats_created_at`                       | DateTime | This column is used to store the timestamp at which this entry was created in the pre-processing table.                                     |
        | 23.    | `pp_stats_updated_at`                       | DateTime | This column is used to store the timestamp on which this entry is updated in the pre-processing table.                                      |
        | 24.    | `p_stats_start_time`                        | DateTime | This column is used to store the timestamp when the process started for a particular run_id.                                                |
        | 25.    | `p_stats_end_time`                          | DateTime | This column is used to store the timestamp when the process completed for a particular run_id.                                              |
        | 26.    | `p_stats_time_elapsed`                      | Integer  | The column is used to store the total time spent completing the processing.                                                                 |
        | 27.    | `p_stats_num_source_files`                  | Integer  | This column is used to store the number of files present in the source container, which was given by the user while making the request.     |
        | 28.    | `p_stats_number_of_files_processed`         | Integer  | This column is used to store the number of files on which the processing is executed.                                                       |  
        | 29.    | `p_stats_number_of_files_success`           | Integer  | This column is used to store the number of files that were stored in a clean container after processing was completed.                      |
        | 30.    | `p_stats_number_of_files_failed`            | Integer  | This column is used to store the number of files that were stored in the audit container after processing was completed.                    |
        | 31.    | `p_stats_status`                            | Integer  | The column is used to indicate the status of the processing while rescanning.                                                               |
        | 32.    | `p_stats_created_at`                        | DateTime | This column is used to store the timestamp on which this entry is created in the processing_stats table.                                    |
        | 33.    | `p_stats_updated_at`                        | DateTime | This column is used to store the timestamp on which this entry is updated in the processing_stats table.                                    |
        | 34.    | `p_stats_size_of_files_processed`           | Integer  | The column is used to store the size of all files on which processing is executed.                                                          |
        |---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

    The values for pp_stats_status and p_stats_status will be the same as those above, which were in the pre_processing_stats table and the processing stats table, respectively.

### TABLE JOBS
    This table is used to record the details for a given job, where job is the end-to-end cleaning of the source container, which includes processing and pre-processing. Only the v2 version of the cdr-fio plugin makes use of it.It has columns as listed below:
        |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
        | "SNO." | "Name"                            | "Type"   | "Description"                                                                                                 |
	    |:-------|-----------------------------------|----------|---------------------------------------------------------------------------------------------------------------|
        | 1.     | `id`                              | Integer  | This column is used to store the auto-incremented id for table indexing.                                      |
        | 2.     | `fio_user_id`                     | Varchar  | This column is used to store the unique user_id from which we got the request.                                |
        | 3.     | `user_name`                       | Varchar  | This column is used to store the user name of the user from which we got the request.                         |
        | 4.     | `run_id`                          | Varchar  | This column is used to store the unique ID (a system-generated UUID) for this request.                        |
        | 5.     | `job_name`                        | Varchar  | This column is used to store the job name assigned for that particular user request.                          |
        | 6.     | `display_name`                    | Varchar  | This column is used to store the display name for that particular user request.                               |
        | 7.     | `source_storage_account_conn_str` | Varchar  | This column is used to store the source storage account connection string sent by the user in the request.    |
        | 8.     | `source_container`                | Varchar  | This column is used to store the container name for which the process step is triggered by the user.          |
        | 9.     | `dest_storage_account_conn_str`   | Varchar  | This column is used to store the destination store account connection string sent by the user in the request. |
        | 10.    | `dest_clean_container`            | Varchar  | This column is used to store the container name where the clean files are stored after the job is done.       |
        | 11.    | `dest_audit_container`            | Varchar  | This column is used to store the container name where the clean files are stored after the job is done.       |
        | 12.    | `status`                          | Integer  | This column is used to store the status of the job.                                                           |
        | 13.    | `created_at`                      | DateTime | This column is used to store the timestamp when the job is created.                                           |
        | 14.    | `updated_at`                      | DateTime | This column is used to store the timestamp when the job is updated.                                           |
        | 15.    | `job_seen`                        | Varchar  | This column is used to store whether the job is seen or not.                                                  |
        | 16.    | `state`                           | Varchar  | This column is used to store the state of the job.                                                            |
        | 17.    | `status_details`                  | Integer  | This column is used to store the status details of the job.                                                   |
        |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|

    The values for status are used to indicate the status of the request. The values and their significance are as follows:
        |-----------------------------------------------------------------------------------------------|
        |"values" | "significance"                                                                      |
        |---------|-------------------------------------------------------------------------------------|
        |    `0`  | It indicates that the job for this run_id is not available, JOB STATUS NONE.        |
        |    `1`  | It indicates that the job for this run_id is initialising, JOB STATUS INITIALIZING. |
        |    `2`  | It indicates that the job for this run_id is in progress, JOB STATUS IN PROGRESS.   |
        |    `3`  | It indicates that the job for this run_id failed, JOB STATUS FAILED.                |
        |    `4`  | It indicates that the job for this run_id is completed, JOB STATUS COMPLETED.       |
        |    `5`  | It indicates that the job for this run_id is stopping, JOB STATUS STOPPING.         |
        |    `6`  | It indicates that the job for this run_id has stopped, JOB STATUS STOPPED.          |
        |    `7`  | It indicates that the job for this run_id is in queue, JOB STATUS QUEUED.           |
        |-----------------------------------------------------------------------------------------------|

    The values for state are used to indicate the state of the request. The values and their significance are as follows:

        |--------------------------------------------------------------------------------------------|
        |"values"         | "significance"                                                           |
        |-----------------|--------------------------------------------------------------------------|
        |`pre-processing` | It indicates that the job is in the pre-processing state, PRE-PROCESSING.|
        |`processing`     | It indicates that the job is in the processing state, PROCESSING.        |
        |--------------------------------------------------------------------------------------------|

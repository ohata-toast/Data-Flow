## Data & Analytics > DataFlow > Release Notes

### December 23, 2025
#### Added Features
* Added engine type
    * V1: it is a legacy engine and is fully compatible with all standard nodes and existing templates.
    * V2: it provides faster performance than V1 with the latest architecture-based engine.

### October 28, 2025
#### Bug Fixes
* Fixed an issue where CPU, Memory, and Network metrics in the Monitoring tab were only displayed for the most recently executed flow.
* Fixed an issue where saving a flow or template configured using a template containing sensitive information would fail. 

### September 23, 2025
#### Feature Updates
* Made modification so that sensitive information is marked with asterisks when setting up nodes.
    * (NHN Cloud) Object Storage > Secret Key
    * (NHN Cloud) CloudTrail > Appkey
    * (NHN Cloud) Log & Crash Search > SecretKey
    * JDBC > Password
    * (Amazon) S3 > Secret Key
* (NHN Cloud) Added **Search Query** attribute to the Log & Crash Search node.

#### Bug Fixes
* Fixed an issue where charts would not be displayed when selecting a flow that had never been run in the Monitoring tab.

### August 26, 2025
#### Feature Updates
* Added a new **Schedule List** tab on the Details screen where you can check the reservation schedule.
* Moved the **Go to Cloud Scheduler Console** button for scheduling flows from the Basic Info tab to the **Schedule List** tab.

### July 29, 2025
#### Feature Updates
* Updated the execution mode setting to be configured at the flow.
* Renamed CloudTrail event names to match the terminology used in the DataFlow console.

#### Bug Fixes
* Fixed an issue where the flow did not terminate properly after draining.
* Fixed an issue where log retrieval requests were still made when the View Recent Logs window was open and the flow had ended.

## June 24, 2025
#### Feature Updates
* Replaced the scheduling functionality to integrate with the Cloud Scheduler service.
* Added an execution mode setting to the source node.
    * STREAMING: Processes data in real time without exiting the flow.
    * BATCH: Processes a set amount of data and then terminates the flow.

### May 27, 2025
#### Feature Updates
* Added new nodes
    * Filter
        * Mutate: A node that can rename fields or transform field values.
    * Sink
        * Stdout: A node that outputs flow events to the log. It can be used for debugging purposes.

#### Bug Fixes
* Fixed an issue where View Logs feature did not function properly when logs accumulated too quickly.

### March 4, 2025
#### Bug Fixes
* Fixed an issue where flow event in/out graphs are not displayed correctly.

### December 24, 2024
#### Feature Updates
* Integrated (Amazon) S3 Sink node and (Amazon) S3 - Parquet Sink node.
* Integrated (NHN Cloud) Object Storage Sink node and (NHN Cloud) Object Storage - Parquet Sink node.

### September 25, 2024
#### Feature Updates
* Stabilized the flow startup process.
  
### August 27, 2024 
#### Feature Updates
* Improved how the Last Executed Time is calculated. 
* Made modifications so that, when displaying the node settings, required items are displayed first.

### July 23, 2024
#### Feature Updates
* Improved to use the enter key to input data when entering data in the array of strings type in the node settings screen.
* Separated the 'Match' setting for the Date node into a 'Source field' setting and a 'Formats' setting.
* Made notifications so that, when starting or ending a flow that has already started or ended, 'FLOW_ALREADY_STARTED'/'FLOW_ALREADY_STOPPED' instead of 'ERROR' appears.
* Made notifications so that, when saving or deleting Log & Crash Search log save settings, 'Save Log & Crash Search save settings' or 'Delete Log & Crash Search save settings' are left in CloudTrail logs.

### July 1, 2024
#### Feature Updates
* Added the feature to set the instance type when running flows 
* (Amazon) Changed the endpoint, region settings for the (Amazon) S3 Source, Sink, and (Amazon) S3 - Parquet Sink nodes from required to optional 
    * The nodes will work correctly if only one of the endpoint, region settings is entered.

#### Bug Fixes
* Fixed an issue where no CloudTrail logs were left when exiting after flow draining, Log & Crash Search logs save settings, enabling and disabling validation.
* Fixed an issue where the scheduling feature was not working intermittently. 
* Fixed an issue where the Cipher node was not working intermittently. 
* Fixed an issue where the (Amazon) S3 Source, Sink, and (Amazon) S3 - Parquet Sink nodes were not able to access the public bucket.
* Fixed an issue where using an unsupported JDBC driver when saving a flow containing a JDBC node with validation disabled would expose `JDBC_UNSUPPORTED_DRIVER` instead of `ERROR`. 
* Fixed an issue where saving a flow containing a Cipher node with validation enabled would expose the appropriate error code instead of `ERROR` if the Cipher node information was entered incorrectly. 
* Fixed an issue where status channge notifications were not sent for flows run by a user who had canceled membership.

### May 28, 2024
#### Feature Updates
* Deleted some settings.
    * Common > Enable Metrics
    * Filter Node Common > Periodic Flush
    * (NHN Cloud) Object Storage > ACL
    * (NHN Cloud) Object Storage > Storage Class
    * (NHN Cloud) Object Storage - Parquet > ACL
    * (NHN Cloud) Object Storage - Parquet > Storage Class
* Made modifications so that, when monitoring for periods longer than 7 days, data would be more precise.

#### Bug Fixes
* Fixed the revision history of unsubscribed users to appear as "UNKNOWN USER" instead of blank.
* Fixed validation of Object Storage, S3 nodes to expose "S3_NO_SUCH_BUCKET" instead of "ERROR" if an invalid bucket name is entered.
* Fixed an issue where node names were different on the flow setup screen and the monitoring screen.
* Fixed an issue where state change notifications were not being sent for flows run via scheduling.

### April 23, 2024
#### Added Features
* Added the flow status change notifications feature

#### Bug Fixes
* Fixed an issue where, when querying flow monitoring with many nodes, the query fails.

### March 26, 2024
#### Added Features
* Added the feature to end after flow draining
    * Added the feature to end a flow after draining that processes all remaining events in the flow.
    * A flow that is draining can be ended directly via End Flow.
    * If the draining ends within the timeout, or if the timeout is exceeded during draining, the flow will end at that point.

### February 27, 2024
#### Feature Updates
* Changed the words "file" and "object" in the descriptions of S3 and OBS nodes to "object".
* Fixed loading UI to appear on flow save, start, stop, and validation requests.
* Made the order of node setup more natural.

#### Bug Fixes
* Fixed an issue where saving flows with S3, OBS Sink nodes would intermittently leave temporary objects for testing during validation.
* Fixed an issue where deleting a flow would not delete the scheduling stored in that flow.
* Fixed an issue where, when creating a flow immediately after activating a project, the flow would fail to run.
* Fixed an issue where, when looking up monitoring for a long period of time, the lookup would fail.
* Fixed an issue where the execution state of a validation feature would go from "Activating" to "Active" faster than it should.

### January 23, 2024
#### Feature Updates
* Fixed a bug where validation would not turn on unless creating the first flow.

### December 19, 2023
#### Added Features
* Added a new node
    * Source
        * Added the feature to run queries against the DB to get data.
    * Sink
        * Added the feature to store data in Object Storage with the parquet type.
        * Added the feature to store data in S3 with the parquet type.

#### Bug Fixes
* Fixed a bug where validation would not turn on properly after creating a flow.

### November 28, 2023
#### Feature Updates
* Added error codes when saving or validating flows.
* Changed to allow you to choose whether or not to use the validation feature.

### October 31, 2023
#### Feature Updates
* Improved the error messages that occur while initializing the DataFlow service environment to be more user-friendly.

### October 17, 2023
#### Feature Updates
* Added the SecretKey property to Log & Crash Search Source nodes.

### September 26, 2023
#### Feature Updates
* Modified to support At Least Once when processing data.
* Added new options for time format in Prefix Settings for S3 and OBS Sink nodes.

#### Bug Fixes
* Fixed a bug where the flow would not terminate if an error occurred during the shutdown of the Log & Crash Search node.
* Fixed a bug where, when copying and running a flow containing a Cipher Filter node, the flow would not work correctly.
* Fixed a bug where, when an error occurred while terminating a flow, a request to terminate again would fail.

### July 25, 2023

#### Bug Fixes

* Modified so that, when a flow fails abnormally during execution, it can resume execution from the last execution point.

### June 27, 2023

#### Feature Updates
* Added a feature to enable Log & Crash Search
    * Added a feature to save flow logs in Log & Crash Search.

#### Bug Fixes
* Modified the activation timing of the View Log button
    * Modified the activation timing of the View Log button to the PREPARING stage.

### March 28, 2023

#### Feature Updates

* Added new nodes
    * Filter
        * Added various data processing methods such as Alter, Date, UUID, Split, and Truncate.
* Added a flow usage exposure feature
    * Added a feature to check flow usage from the console in real time.

#### Bug Fixes

* Fixed a bug where flow usage begins to appear before entering the PREPARING stage.

### February 28, 2023

#### Feature Updates

* Added new nodes
    * Source
        * Added a feature to import data from NHN Cloud Object Storage node.
        * Added a feature to import data through Amazon S3 interface.
        * Added a feature to import data through Apache Kafka.
    * Filter
        * Added a feature to preprocess data in various ways by adding grok, json, csv nodes.

### January 6, 2023

#### Bug Fixes

* Fixed an issue where the first button click after adding a node in the flow edit screen does not work.
* Fixed an issue where a field is not added when the cipher node is configured to add fields.

### December 27, 2022

#### Release of a New Service

* DataFlow is a service that creates and run ETL flows.
* Supports sources listed below.
    * NHN Cloud Log & Crash Search
    * NHN Cloud CloudTrail
* Supports a filter below.
    * Cipher (Required to connect with NHN Cloud Secure Key Manager)
* Supports sinks below.
    * NHN Cloud Object Storage
    * Amazon S3 (Compatible)
    * Apache Kafka

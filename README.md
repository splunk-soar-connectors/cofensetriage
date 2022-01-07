[comment]: # "Auto-generated SOAR connector documentation"
# Cofense Triage

Publisher: Splunk Community  
Connector Version: 2\.0\.1  
Product Vendor: Cofense  
Product Name: Triage  
Product Version Supported (regex): "\.\*"  
Minimum Product Version: 4\.9\.39220  

This app supports investigative actions that enable the security teams to analyze and respond to phishing faster

[comment]: # " File: readme.md"
[comment]: # "  Copyright (c) 2020-2021 Splunk Inc."
[comment]: # ""
[comment]: # "  Licensed under Apache 2.0 (https://www.apache.org/licenses/LICENSE-2.0.txt)"
[comment]: # ""
The asset settings page has several ingestions related settings. This app is currently written to
support the ingestion of either the Cofense Triage Threat Indicators or the Cofense Triage Reports.
This is set by the **ingestion_method** variable by a pull-down menu. If you wish to ingest both
sets of data, it is suggested you set up a second Cofense Triage asset with the same credentials,
but with the variable set to the other value.

**1. Remaining Settings**

<table>
<thead>
<tr class="header">
<th>Setting</th>
<th>Description</th>
<th>Notes</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>max_results</td>
<td>Maximum number of results retrieved during the ingestion run.</td>
<td>This is adjustable to any number. The practical limit is dictated by the number of API calls that your rate limit allows. Each API call will retrieve the maximum allowed of 50 results.</td>
</tr>
<tr class="even">
<td>start_date</td>
<td>The initial start date and time of the ingestion. The default is six days ago.</td>
<td>This setting is used only if there weren't any prior successful ingestions and were ignored afterward. If left blank, it will default to the product setting of six days ago. If one or more results are successfully ingested, the relevant date of the last ingested result is used to set the start date for the next ingestion run. It is important to set this setting to date within a range that contains data.<br />
</td>
</tr>
<tr class="odd">
<td>date_sort</td>
<td>Retrieve either the oldest results first or the latest results first.</td>
<td>This setting is used to set which pages of results to retrieve and how they are sorted. This setting makes the observed assumption that the result ID is ordered by ascending date. ie. ID=1 is an older result than ID=2. If this setting is the <strong>Oldest first</strong> the results are retrieved and sorted with the lowest ID first. If the ingestion run of <strong>max_results</strong> does not completely exhaust this list of results, it will continue to retrieve the oldest entries until all the results are exhausted. If this setting is the <strong>latest first</strong> the results are retrieved and sorted with the highest ID first. We will ingest the newest results first and then work our way down to older results until we hit <strong>max_results</strong> or your rate limit. <em>If older results remaining in the ingestion run, they will be ignored on the next ingestion run.</em> This will always guarantee your latest results are ingested first at the risk of losing older results if it exceeds your <strong>max_result</strong> or rate limit.</td>
</tr>
<tr class="even">
<td>cef_mapping</td>
<td>JSON dictionary is represented as a serialized JSON string. Only applicable if ingesting new artifacts</td>
<td>This parameter is a JSON dictionary represented as a serialized JSON string, such as the result of json.dumps(). Each key in the dictionary is a potential key name in an artifact that is to be renamed to the value. For example, if the cef_mapping is {"website": "requestURL"} your artifact will have requestURL cef fields in place of website cef fields.</td>
</tr>
<tr class="odd">
<td>ingestion_method</td>
<td>Ingestion of either Threat Indicators or Reports</td>
<td>User can select whether to ingest Threat Indicators or Reports</td>
</tr>
</tbody>
</table>

  

**2. Ingestion Settings for Ingesting Threat Indicators**

| Setting      | Description                                                                                                          | Notes                                                                                                                                                                                                                          |
|--------------|----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| threat_type  | Filter results by threat indicator type, default retrieve All. Types are; Subject, Sender, Domain, URL, MD5, SHA256. | These are applied as filters in the API call to retrieved results. You may retrieve all results or filter results by a single type. At the moment, if you wish to ingest two types, it is suggested you create a second asset. |
| threat_level | Filter results by threat indicator level, default retrieve All. Levels are; Malicious, Suspicious, Benign.           | Similar to the threat_type setting, these will allow either all results or filtered to a single level.                                                                                                                         |

  

**3. Ingestion Settings for Ingesting Reports**

| Setting                 | Description                                                                                                                          | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-------------------------|--------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|                         |                                                                                                                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| report_type             | Type of reports to retrieve, default retrieve All.                                                                                   | Possible values are; All - All reports in Inbox, Recon, and Processed folders; Inbox - Uncategorized reports in the Inbox and Recon folders; Processed - Categorized reports in the Processed folder. Be aware that the reports in the Recon folder are ingested only if the option is All or Inbox but not Processed. Reports from the Inbox folder are unreviewed reports and therefore missing any evaluated information                                              |
| report_ingest_subfields | Only applicable if ingesting reports. This option will ingest the dictionary and list fields of the subject as additional artifacts. | If set to true, during ingestion of reports, in addition to the Report Artifact which contains the entire report as an artifact, it will extract various sub-elements and create individual artifacts for the following items; URLs, tags, rules, and attachments.                                                                                                                                                                                                       |
| report_match_priority   | The highest match priority is based on rule hits for the report.                                                                     |                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| report_category_id      | Filter by category ID, default retrieve All.                                                                                         | The category ID (1-5) for processed reports. Takes either string or number. Only valid when retrieving "All" or "Processed" reports. Category IDs correspond to category names as follows: 5 (lowest): Phishing Simulation; 1: Non-Malicious; 2: Spam; 3: Crimeware; 4 (highest): Advanced Threats. You may retrieve all results or filter results by a single category. At the moment, if you wish to ingest two categories, it is suggested you create a second asset. |
| report_tags             | One or more tags of processed reports to filter on. Use commas to separate multiple tags.                                            |                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

**NOTE** : The Triage devices fetch data according to their own rules. If you look carefully at the
logs or the output of the poll-now, you may see the last result from the previous ingestion,
reingested and marked as a duplicate container. This is to guarantee we do not miss any results
since the last ingestion.


### Configuration Variables
The below configuration variables are required for this Connector to operate.  These variables are specified when configuring a Triage asset in SOAR.

VARIABLE | REQUIRED | TYPE | DESCRIPTION
-------- | -------- | ---- | -----------
**base\_url** |  required  | string | Base URL of the Triage appliance
**verify\_server\_cert** |  optional  | boolean | Verify SSL certificate
**api\_email** |  required  | string | API email address for authentication
**api\_token** |  required  | password | API token for authentication
**ingestion\_method** |  optional  | string | You can ingest either Threat Indicators or Reports \(More details in Section 1\: Remaining Settings of the readme/documentation\)
**max\_results** |  optional  | numeric | Maximum number of results to ingest into containers
**start\_date** |  optional  | string | The initial start date and time of the ingestion\. The default is six days ago
**date\_sort** |  optional  | string | Retrieve results either the oldest first or the latest first
**cef\_mapping** |  optional  | string | JSON dictionary represented as a serialized JSON string \(More details in Section 1\: Remaining Settings of the readme/documentation\)
**threat\_type** |  optional  | string | Filter results by threat indicator type, default retrieve All\. Types are\: Subject, Sender, Domain, URL, MD5, SHA256
**threat\_level** |  optional  | string | Filter results by threat indicator level, default retrieve All\. Levels are\: Malicious, Suspicious, Benign
**report\_ingest\_subfields** |  optional  | boolean | Only applicable if ingesting reports\. This option will ingest dictionary and list fields of the subject as additional artifacts
**report\_type** |  optional  | string | Type of reports to retrieve \(More details in Section 3\: Ingestion Settings for Ingesting Reports of the readme/documentation\)
**report\_match\_priority** |  optional  | numeric | The highest match priority based on rule hits for the report
**report\_category\_id** |  optional  | string | Report category ID \(Select any value from the value\_list\) \(More details in Section 3\: Ingestion Settings for Ingesting Reports of the readme/documentation\)
**report\_tags** |  optional  | string | One or more tags of processed reports to filter on\. Use commas to separate multiple tags

### Supported Actions  
[test connectivity](#action-test-connectivity) - Validate the asset configuration for connectivity using the supplied configuration  
[on poll](#action-on-poll) - Callback action for the on\_poll ingest functionality  
[get threat indicators](#action-get-threat-indicators) - Retrieve the subjects, senders, domains, URLs, or MD5 or SHA256 hashes that operators identified in Cofense Triage as threat indicators within a specified timeframe  
[get reports](#action-get-reports) - Retrieve all reports in the Inbox, Recon, and Processed folders that match specified parameters  
[get report](#action-get-report) - Retrieve a single report that matches the specified report ID\. Optionally ingest to a provided label  
[get email](#action-get-email) - Downloads the raw email attachment for the report that matches the specified report ID  
[get file](#action-get-file) - Downloads and vault the attachment that matches the specified attachment ID  
[get reporters](#action-get-reporters) - Retrieves information about reporters, such as their email address and credit score, whether they are VIP reporters, how many reports they reported, and the date and time of their last report  
[get reporter](#action-get-reporter) - Retrieve reporter that matches the specified reporter ID  
[run query](#action-run-query) - Retrieve integration results based on the specified hash \(MD5 or SHA256\) or URL\. Specify only one parameter \(sha256, md5, or URL\) with this method  

## action: 'test connectivity'
Validate the asset configuration for connectivity using the supplied configuration

Type: **test**  
Read only: **True**

#### Action Parameters
No parameters are required for this action

#### Action Output
No Output  

## action: 'on poll'
Callback action for the on\_poll ingest functionality

Type: **ingest**  
Read only: **True**

#### Action Parameters
No parameters are required for this action

#### Action Output
No Output  

## action: 'get threat indicators'
Retrieve the subjects, senders, domains, URLs, or MD5 or SHA256 hashes that operators identified in Cofense Triage as threat indicators within a specified timeframe

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**max\_results** |  required  | If the number of total results exceeds max\_results, return only the first max\_results number of items retrieved | numeric | 
**date\_sort** |  optional  | Retrieve either the oldest results first or the latest results first | string | 
**type** |  optional  | Filter results by threat indicator type, default retrieve All\. Types are\: Subject, Sender, Domain, URL, MD5, SHA256 | string | 
**level** |  optional  | Filter results by threat indicator level, default retrieve All\. Levels are\: Malicious, Suspicious, Benign | string | 
**start\_date** |  optional  | The start date and time of the query\. The default is six days ago | string |  `cofensetriage date` 
**end\_date** |  optional  | The end date and time of the query\. The default is current time | string |  `cofensetriage date` 
**all\_pages** |  optional  | Retrieve all pages or only a specific page and number of results | boolean | 
**page** |  optional  | Ignored if all\_pages is set to true\. The page number of results to retrieve\. The default is zero, which is the same as all pages\. The header of the response contains the number of the next page and the total number of results | numeric | 
**per\_page** |  optional  | Ignored if all\_pages is set to true\. The number of results rendered per page\. The maximum value is 50 results per page | numeric | 
**ingest\_to\_label** |  optional  | If set, ingest report\(s\) into container\(s\) with provided label\. The label must be valid or action will fail | string | 
**tenant** |  optional  | Required if ingest\_to\_label is set\. Must provide a valid tenant name or id or action will fail | string | 
**cef\_mapping** |  optional  | Only applicable if ingesting new artifacts\. This parameter is a JSON dictionary represented as a serialized JSON string, such as the result of json\.dumps\(\)\. Each key in the dictionary is a potential key name in an artifact that is to be renamed to the value\. For example, if the cef\_mapping is \{"website"\:"requestURL"\} your artifact will have requestURL cef fields in place of website cef fields | string | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.all\_pages | boolean | 
action\_result\.parameter\.cef\_mapping | string | 
action\_result\.parameter\.date\_sort | string | 
action\_result\.parameter\.end\_date | string |  `cofensetriage date` 
action\_result\.parameter\.ingest\_to\_label | string | 
action\_result\.parameter\.level | string | 
action\_result\.parameter\.max\_results | numeric | 
action\_result\.parameter\.page | numeric | 
action\_result\.parameter\.per\_page | numeric | 
action\_result\.parameter\.start\_date | string |  `cofensetriage date` 
action\_result\.parameter\.tenant | string | 
action\_result\.parameter\.type | string | 
action\_result\.data\.\*\.container\_id | string |  `cofensetriage container id` 
action\_result\.data\.\*\.created\_at | string |  `cofensetriage date` 
action\_result\.data\.\*\.id | string |  `cofensetriage threat indicator id` 
action\_result\.data\.\*\.operator\_id | string | 
action\_result\.data\.\*\.report\_id | string |  `cofensetriage report id` 
action\_result\.data\.\*\.threat\_key | string | 
action\_result\.data\.\*\.threat\_level | string | 
action\_result\.data\.\*\.threat\_value | string |  `sha256`  `md5`  `url`  `hash`  `domain`  `hostname` 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'get reports'
Retrieve all reports in the Inbox, Recon, and Processed folders that match specified parameters

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**max\_results** |  required  | If the number of total results exceeds max\_results, return only the first max\_results number of items retrieved | numeric | 
**date\_sort** |  optional  | Retrieve either the oldest results first or the latest results first | string | 
**type** |  required  | Type of reports to retrieve, default All\. Possible values are\: All \- All reports in Inbox, Recon, and Processed folders; Inbox \- Uncategorized reports in the Inbox and Recon folders; Processed \- Categorized reports in the Processed folder | string | 
**match\_priority** |  optional  | The highest match priority based on rule hits for the report | numeric |  `cofensetriage match priority` 
**category\_id** |  optional  | Filter by category ID, default retrieves All\. The category ID \(1\-5\) for processed reports\. Takes either string or number\. Only valid when retrieving "All" or "Processed" reports\. Category IDs correspond to category names as follows\: 5 \(lowest\)\: Phishing Simulation; 1\: Non\-Malicious; 2\: Spam; 3\: Crimeware; 4 \(highest\)\: Advanced Threats | string |  `cofensetriage category id` 
**tags** |  optional  | One or more tags of processed reports to filter on\. Use commas to separate multiple tags | string |  `cofensetriage tags` 
**start\_date** |  optional  | The start date and time of the query\. The default is six days ago | string |  `cofensetriage date` 
**end\_date** |  optional  | The end date and time of the query\. The default is current time | string |  `cofensetriage date` 
**all\_pages** |  optional  | Retrieve all pages or only a specific page and number of results | boolean | 
**page** |  optional  | Ignored if all\_pages is set to true\. The page number of results to retrieve\. The default is zero, which is the same as all pages\. The header of the response contains the number of the next page and the total number of results | numeric | 
**per\_page** |  optional  | Ignored if all\_pages is set to true\. The number of results rendered per page\. The maximum value is 50 results per page | numeric | 
**ingest\_to\_label** |  optional  | If set, ingest report\(s\) into container\(s\) with provided label\. The label must be valid or action will fail | string | 
**tenant** |  optional  | Required if ingest\_to\_label is set\. Must provide a valid tenant name or id or action will fail | string | 
**ingest\_subfields** |  optional  | Only applicable if ingesting new containers/artifacts\. This option will ingest dictionary and list fields of the subject as additional artifacts | boolean | 
**cef\_mapping** |  optional  | Only applicable if ingesting new artifacts\. This parameter is a JSON dictionary represented as a serialized JSON string, such as the result of json\.dumps\(\)\. Each key in the dictionary is a potential key name in an artifact that is to be renamed to the value\. For example, if the cef\_mapping is \{"website"\:"requestURL"\} your artifact will have requestURL cef fields in place of website cef fields | string | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.all\_pages | boolean | 
action\_result\.parameter\.category\_id | string |  `cofensetriage category id` 
action\_result\.parameter\.cef\_mapping | string | 
action\_result\.parameter\.date\_sort | string | 
action\_result\.parameter\.end\_date | string |  `cofensetriage date` 
action\_result\.parameter\.ingest\_subfields | boolean | 
action\_result\.parameter\.ingest\_to\_label | string | 
action\_result\.parameter\.match\_priority | numeric |  `cofensetriage match priority` 
action\_result\.parameter\.max\_results | numeric | 
action\_result\.parameter\.page | numeric | 
action\_result\.parameter\.per\_page | numeric | 
action\_result\.parameter\.start\_date | string |  `cofensetriage date` 
action\_result\.parameter\.tags | string |  `cofensetriage tags` 
action\_result\.parameter\.tenant | string | 
action\_result\.parameter\.type | string | 
action\_result\.data\.\*\.category\_id | string |  `cofensetriage category id` 
action\_result\.data\.\*\.container\_id | string |  `cofensetriage container id` 
action\_result\.data\.\*\.id | string |  `cofensetriage report id` 
action\_result\.data\.\*\.location | string | 
action\_result\.data\.\*\.match\_priority | string |  `cofensetriage match priority` 
action\_result\.data\.\*\.md5 | string |  `md5`  `hash` 
action\_result\.data\.\*\.processed\_at | string |  `cofensetriage date` 
action\_result\.data\.\*\.report\_subject | string | 
action\_result\.data\.\*\.reported\_at | string |  `cofensetriage date` 
action\_result\.data\.\*\.reporter\_id | string |  `cofensetriage reporter id` 
action\_result\.data\.\*\.sha256 | string |  `sha256`  `hash` 
action\_result\.data\.\*\.tags | string |  `cofensetriage tags` 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'get report'
Retrieve a single report that matches the specified report ID\. Optionally ingest to a provided label

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**report\_id** |  required  | The ID of the report to retrieve | numeric |  `cofensetriage report id` 
**ingest\_to\_label** |  optional  | If set, ingest report\(s\) into container\(s\) with provided label\. The label must be valid or action will fail | string | 
**tenant** |  optional  | Required if ingest\_to\_label is set\. Must provide a valid tenant name or id or action will fail | string | 
**ingest\_subfields** |  optional  | Only applicable if ingesting new containers/artifacts\. This option will ingest dictionary and list fields of the subject as additional artifacts | boolean | 
**cef\_mapping** |  optional  | Only applicable if ingesting new artifacts\. This parameter is a JSON dictionary represented as a serialized JSON string, such as the result of json\.dumps\(\)\. Each key in the dictionary is a potential key name in an artifact that is to be renamed to the value\. For example, if the cef\_mapping is \{"website"\:"requestURL"\} your artifact will have requestURL cef fields in place of website cef fields | string | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.cef\_mapping | string | 
action\_result\.parameter\.ingest\_subfields | boolean | 
action\_result\.parameter\.ingest\_to\_label | string | 
action\_result\.parameter\.report\_id | numeric |  `cofensetriage report id` 
action\_result\.parameter\.tenant | string | 
action\_result\.data\.\*\.category\_id | string |  `cofensetriage category id` 
action\_result\.data\.\*\.container\_id | string |  `cofensetriage container id` 
action\_result\.data\.\*\.id | string |  `cofensetriage report id` 
action\_result\.data\.\*\.location | string | 
action\_result\.data\.\*\.match\_priority | string |  `cofensetriage match priority` 
action\_result\.data\.\*\.md5 | string |  `md5`  `hash` 
action\_result\.data\.\*\.processed\_at | string |  `cofensetriage date` 
action\_result\.data\.\*\.report\_subject | string | 
action\_result\.data\.\*\.reported\_at | string |  `cofensetriage date` 
action\_result\.data\.\*\.reporter\_id | string |  `cofensetriage reporter id` 
action\_result\.data\.\*\.sha256 | string |  `sha256`  `hash` 
action\_result\.data\.\*\.tags | string |  `cofensetriage tags` 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'get email'
Downloads the raw email attachment for the report that matches the specified report ID

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**report\_id** |  required  | The ID of the report to retrieve | numeric |  `cofensetriage report id` 
**download\_method** |  required  | Download as artifact or as vaulted file | string | 
**create\_vaulted\_file\_artifact** |  optional  | If downloading vaulted file, create an artifact referencing the file | boolean | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.create\_vaulted\_file\_artifact | boolean | 
action\_result\.parameter\.download\_method | string | 
action\_result\.parameter\.report\_id | numeric |  `cofensetriage report id` 
action\_result\.data\.\*\.artifact\_id | string |  `cofensetriage artifact id` 
action\_result\.data\.\*\.filename | string |  `file name` 
action\_result\.data\.\*\.md5 | string |  `md5`  `hash` 
action\_result\.data\.\*\.report\_id | string |  `cofensetriage report id` 
action\_result\.data\.\*\.sha1 | string |  `sha1`  `hash` 
action\_result\.data\.\*\.sha256 | string |  `sha256`  `hash` 
action\_result\.data\.\*\.size | string | 
action\_result\.data\.\*\.vault\_id | string |  `vault id` 
action\_result\.data\.\*\.vaulted | string |  `file path` 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'get file'
Downloads and vault the attachment that matches the specified attachment ID

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**attachment\_id** |  required  | The ID of the attachment to retrieve | numeric |  `cofensetriage attachment id` 
**create\_vaulted\_file\_artifact** |  optional  | If downloading vaulted file, create an artifact referencing the file | boolean | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.attachment\_id | numeric |  `cofensetriage attachment id` 
action\_result\.parameter\.create\_vaulted\_file\_artifact | boolean | 
action\_result\.data\.\*\.artifact\_id | string |  `cofensetriage artifact id` 
action\_result\.data\.\*\.attachment\_id | string |  `cofensetriage attachment id` 
action\_result\.data\.\*\.filename | string |  `file name` 
action\_result\.data\.\*\.md5 | string |  `md5`  `hash` 
action\_result\.data\.\*\.sha1 | string |  `sha1`  `hash` 
action\_result\.data\.\*\.sha256 | string |  `sha256`  `hash` 
action\_result\.data\.\*\.size | string | 
action\_result\.data\.\*\.vault\_id | string |  `vault id` 
action\_result\.data\.\*\.vaulted | string |  `file path` 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'get reporters'
Retrieves information about reporters, such as their email address and credit score, whether they are VIP reporters, how many reports they reported, and the date and time of their last report

Type: **investigate**  
Read only: **True**

If the value of vip field is true, then only vip reporters will be retrieved otherwise, both vip and non\-vip reporters will be retrieved\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**max\_results** |  required  | If the number of total results exceeds max\_results, return only the first max\_results number of items retrieved | numeric | 
**date\_sort** |  optional  | Retrieve either the oldest results first or the latest results first | string | 
**vip** |  optional  | Whether to fetch VIP reporters \(true\) or not \(false\) | boolean | 
**email** |  optional  | The email address of the reporter to fetch\. If you do not pass a value for this parameter, Cofense Triage returns all reporters | string |  `email` 
**start\_date** |  optional  | The start date and time of the query\. The default is six days ago | string |  `cofensetriage date` 
**end\_date** |  optional  | The end date and time of the query\. The default is current time | string |  `cofensetriage date` 
**all\_pages** |  optional  | Retrieve all pages or only a specific page and number of results | boolean | 
**page** |  optional  | Ignored if all\_pages is set to true\. The page number of results to retrieve\. The default is zero, which is the same as all pages\. The header of the response contains the number of the next page and the total number of results | numeric | 
**per\_page** |  optional  | Ignored if all\_pages is set to true\. The number of results rendered per page\. The maximum value is 50 results per page | numeric | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.all\_pages | boolean | 
action\_result\.parameter\.date\_sort | string | 
action\_result\.parameter\.email | string |  `email` 
action\_result\.parameter\.end\_date | string |  `cofensetriage date` 
action\_result\.parameter\.max\_results | numeric | 
action\_result\.parameter\.page | numeric | 
action\_result\.parameter\.per\_page | numeric | 
action\_result\.parameter\.start\_date | string |  `cofensetriage date` 
action\_result\.parameter\.vip | boolean | 
action\_result\.data\.\*\.created\_at | string | 
action\_result\.data\.\*\.credibility\_score | string | 
action\_result\.data\.\*\.email | string |  `email` 
action\_result\.data\.\*\.id | string |  `cofensetriage reporter id` 
action\_result\.data\.\*\.last\_reported\_at | string | 
action\_result\.data\.\*\.reports\_count | string | 
action\_result\.data\.\*\.updated\_at | string | 
action\_result\.data\.\*\.vip | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'get reporter'
Retrieve reporter that matches the specified reporter ID

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**reporter\_id** |  required  | Required\. The ID of the reporter to retrieve | numeric |  `cofensetriage reporter id` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.reporter\_id | numeric |  `cofensetriage reporter id` 
action\_result\.data\.\*\.created\_at | string |  `cofensetriage date` 
action\_result\.data\.\*\.credibility\_score | string | 
action\_result\.data\.\*\.email | string |  `email` 
action\_result\.data\.\*\.id | string |  `cofensetriage reporter id` 
action\_result\.data\.\*\.last\_reported\_at | string |  `cofensetriage date` 
action\_result\.data\.\*\.reports\_count | string | 
action\_result\.data\.\*\.updated\_at | string |  `cofensetriage date` 
action\_result\.data\.\*\.vip | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
action\_result\.summary\.requests\_url | string | 
action\_result\.summary\.requests\_params | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'run query'
Retrieve integration results based on the specified hash \(MD5 or SHA256\) or URL\. Specify only one parameter \(sha256, md5, or URL\) with this method

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**query\_type** |  required  | Type of parameter | string | 
**search\_term** |  required  | Search parameter for query | string |  `sha256`  `md5`  `url`  `hash` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.parameter\.query\_type | string | 
action\_result\.parameter\.search\_term | string |  `sha256`  `md5`  `url`  `hash` 
action\_result\.data | string | 
action\_result\.status | string | 
action\_result\.message | string | 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric | 
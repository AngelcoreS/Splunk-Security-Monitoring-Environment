<h1>Splunk-Security-Monitoring-Environment</h1>

I played the role of an SOC Analyst at a small company called Virtual Space Industries (VSI), which designs virtual-reality programs for businesses. The VSI products that you have been tasked with monitoring include: An Apache web server, which hosts the administrative webpage. A Windows operating system, which runs many of VSI’s back-end operations

- VSI recently experienced several cyberattacks, likely from their adversary JobeCorp.

- Fortunately, your SOC team had set up several monitoring solutions to help VSI quickly identify what was attacked.

- These monitoring solutions will also help VSI create mitigation strategies to protect the organization.

- There is an Attack_Analysis.md file that you can access and see the analysis after the attack

Logs provided:

- <b> Apache Server Logs </b>
  - This server is used for VSI's main public-facing website vsi-company.com.
  - [Apache Logs](Logs/apache_logs.txt)
  - [Apache Attack Logs](Logs/apache_attack_logs.txt)

- <b> Windows Server Logs </b>
  - This server contains intellectual property of VSI’s next-generation virtual-reality programs.
  - [Windows Logs](Logs/windows_server_logs.csv)
  - [Windows Attack Logs](/Logs/windows_server_attack_logs.csv)

<h2>Load and Analyze Apache Logs</h2>

Next, Apache web server logs representing “regular” activity for VSI were uploaded and analyzed:

- Select the “Add Data” option within Splunk. Select the “Upload” option. Select the apache_logs.txt.

- After successful upload, “Start Searching” was selected and the time range set to “All Time.”

</br>

<b><h3>------------------Reports------------------</h3></b>

</br>

- HTTP Methods Report:

Create a report showing a table of the different HTTP methods (GET, POST, HEAD, etc.).

This will provide insight into the type of HTTP activity being requested against their web server.

`source="apache_logs.txt" | top method`

![Method](Pictures/apache/1Method_Apache.png)

- Top 10 Referring Domains Report:

Create a report showing the top 10 domains that refer to VSI’s website.

This will assist VSI with identifying suspicious referrers.

`source="apache_logs.txt" | top limit=10 referer_domain`

![Referer](Pictures/apache/2Referer_domain_apache.png)

- HTTP Response Codes Report:

Creat a report showing the count of each HTTP response code.

This will provide insight into any suspicious levels of HTTP responses.

![Status](Pictures/apache/3Status_code_apache.png)

</br>

<b><h3>------------------Alerts------------------</h3></b>

</br>

- Non-US Activity Alert:

Determine a baseline and threshold for hourly activity from any country besides the United States.

Create an alert that triggers an email to SOC@VSI-company.com when the threshold is reached.

`source="apache_logs.txt" | iplocation clientip | where Country!="United States"`

Analyzing the next picture

![Threshold](Pictures/apache/4threshold_activity_by_location.png)

Conclusion: Baseline for hourly activity is around 80 and the spike is 113 the threshold for hourly activity was set at 115

![Alert](Pictures/apache/4HighNon-USActivity.png)

- HTTP POST Method Alert:

Determine a baseline and threshold for the hourly count of the HTTP POST method.

Create an alert that triggers an email to SOC@VSI-company.com when the threshold is reached.

`source="apache_logs.txt" method=POST`

Analyzing the next picture

![Threshold](Pictures/apache/5threshold_activity_by_POST.png)

Conclusion: Baseline for hourly activity is around 3 and the spike is 7 the threshold for hourly activity was set greater than 7

  ![Alert](Pictures/apache/5HTTPPOSTCount.png)

</br>

  <b><h3>------------------Visualizations and Dashboards------------------</h3></b>

</br>

- Line Chart of HTTP Methods Over Time:

Create a line chart displaying the different HTTP method field values over time, using timechart span=1h count by method.

`source="apache_logs.txt" | timechart span=1h count by method`

![linechart](Pictures/apache/7Chart_Method_apache.png)

- Geographical Map of Client IPs:

Create a geographical map showing the location based on the clientip field.

`source="apache_logs.txt" | iplocation clientip | geostats count`

![map](Pictures/apache/8Chart_Map_clientip.png)

- Number of Different URIs Visualization:

Create a visualization displaying the number of different URIs.

`source="apache_logs.txt" | top limit=10 uri`

![URI](Pictures/apache/9URI_Apache.png)

- Top 10 Countries Visualization:

Create a visualization displaying the count of the top 10 countries appearing in the log.

`source="apache_logs.txt" | iplocation clientip | top limit=10 Country`

![Countries](Pictures/apache/10Chart_Countries_apache.png)

- User Agents Count Visualization:

Create a visualization illustrating the count of different user agents.

`source="apache_logs.txt" | top useragent`

![useragent](Pictures/apache/11useragent_apache.png)

- Single-Value Visualization:
  
Add a single-value visualization analyzing a single data point, such as a radial gauge or marker gauge.

`source="apache_logs.txt" status=200  | timechart span=1h count by status`

![radialgauge](Pictures/apache/12status_apache.png)

<b>Note: I wanted to create a real-time visualization. However, since there have been no incoming logs in the last hour, it shows 0. </b>

</br>

- <b>Dashboard</b>

</br>

![dashboard](Pictures/apache/DASHbeforeattack.png)


<h2>Load and Analyze Windows Servers Logs</h2>

- Select the “Add Data” option within Splunk. Select the “Upload” option. Select the indows_server_logs.csv.

- After successful upload, “Start Searching” was selected and the time range set to “All Time.”
  
</br>

<b><h3>------------------Reports------------------</h3></b>

</br>
- Signatures and Signature IDs Report:

Create a report with a table of signatures and associated signature IDs. Remove duplicate values in the SPL search to ensure a clean report.

`source="windows_server_logs.csv" | table signature signature_id | dedup signature`

![tablesig](Pictures/windows%20server/1Table_signature_and_ID.png)

- Severity Levels Report:

Create a report that displays the severity levels, along with the count and percentage of each.

`source="windows_server_logs.csv" | top severity`

![severity](Pictures/windows%20server/2Severity_lvl_%25.png)

- Success vs. Failure Report:

Create a report comparing the success and failure of Windows activities, using the status field for information.

`source="windows_server_logs.csv" | top status`

![status](Pictures/windows%20server/3SucessVsFailure_Windows.png)


</br>

<b><h3>------------------Alerts------------------</h3></b>

</br>

- Failed Windows Activity Alert:

Determine a baseline and threshold for the hourly level of failed Windows activity.

Create an alert that triggers an email to SOC@VSI-company.com when the threshold is reached.

`source="windows_server_logs.cvs" status="failure"`

Analyzing the next picture

![Threshold](Pictures/windows%20server/Threshold_failure_lvl.png)

Conclusion: Baseline for hourly activity is around 9, the threshold for hourly activity was set at 15

![Alert](Pictures/windows%20server/4Alert_failed_windows.png)

- Successful Login Alert:

Determine a baseline and threshold for the hourly count of the signature “an account was successfully logged on.”

Create an alert based on the corresponding signature ID that triggers an email to SOC@VSI-company.com when the threshold is reached.

`source="windows_server_logs.csv" signature="An account was successfully logged on"`

- Baseline for hourly success of logged on accounts: 12.
- Threshold for hourly success of logged on accounts: 30.

![Alert](Pictures/windows%20server/5alert_for_successful_log_ons_720.png)

- User Account Deletion Alert:

Determine a baseline and threshold for the hourly count of the signature “a user account was deleted.”

Create an alert based on the corresponding signature ID that triggers an email to SOC@VSI-company.com when the threshold is reached.

`source="windows_server_logs.csv" signature_id=4726`

Analyzing the next picture

![Threshold](Pictures/windows%20server/Threshold_user_deleted.png)

Conclusion: Baseline for hourly activity is around 17, the threshold for hourly activity was set at 20

![Alert](Pictures/windows%20server/6Alert_Deleted_User.png)

</br>

  <b><h3>------------------Visualizations and Dashboards------------------</h3></b>

</br>

- Line Chart of Signatures Over Time:

Create a line chart displaying the different signature field values over time, using timechart span=1h count by signature.

`source="windows_server_logs.csv" | timechart span=1h count by signature`

![linecharts](Pictures/windows%20server/7Chart_Signature.png)

- Line Chart of Users Over Time:

Create a line chart displaying the different user field values over time.

`source="windows_server_logs.csv" | timechart span=1h count by user`

![linechartu](Pictures/windows server/8Chart_User.png)

- Count of Different Signatures Visualization:

Create a visualization illustrating the count of different signatures.

`source="windows_server_logs.csv" | top signature`

![barcharts](Pictures/windows%20server/9Bar_count_signature.png)

- Count of Different Users Visualization:

Create a visualization illustrating the count of different users.

`source="windows_server_logs.csv" | top user`

![barchartu](Pictures/windows%20server/10Bar_count_user.png)

- Single-Value Visualization:

Add a single-value visualization analyzing a single data point, such as a radial gauge or marker gauge.

`source="windows_server_logs.csv" severity=high | timechart span=1h count

![radialgau](Pictures/windows%20server/11radialgauge_Highseverity.png)

<b>Note: I wanted to create a real-time visualization. However, since there have been no incoming logs in the last hour, it shows 0. </b>






<h1>Splunk-Security-Monitoring-Environment</h1>

I played the role of an SOC Analyst at a small company called Virtual Space Industries (VSI), which designs virtual-reality programs for businesses. The VSI products that you have been tasked with monitoring include: An Apache web server, which hosts the administrative webpage. A Windows operating system, which runs many of VSI’s back-end operations

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

<b>Reports</b>

- HTTP Methods Report:

Created a report showing a table of the different HTTP methods (GET, POST, HEAD, etc.).

`source="windows_server_logs.csv" | table signature signature_id | dedup signature`

[1](Pictures/apache/1Method_Apache.png)

Top 10 Referring Domains Report:

Created a report showing the top 10 domains that refer to VSI’s website.
Screenshot of the report was taken.
HTTP Response Codes Report:

Created a report showing the count of each HTTP response code.
Screenshot of the report was taken.

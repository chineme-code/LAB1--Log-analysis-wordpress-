# LAB1--Log-analysis-WordPress-
Scenario 
One of our WordPress sites has been compromised but we're unsure how. The primary hypothesis is that an installed plugin was vulnerable to a remote code execution vulnerability which gave an attacker access to the underlying operating system of the server.
To make your GitHub repository appealing to potential employers, I recommend structuring the write-up professionally, adding explanations, and using markdown formatting to improve readability. Below is a polished version of your write-up that you can include in your GitHub repository:

---

# Endpoint Analysis (WordPress)

This project analyses access logs from a WordPress installation to identify potential security issues, such as suspicious IP addresses and vulnerabilities in WordPress plugins.

## Key Learnings and Steps

1. Extracting IP Addresses
To begin the analysis, I extracted the IP addresses from the `access.log` file using the following shell command:
```bash
cat access.log | cut -d ' ' -f 1 | sort | uniq -c | sort -nr > IPs.txt
```
This command extracts the IP addresses, sorts them, removes duplicates, and counts the occurrences. The results are saved in `IPs.txt`.

2. Identifying User Agents
Understanding the user agents accessing the site is crucial for identifying potential threats. I used this command to extract and analyze the user agents:
```bash
cat access.log | cut -d '"' -f 6 | cut -d '[' -f 1 | sort | uniq -c | sort -nr > useragents.txt
```
The output, saved in `useragents.txt`, provides a sorted list of user agents, showing the frequency of each.

3. Filtering POST Requests
Next, I focused on analyzing POST requests, specifically filtering out those that resulted in a "403 Forbidden" response:
```bash
grep 'POST' access.log | grep -v '403' | cut -d ' ' -f 1 | sort | uniq -c | sort -nr > POSTIPs.txt
```
This command helped me identify the source IP addresses of successful POST requests, which are saved in `POSTIPs.txt`.

4. Investigating Suspicious IP Addresses
During the analysis, I identified a suspicious IP address (`103.69.55.212`). I used the following command to extract its activity from the logs:
```bash
grep '103.69.55.212' access.log
```
To further investigate, I checked the IP against AbuseIPDB and VirusTotal. Although VirusTotal returned no results, AbuseIPDB provided valuable insights.

5. Analyzing Suspicious PHP File Uploads
Upon deeper inspection, I found a suspicious PHP file in the logs. I suspected it might be related to a vulnerable WordPress plugin. I searched the logs for GET requests associated with known vulnerable plugins:
```bash
grep 'POST' access.log | grep -i 'contact'
```
This command revealed traffic related to the `contact-form-7` plugin, which I then researched for known vulnerabilities. I also checked for suspicious activity associated with the `simple-file-list` plugin:
```bash
grep 'POST' access.log | grep -i 'simple'
```
This search revealed a PHP file upload, possibly automated using Python requests. To narrow down the activity, I analyzed the associated IP address (`119.241.22.121`):
```bash
grep '119.241.22.121' access.log
```
This command helped me identify a POST request with a login token, indicating potential unauthorized access.

6. OSINT and Further Analysis
To conclude the analysis, I performed Open-Source Intelligence (OSINT) on the `contact-form-7` and `simple-file-list` plugins by searching for GET requests related to their activation:
```bash
grep -i 'contact-form-7' access.log
grep -i 'simple-file-list' access.log
```
These searches helped me identify any meaningful activity related to activating these plugins.

https://blueteamlabs.online/achievement/share/challenge/60157/9#

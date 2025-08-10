**[Website Health Check Logger]{.underline}**

[\#!/bin/bash]{.mark}

[URL=\"https://github.com\"]{.mark}

[LOG_DIR=\"\$HOME/website_health_logs\"]{.mark}

[mkdir -p \"\$LOG_DIR\"]{.mark}

[TODAY=\$(date +\"%Y-%m-%d\")]{.mark}

[LOG_FILE=\"\$LOG_DIR/health_log\_\$TODAY.log\"]{.mark}

[response=\$(curl -o /dev/null -s -A \"Mozilla/5.0\" -w \"%{http_code}
%{time_total}\" \"\$URL\")]{.mark}

[status_code=\$(echo \"\$response\" \| awk \'{ print \$1 }\')]{.mark}

[response_time=\$(echo \"\$response\" \| awk \'{ print \$2 }\')]{.mark}

[timestamp=\$(date +\"%Y-%m-%d %H:%M:%S\")]{.mark}

[echo \"\$timestamp \| Status: \$status_code \| Response Time:
\${response_time}s\" \>\> \"\$LOG_FILE\"]{.mark}

**🎯 Purpose:**

This script checks the **HTTP status code** and **response time** of a
website (e.g., GitHub) and logs the result with a timestamp into a daily
log file.

**🧩 Line-by-line Explanation:**

**✅ 1. Set the target URL**

URL=\"https://github.com\"

→ The website whose health is being monitored.

**✅ 2. Define the log directory**

LOG_DIR=\"\$HOME/website_health_logs\"

mkdir -p \"\$LOG_DIR\"

→ Ensures the log directory exists. \$HOME means the current user's home
directory.  
→ Example: /home/ubuntu/website_health_logs

**✅ 3. Get today\'s date**

TODAY=\$(date +\"%Y-%m-%d\")

LOG_FILE=\"\$LOG_DIR/health_log\_\$TODAY.log\"

→ This creates a unique log file for each day.  
Example: health_log_2025-04-03.log

**✅ 4. Send HTTP request**

response=\$(curl -o /dev/null -s -A \"Mozilla/5.0\" -w \"%{http_code}
%{time_total}\" \"\$URL\")

- -o /dev/null: Discards actual webpage content.

- -s: Silent mode (no progress bar).

- -A: Sets a browser-style User-Agent (helps avoid blocks).

- -w: Formats curl\'s output to show only the **status code** and
  **response time**.

Example Output:

200 0.234

**✅ 5. Extract status code and response time**

status_code=\$(echo \"\$response\" \| awk \'{ print \$1 }\')

response_time=\$(echo \"\$response\" \| awk \'{ print \$2 }\')

- \$1: Status code (200 = OK, 404 = Not found, etc.)

- \$2: Time taken for the request (in seconds)

**✅ 6. Get current timestamp and log result**

timestamp=\$(date +\"%Y-%m-%d %H:%M:%S\")

echo \"\$timestamp \| Status: \$status_code \| Response Time:
\${response_time}s\" \>\> \"\$LOG_FILE\"

→ Appends the status and response time to the day\'s log file.

**📂 Sample Log Output**

2025-04-03 12:01:00 \| Status: 200 \| Response Time: 0.234s

2025-04-03 12:05:00 \| Status: 200 \| Response Time: 0.245s

🧠 **Use Case:** You can schedule this with cron to run every 5 minutes
and monitor website health over time.

**📦Script 2: Archive Yesterday's Log**

[\#!/bin/bash]{.mark}

[LOG_DIR=\"\$HOME/website_health_logs\"]{.mark}

[YESTERDAY=\$(date -d \"yesterday\" +\"%Y-%m-%d\")]{.mark}

[YESTERDAY_LOG=\"\$LOG_DIR/health_log\_\$YESTERDAY.log\"]{.mark}

[YESTERDAY_ARCHIVE=\"\$LOG_DIR/health_log\_\$YESTERDAY.tar.gz\"]{.mark}

[ARCHIVE_HISTORY=\"\$LOG_DIR/archive_history.log\"]{.mark}

[if \[ -f \"\$YESTERDAY_LOG\" \] && \[ ! -f \"\$YESTERDAY_ARCHIVE\" \];
then]{.mark}

[tar -czf \"\$YESTERDAY_ARCHIVE\" \"\$YESTERDAY_LOG\"]{.mark}

[rm \"\$YESTERDAY_LOG\"]{.mark}

[echo \"\$(date) \| Archived \$YESTERDAY_LOG\" \>\>
\"\$ARCHIVE_HISTORY\"]{.mark}

[else]{.mark}

[echo \"\$(date) \| Nothing to archive or already archived\" \>\>
\"\$ARCHIVE_HISTORY\"]{.mark}

[fi]{.mark}

**For Cron Job** :

**First check time zone**: timedatectl

**Switch to IST/Or ur region**: sudo timedatectl set-timezone
Asia/Kolkata

![](media/image1.png){width="5.7171620734908135in"
height="2.2251924759405073in"}

**Cron Job Setup:**

**[Crontab -e]{.mark}**

**Add :**

**I[f u want to run the script at 12:10 AM daily then : 10 0 \* \* \*
Path/to/script]{.mark}**

**🎯 Purpose:**

This script:

1.  Checks if **yesterday\'s log file** exists

2.  Archives it into .tar.gz format (compressed)

3.  Deletes the original .log file

4.  Logs this activity in an archive_history.log file

**🧩 Line-by-line Explanation:**

**✅ 1. Define the directory and date**

LOG_DIR=\"\$HOME/website_health_logs\"

YESTERDAY=\$(date -d \"yesterday\" +\"%Y-%m-%d\")

→ Computes yesterday's date and uses it to locate the log file.

**✅ 2. Define filenames**

YESTERDAY_LOG=\"\$LOG_DIR/health_log\_\$YESTERDAY.log\"

YESTERDAY_ARCHIVE=\"\$LOG_DIR/health_log\_\$YESTERDAY.tar.gz\"

ARCHIVE_HISTORY=\"\$LOG_DIR/archive_history.log\"

→ Determines:

- File to archive

- Archive output file

- History tracker file

**✅ 3. Check and archive**

if \[ -f \"\$YESTERDAY_LOG\" \] && \[ ! -f \"\$YESTERDAY_ARCHIVE\" \];
then

→ Only archive **if**:

- The .log file exists

- The .tar.gz version doesn't already exist

**✅ 4. Archive, remove original, and log**

tar -czf \"\$YESTERDAY_ARCHIVE\" \"\$YESTERDAY_LOG\"

rm \"\$YESTERDAY_LOG\"

echo \"\$(date) \| Archived \$YESTERDAY_LOG\" \>\> \"\$ARCHIVE_HISTORY\"

→ Compresses the file, deletes the original, and logs the event.

**✅ 5. If no action needed**

else

echo \"\$(date) \| Nothing to archive or already archived\" \>\>
\"\$ARCHIVE_HISTORY\"

→ Ensures you still have a record that the script was executed even if
it skipped archiving.

**📂 Sample Archive History Log**

Thu Apr 3 00:01:01 UTC 2025 \| Archived
/home/ubuntu/website_health_logs/health_log_2025-04-02.log

**💡 Bonus: Automation Tip with Crontab**

You can automate both scripts like this:

\# Every 5 minutes - run health check

\*/5 \* \* \* \* /home/ubuntu/scripts/health_check.sh

\# Every day at 12:01 AM - archive yesterday\'s log

1 0 \* \* \* /home/ubuntu/scripts/archive_logs.sh

**✅ Summary**

| **Script**      | **Purpose**                                                    |
|-----------------|----------------------------------------------------------------|
| health_check.sh | Logs website response time + status code into daily logs       |
| archive_logs.sh | Archives and removes yesterday\'s log, maintains a log history |

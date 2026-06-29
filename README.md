Script for Automatic cleanup

#!/bin/bash

#############################################################
# Script Name : log_cleanup.sh
# Purpose     : Cleanup and compress old log files
# Author      : DevOps Team
# Usage       : ./log_cleanup.sh
#############################################################

# Exit immediately if command fails
set -e

# Variables
LOG_DIR="/var/log"
SCRIPT_LOG="/var/log/log_cleanup.log"
DATE=$(date '+%Y-%m-%d %H:%M:%S')

# Counters
COMPRESSED_COUNT=0
DELETED_COUNT=0
LARGE_FILES_COUNT=0

# Start logging
{
    echo "===================================================="
    echo "Log Cleanup Started at: $DATE"
    echo "===================================================="

    #########################################################
    # Step 1: Find large log files (>500MB)
    #########################################################

    echo "[INFO] Searching for log files larger than 500MB..."

    LARGE_FILES=$(find "$LOG_DIR" -type f -name "*.log" -size +500M)

    if [[ -n "$LARGE_FILES" ]]; then
        echo "$LARGE_FILES"

        # Count large files
        LARGE_FILES_COUNT=$(echo "$LARGE_FILES" | wc -l)
    else
        echo "[INFO] No large log files found."
    fi
	
	#########################################################
    # Step 2: Compress logs older than 7 days
    #########################################################

    echo ""
    echo "[INFO] Compressing logs older than 7 days..."

    OLD_LOGS=$(find "$LOG_DIR" -type f -name "*.log" -mtime +7)

    if [[ -n "$OLD_LOGS" ]]; then

        # Loop through each log file
        while IFS= read -r FILE
        do
            echo "Compressing: $FILE"

            gzip "$FILE"

            ((COMPRESSED_COUNT++))

        done <<< "$OLD_LOGS"

    else
        echo "[INFO] No old logs found for compression."
    fi

    #########################################################
    # Step 3: Delete compressed logs older than 30 days
    #########################################################

    echo ""
    echo "[INFO] Deleting compressed logs older than 30 days..."

    OLD_COMPRESSED=$(find "$LOG_DIR" -type f -name "*.gz" -mtime +30)

    if [[ -n "$OLD_COMPRESSED" ]]; then

        while IFS= read -r FILE
        do
            echo "Deleting: $FILE"

            rm -f "$FILE"

            ((DELETED_COUNT++))

        done <<< "$OLD_COMPRESSED"

    else
        echo "[INFO] No old compressed logs found."
    fi

    #########################################################
    # Step 4: Generate Summary
    #########################################################

    echo ""
    echo "================ Summary ================="
    echo "Large Files Found      : $LARGE_FILES_COUNT"
    echo "Logs Compressed        : $COMPRESSED_COUNT"
    echo "Compressed Logs Deleted: $DELETED_COUNT"
    echo "=========================================="

    echo "Cleanup Completed Successfully"

} >> "$SCRIPT_LOG" 2>&1

# Exit successfully
exit 0

==================================================================================

Sample Output
-------------
ubuntu@ip-172-31-36-28:~$ cat ~/log_lab/cleanup_execution.log

# Log Cleanup Started at: 2026-05-26 09:33:13
[INFO] Searching for log files larger than 500MB...
/home/ubuntu/log_lab/nginx/access.log
/home/ubuntu/log_lab/app1/big_app.log
[INFO] Compressing logs older than 7 days...
[INFO] No old logs found for compression.
# [INFO] Deleting compressed logs older than 30 days...
Deleting: /home/ubuntu/log_lab/app1/old_app.log.gz

# Log Cleanup Started at: 2026-05-26 09:34:01
[INFO] Searching for log files larger than 500MB...
/home/ubuntu/log_lab/nginx/access.log
/home/ubuntu/log_lab/app1/big_app.log
[INFO] Compressing logs older than 7 days...
[INFO] No old logs found for compression.
[INFO] Deleting compressed logs older than 30 days...
Deleting: /home/ubuntu/log_lab/app2/old_error.log.gz

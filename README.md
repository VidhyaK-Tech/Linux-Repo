#  Log Cleanup Script

## Overview

`log_cleanup.sh` is a Bash script designed to automate log file maintenance on Linux systems.
It helps in managing disk space by identifying large logs, compressing old logs, and removing outdated compressed files.

---

## Features

* 🔍 Detects log files larger than **500MB**
* 🗜 Compresses `.log` files older than **7 days**
* 🗑 Deletes compressed `.gz` files older than **30 days**
* 📊 Generates execution summary
* 📝 Logs all operations to a script log file

---

## Script Details

| Attribute       | Value                      |
| --------------- | -------------------------- |
| Script Name     | `log_cleanup.sh`           |
| Log Directory   | `/var/log`                 |
| Script Log File | `/var/log/log_cleanup.log` |

---

## Prerequisites

* Linux/Unix system
* Bash shell
* Required permissions to access `/var/log`
* Installed utilities:

  * `find`
  * `gzip`
  * `rm`

---

## Usage

### 1. Make the script executable

```bash
chmod +x log_cleanup.sh
```

### 2. Run the script

```bash
./log_cleanup.sh
```

---

## Automation (Recommended)

You can schedule this script using **cron**.

### Example: Run daily at 2 AM

```bash
crontab -e
```

Add:

```bash
0 2 * * * /path/to/log_cleanup.sh
```

---

##  How It Works

### Step 1: Identify Large Logs

* Finds `.log` files larger than **500MB**

### Step 2: Compress Old Logs

* Compresses `.log` files older than **7 days** using `gzip`

### Step 3: Delete Old Compressed Logs

* Removes `.gz` files older than **30 days**

### Step 4: Summary Report

* Displays:

  * Number of large files found
  * Number of logs compressed
  * Number of logs deleted

---

##  Sample Output

```
====================================================
Log Cleanup Started at: 2026-06-29 02:00:00
====================================================

[INFO] Searching for log files larger than 500MB...
/var/log/syslog.log

[INFO] Compressing logs older than 7 days...
Compressing: /var/log/app.log

[INFO] Deleting compressed logs older than 30 days...
Deleting: /var/log/app.log.gz

================ Summary =================
Large Files Found      : 1
Logs Compressed        : 1
Compressed Logs Deleted: 1
==========================================

Cleanup Completed Successfully
```

---

## Important Notes

* Run with **sudo/root privileges** for full access:

  ```bash
  sudo ./log_cleanup.sh
  ```
* Ensure critical logs are backed up before deletion
* Modify paths if your logs are stored outside `/var/log`

---

## Best Practices

* Test in a staging environment before production use
* Adjust file size and retention thresholds as needed
* Monitor script logs regularly

---

## Author

*VidhyaK-Tech*

---

## 📄 License

This script is free to use and modify for internal and commercial purposes.

# Log Analyzer Script

## 1. Log File (log)

This is the file being monitored. It contains mixed log messages including INFO and ERROR.

```Log
INFO: Application started
ERROR: Failed to connect to the database
INFO: Processing request ID 123
ERROR: Timeout while waiting for response
INFO: Shutdown complete
```
## 2. Python Script (logger.py) 

This script is responsible for analyzing the log file and printing out lines that contain the keyword "ERROR".

### What it does:

- *Opens the log file.*
- *Reads all lines.*
- *Filters out lines with "ERROR".*
- *Prints those error lines to the console.*

**Key Function:**
```python
def check_log_for_errors(log_file_path):
```
**Sample Output**
```
Errors found in log file:
ERROR: Failed to connect to the database
ERROR: Timeout while waiting for response
```

**Full logger.py Code:**
```python
def check_log_for_errors(log_file_path):
    try:
        with open(log_file_path, 'r') as file:
            lines = file.readlines()
        errors = [line for line in lines if 'ERROR' in line]
        
        if errors:
            print("Errors found in log file:")
            for error in errors:
                print(error.strip())
        else:
            print("No errors found in log file.")
    
    except FileNotFoundError:
        print(f"Log file '{log_file_path}' not found.")

if __name__ == "__main__":
    log_file_path = "log"
    check_log_for_errors(log_file_path)
```
### 3. Shell Script (`script.sh`)

This script runs in an infinite loop to repeatedly execute `logger.py` every 20 seconds.

**Main Features:**

- Sets `INTERVAL=20`.
- Uses `while true` loop.
- Calls Python script `logger.py`.
- Sleeps for 20 seconds before repeating.

**Purpose:** Continuously monitors the log file for new errors.

### script.sh file contains bash script
**bash script:**
```python
#!/bin/bash

PYTHON_SCRIPT="logger.py"

#check the log file in every 20 seconds (we pass here seconds)
INTERVAL=20 

echo "Starting the log checker. Press Ctrl+C to stop."

while true
do
    echo "Running the Python script to check for errors..."
    python3 "$PYTHON_SCRIPT"
    echo "Explicitely sleeping for $INTERVAL seconds..."
    sleep $INTERVAL
done
```

### 4. Runner Script (`runner.sh`)

This is the script that kickstarts the entire system.

**It performs:**

- Setting executable permissions for `logger.py` and `script.sh`.
- Launching `script.sh`, which in turn runs `logger.py` in a loop.


## runner.sh file contains bash script
**bash script:**
```bash
#!/bin/bash

# Make logger.py executable
chmod 700 logger.py

# Make script.sh executable
chmod 700 script.sh

echo "Permissions set to 700 for logger.py and script.sh."

echo "Running ./script.sh file to check Error logs"

./script.sh
```

**Output of log analyzer:**
```
Permissions set to 700 for logger.py and script.sh.
Running ./script.sh file to check Error logs
Starting the log checker. Press Ctrl+C to stop.
Running the Python script to check for errors...
Errors found in log file:
ERROR: Failed to connect to the database
ERROR: Timeout while waiting for response
Explicitely sleeping for 20 seconds...
Running the Python script to check for errors...
```

### Overall Flowchart

```sql
[runner.sh]
     ↓
Sets permissions & runs → [script.sh]
                             ↓
        Loop every 20s → [logger.py]
                             ↓
               Reads → [log]
                             ↓
       Filters 'ERROR' → Prints



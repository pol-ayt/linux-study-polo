<center><img src="https://github.com/aaron-clarusway/fullstack/blob/master/itf-logo.png?raw=true"  alt="alt text" width="200"/></center>
<br>

<center><h1> Linux Workshop-6</h1></center>
<p><b>Clarusway</b><img align="right"
  src="https://secure.meetupstatic.com/photos/event/3/1/b/9/600_488352729.jpeg"  width="15px"></p>


<br>

---

### **Linux Filters and Control Operators Lab**

#### **Lab Objectives:**
- Master pipe (`|`) operations for command chaining
- Practice essential filter commands (grep, cut, sort, etc.)
- Implement control operators (`&&`, `||`, `;`, `&`)
- Combine filters and operators for real-world tasks

---

### **Section 1: Filter Commands**

#### **Exercise 1: Basic Filters - Explained**

##### **1. Creating the Sample File**

```bash
echo -e "apple\nbanana\ncherry\ndate\nfig" > fruits.txt
```
- `echo -e`: Enables interpretation of backslash escapes (`\n` = newline)
- Creates a file with 5 fruit names, each on a new line
- File content:
  ```
  apple
  banana
  cherry
  date
  fig
  ```

##### **2. Filter Pipeline Examples**

##### **a. grep Filter**

```bash
cat fruits.txt | grep "a"
```
- `cat`: Outputs file content
- `grep "a"`: Finds lines containing letter "a"
- Output:
  ```
  apple
  banana
  date
  ```

##### **b. cut Filter**

```bash
cat fruits.txt | cut -c1-3
```
- `cut -c1-3`: Extracts characters 1 through 3 from each line
- Output:
  ```
  app
  ban
  che
  dat
  fig
  ```

##### **c. tr Filter**

```bash
cat fruits.txt | tr 'a-z' 'A-Z'
```
- `tr`: Translates characters
- `'a-z' 'A-Z'`: Converts lowercase to uppercase
- Output:
  ```
  APPLE
  BANANA
  CHERRY
  DATE
  FIG
  ```

##### **d. wc Filter**

```bash
cat fruits.txt | wc -l
```
- `wc -l`: Counts lines (`-l` = line count)
- Output:
  ```
  5
  ```

---

#### **Exercise 2: Advanced Filter Chain - Explained**

##### **Processing /etc/passwd**
```bash
cat /etc/passwd | 
  cut -d: -f1,7 |    # Extract username and shell
  grep -v "nologin" | # Exclude system accounts
  sort |             # Alphabetical sort
  tee users.txt |    # Save and pass through
  head -n 5          # Show first 5
```

**Step-by-Step Breakdown:**

1. **Source Data**:
   - `/etc/passwd` format:  
     `username:x:UID:GID:description:home:shell`

2. **cut Command**:
   - `-d:` = Use colon as delimiter
   - `-f1,7` = Extract fields 1 (username) and 7 (shell)
   - Example intermediate output:
     ```
     root:/bin/bash
     daemon:/usr/sbin/nologin
     ```

3. **grep Filter**:
   - `-v "nologin"` = Invert match (exclude lines containing "nologin")
   - Removes system/service accounts

4. **sort Command**:
   - Sorts remaining lines alphabetically by username

5. **tee Command**:
   - Saves output to `users.txt` while also passing it forward
   - Dual purpose: persistent storage + pipeline continuation

6. **head Command**:
   - `-n 5` = Show only first 5 lines
   - Final output example:
     ```
     avahi:/usr/sbin/nologin
     backup:/usr/sbin/nologin
     bin:/usr/sbin/nologin
     daemon:/usr/sbin/nologin
     games:/usr/sbin/nologin
     ```

**Key Concepts Illustrated:**
- Field extraction with `cut`
- Negative matching with `grep -v`
- Data persistence with `tee`
- Pipeline composition

---

#### **Visualization of Data Flow**

```
/etc/passwd
     |
     v
   cut -d: -f1,7    -> username:shell pairs
     |
     v
 grep -v "nologin"  -> exclude service accounts
     |
     v
    sort            -> alphabetical order
     |
     v
    tee             -> save to users.txt
     |
     v
   head -n 5        -> show top 5 results
```

#### **Why This Matters in DevOps**
1. **Log Analysis**: Similar pipelines process server logs
2. **User Management**: Audit system accounts efficiently
3. **Data Transformation**: Prepare data for monitoring tools
4. **Automation**: Build reusable processing chains

----

-------

### **Section 2: Control Operators**

#### **Exercise 3: Sequential Execution - Explained**

##### **1. Semicolon (;) - Unconditional Execution**
```bash
echo "First"; false; echo "Second"
```
- **Behavior**: 
  
  - Runs commands sequentially regardless of previous command's success/failure
  - `false` is a command that always returns non-zero (failure) status
- **Output**:
  ```
  First
  Second
  ```
- **Real-world use**: Chaining unrelated commands where all must execute
  ```bash
  apt update; apt upgrade -y; reboot
  ```

##### **2. Double Ampersand (&&) - Conditional Success**
```bash
mkdir test_dir && cd test_dir && touch test_file
```
- **Behavior**:
  - Each command executes only if the previous succeeded
  - If `mkdir` fails, the entire chain stops
- **Output**: Creates directory, enters it, and creates a file
- **Real-world use**: Safe deployment scripts
  ```bash
  docker build . && docker push myimage:latest
  ```

##### **3. Double Pipe (||) - Conditional Failure**
```bash
rm non_existent_file || echo "File didn't exist"
```
- **Behavior**:
  - Right-side executes only if left-side command fails
  - Acts as an "error handler"
- **Output**: `File didn't exist`
- **Real-world use**: Graceful fallbacks
  ```bash
  kill -9 $PID || echo "Process $PID already terminated"
  ```

---

#### **Exercise 4: Background Jobs - Explained**

##### **1. Ampersand (&) - Background Process**
```bash
sleep 10 &
```
- **Behavior**:
  - Runs command in background (returns shell control immediately)
  - Returns job ID (e.g., `[1] 12345`)
- **Use case**: Long-running tasks while continuing work
  ```bash
  ./deploy.sh > deploy.log &
  ```

##### **2. jobs - Process Management**
```bash
jobs
```
- **Output**:
  ```
  [1]+  Running                 sleep 10 &
  ```
- **Key columns**:
  - Job ID (`[1]`)
  - Status (`Running`)
  - Command

##### **3. fg - Foreground Control**
```bash
fg %1
```
- **Behavior**:
  - Brings job #1 to foreground
  - Shell waits for completion
- **Alternative**:
  ```bash
  bg %1  # Resume background execution if stopped
  ```

---

#### **Control Operator Cheat Sheet**

| Operator | Name      | Behavior                 | Example        |
| -------- | --------- | ------------------------ | -------------- |
| `;`      | Semicolon | Sequential execution     | `cmd1; cmd2`   |
| `&&`     | AND       | Run if previous succeeds | `cmd1 && cmd2` |
| `||`     | OR        | Run if previous fails    | `cmd1 || cmd2` |
| `&`      | Ampersand | Background execution     | `cmd &`        |
| `|`      | Pipe      | Redirect output          | `cmd1 | cmd2`  |

---

#### **Real-World DevOps Examples**

These are some examples from real life implemantations. You can't run these commands as they are depended to real examples, but these can give you some hints about where these codes are implemented.

#### **1. Deployment Pipeline**
```bash
npm test && npm build && scp dist/* prod:/var/www/
```

**What's happening**: Runs tests, builds if tests pass, then deploys to production - a complete CI/CD pipeline in one line.

#### **2. Error-Resilient Cleanup**

```bash
docker stop mycontainer || true && docker rm mycontainer || true
```

**What's happening**: Attempts to stop then remove a container, continuing even if commands fail (|| true suppresses errors).

#### **3. Background Monitoring**

```bash
while true; do kubectl get pods >> pod.log; sleep 30; done &
```

**What's happening**: Continuously logs Kubernetes pod status every 30 seconds in the background (&).

#### **4. Conditional Notifications**

```bash
curl -sSf https://api.service/health || slack-alert "API DOWN"
```

**What's happening**: Checks API health, sends Slack alert only if the check fails (non-zero exit code).

---

#### **Why This Matters**

1. **Script Reliability**: Prevent subsequent commands after failures
2. **Resource Management**: Background long-running tasks
3. **Automation**: Build self-healing workflows
4. **Monitoring**: Continuous checks without blocking

---

---

### **Section 3: Combined Challenge**

#### **Data Processing Pipeline - Explained**

##### **1. Downloading Sample Data**
```bash
curl -sL -o countries.csv \
  https://raw.githubusercontent.com/dr5hn/countries-states-cities-database/master/csv/countries.csv
```
- ##### **curl Options Explained:**

  | Option             | Description                                                  |
  | :----------------- | :----------------------------------------------------------- |
  | `-s`               | Silent mode - hides progress meter and error messages        |
  | `-L`               | Follow redirects - important for GitHub raw URLs which redirect |
  | `-o countries.csv` | Save output to `countries.csv` instead of displaying in terminal |

- **Sample Data Format**:

  ```
  id,name,iso3,iso2,numeric_code,phone_code,capital,currency,currency_name,currency_symbol,...
  4,Afghanistan,AFG,AF,4,93,Kabul,AFN,Afghan afghani,؋,...
  8,Albania,ALB,AL,8,355,Tirana,ALL,Albanian lek,L,...
  ```

*to see the exact table format visit* https://github.com/dr5hn/countries-states-cities-database/blob/master/csv/countries.csv

##### **2. Processing Pipeline Breakdown**

```bash
cat countries.csv |
  grep -v "id" |            # Remove header line
  cut -d',' -f13 |          # Extract regions column (13th field)
  tr 'a-z' 'A-Z' |          # Convert to uppercase
  sort |                    # Sort alphabetically
  uniq |                    # Remove duplicates
  grep -v '^$' |            # Remove blank/empty lines
  tee regions.txt |         # Save to file and pass through
  wc -l                     # Count unique regions
```

**Step-by-Step Transformation**:

1. **Remove Header** (`grep -v "id"`)

   - Input:
     ```
     id,name,iso3,iso2,numeric_code,phone_code,capital,currency,currency_name,currency_symbol,...
     4,Afghanistan,AFG,AF,4,93,Kabul,AFN,Afghan afghani,؋,...
     8,Albania,ALB,AL,8,355,Tirana,ALL,Albanian lek,L,...
     ```
   - Output:
     ```
     4,Afghanistan,AFG,AF,4,93,Kabul,AFN,Afghan afghani,؋,...
     8,Albania,ALB,AL,8,355,Tirana,ALL,Albanian lek,L,...
     ```

2. **Extract Region Column** (`cut -d',' -f13`)

   - `-d','`: Uses comma as delimiter
   - `-f13`: Selects the 13th field
   - Output:
     ```
     Asia
     Europe
     Europe
     Africa
     Oceania ....
     ```

3. **Standardize Case** (`tr 'a-z' 'A-Z'`)
   - Output:
     ```
     ASIA
     EUROPE
     EUROPE
     AFRICA
     OCEANIA ...
     ```

4. **Sort Alphabetically** (`sort`)
   - Output:
     ```
     "CARIBISCH NEDERLAND"
     "ISLE OF MAN"
     AFRICA
     AFRICA
     AFRICA ...
     ```

5. **Remove Duplicates** (`uniq`)
   - Output:

     ```
     "CARIBISCH NEDERLAND"
     "ISLE OF MAN"
     AFRICA
     AMERICAS
     ASIA
     EUROPE
     OCEANIA
     POLAR
     ```

6. **Remove Blank Lines** (`grep -v '^$'`)

   - `^$` is a regex pattern matching empty lines

   - `-v` inverts the match to exclude blanks

7. **Save Output** (`tee regions.txt`)

   - Creates file while continuing pipeline
   - File contents match current output

8. **Count Lines** (`wc -l`)
   - Final output:
     ```
     8
     ```

#### **Verification Commands Explained**

```bash
cat continents.txt  # Displays all unique continents
```
- Shows:
  ```
  "CARIBISCH NEDERLAND"
  "ISLE OF MAN"
  AFRICA
  AMERICAS
  ASIA
  EUROPE
  OCEANIA
  POLAR
  ```

```bash
sort -u continents.txt | wc -l  # Alternative unique count
```
- `-u`: Equivalent to `sort | uniq`
- Validates the count matches the file content

---

#### **Why This Pipeline Matters in DevOps**

1. **Data Preparation**: Transforms raw data into structured formats
2. **ETL Processes**: Mimics Extract-Transform-Load workflows
3. **Log Analysis**: Similar to processing server logs for insights
4. **Automated Reporting**: Creates audit-ready outputs

**Real-World Applications**:
- Processing cloud inventory lists
- Analyzing region/distribution of resources
- Preparing data for monitoring dashboards

---

### **Pipeline Visualization**

```
countries.csv
     |
     v
  grep -v       -> Remove header line
     |
     v
  cut -d, -f2   -> Extract continent column
     |
     v
  tr            -> Standardize case
     |
     v
  sort          -> Alphabetize
     |
     v
  uniq          -> Deduplicate
     | 
     v
  tee           -> Save to file + continue
     |
     v
  wc -l         -> Final count
```

---

---

### **Section 4: Real-World Scenarios**

#### **Challenge: Security Log Analysis**

**Your Task:**  
Create a pipeline that identifies the top 5 IP addresses with failed SSH login attempts from `/var/log/auth.log`, showing:

1. Count of attempts per IP  
2. Sorted from most to least attempts  
3. Only display the IP addresses (not full log lines)

*Bonus: Include the usernames that were attempted*

Log data in `/var/log/auth.log` most likely will be empty as our computers are not real servers. So you can below code to generate some sample data for testing the SSH attack analysis pipeline. 

##### Generate Sample `/var/log/auth.log` Data

```bash
# Create sample failed SSH attempts with random IPs and usernames
sudo bash -c 'cat > /var/log/auth.log <<EOF
Mar 10 14:23:45 server sshd[12345]: Failed password for root from 203.0.113.45 port 54322 ssh2
Mar 10 14:23:47 server sshd[12345]: Failed password for admin from 203.0.113.45 port 54322 ssh2
Mar 10 14:23:50 server sshd[12345]: Failed password for ubuntu from 198.51.100.22 port 42351 ssh2
Mar 10 14:23:52 server sshd[12345]: Failed password for test from 203.0.113.45 port 54322 ssh2
Mar 10 14:23:55 server sshd[12345]: Failed password for root from 192.0.2.128 port 38422 ssh2
Mar 10 14:23:57 server sshd[12345]: Failed password for admin from 203.0.113.45 port 54322 ssh2
Mar 10 14:24:00 server sshd[12345]: Failed password for guest from 198.51.100.22 port 42351 ssh2
Mar 10 14:24:02 server sshd[12345]: Failed password for root from 172.16.254.3 port 58211 ssh2
Mar 10 14:24:05 server sshd[12345]: Failed password for admin from 203.0.113.45 port 54322 ssh2
Mar 10 14:24:07 server sshd[12345]: Failed password for ubuntu from 10.0.0.27 port 49233 ssh2
Mar 10 14:24:10 server sshd[12345]: Failed password for test from 198.51.100.22 port 42351 ssh2
Mar 10 14:24:12 server sshd[12345]: Failed password for root from 203.0.113.45 port 54322 ssh2
Mar 10 14:24:15 server sshd[12345]: Failed password for admin from 192.0.2.128 port 38422 ssh2
Mar 10 14:24:17 server sshd[12345]: Failed password for guest from 203.0.113.45 port 54322 ssh2
Mar 10 14:24:20 server sshd[12345]: Failed password for root from 198.51.100.22 port 42351 ssh2
EOF'
```

---

#### **Solution: SSH Attack Analysis**

```bash
sudo cat /var/log/auth.log |
  grep "sshd.*Failed" |          # Filter SSH failures
  awk '{print $(NF-3)}' |        # Extract IP (4th field from end)
  sort |                         # Prepare for counting
  uniq -c |                      # Count duplicates
  sort -nr |                     # Sort by count (descending)
  head -n 5                      # Show top 5
```

**Sample Output:**
```
   42 203.0.113.45
   23 198.51.100.22
   15 192.0.2.128
    9 172.16.254.3
    5 10.0.0.27
```

**Bonus Solution (with usernames):**

```bash
sudo cat /var/log/auth.log |
  grep "sshd.*Failed" |
  awk '{print $(NF-3), $9}' |    # IP and username
  sort | uniq -c | sort -nr | head -n 5
```

---

---

#### **Key Learning Points**

1. **Log Analysis Patterns**:
   - `grep` → `awk` → `sort` → `uniq -c` is a standard counting pipeline
   - `$(NF-3)` in awk gets the 3rd field from the end

2. **Service Monitoring**:
   - `||` for conditional execution
   - `tee` for dual output (file + stdout)
   - Command grouping with `( )`

---

---

### **Lab Cleanup**

```bash
# Remove created files
rm fruits.txt users.txt countries.txt continents.txt 2>/dev/null

# Kill background processes
jobs -p | xargs -r kill
```

### **Key Concepts Demonstrated:**
1. **Filter Chaining**: Building complex data transformations
2. **Error Handling**: Using `&&` and `||` for robust scripts
3. **Output Management**: `tee` for saving and viewing simultaneously
4. **Background Processing**: Managing long-running tasks

---

**<p align="center">&#9786; Thanks for Attending &#9997;</p>**

<p>Clarusway<img align="right"
  src="https://secure.meetupstatic.com/photos/event/3/1/b/9/600_488352729.jpeg"  width="15px"></p>

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
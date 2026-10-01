# Cowrie SSH Honeypot

## Overview

Cowrie is an open-source SSH and Telnet honeypot designed to detect and record unauthorized access attempts.

This project demonstrates the setup, configuration, testing, and monitoring of a Cowrie SSH honeypot in a Linux environment. The project also includes log monitoring and analysis using Splunk.

## Technologies Used

* Kali Linux
* Cowrie Honeypot
* SSH
* Python
* Twisted
* Splunk
* VirtualBox

## 1. System Preparation

Update the system packages:

```bash
sudo apt update
sudo apt upgrade -y
```

Install the required packages:

```bash
sudo apt install git python3 python3-venv python3-dev libssl-dev libffi-dev build-essential -y
```

## 2. Download Cowrie

Clone the Cowrie repository:

```bash
git clone https://github.com/cowrie/cowrie.git
```

Move into the Cowrie directory:

```bash
cd cowrie
```

## 3. Create Python Virtual Environment

Create a virtual environment:

```bash
python3 -m venv cowrie-env
```

Activate the environment:

```bash
source cowrie-env/bin/activate
```

Install the required Python packages:

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

## 4. Configure Cowrie

Cowrie configuration files are located inside the `etc` directory.

The configuration can be customized according to the lab environment.

Example configuration file:

```text
etc/cowrie.cfg
```

The SSH honeypot was configured to listen on port:

```text
2222
```

## 5. Start Cowrie

Start the Cowrie honeypot using:

```bash
bin/cowrie start
```

Check the status:

```bash
bin/cowrie status
```

The honeypot should display a message indicating that it is ready to accept SSH connections.

## 6. Testing the Honeypot

The honeypot can be tested by connecting to the configured SSH port:

```bash
ssh root@<HONEYPOT-IP> -p 2222
```

Replace `<HONEYPOT-IP>` with the IP address of the Cowrie honeypot.

During testing, Cowrie records authentication attempts, commands entered by users, connection information, and session activity.

## 7. Monitoring Cowrie Logs

Cowrie stores different types of logs containing information about SSH sessions and attacker activity.

Important directories include:

```text
var/log/cowrie/
var/lib/cowrie/
```

TTY session recordings are stored under:

```text
var/lib/cowrie/tty/
```

JSON logs can be used for further analysis and integration with monitoring tools such as Splunk.

## 8. Splunk Log Monitoring

Cowrie JSON logs can be forwarded to Splunk for security monitoring and analysis.

Splunk can be used to identify:

* SSH connection attempts
* Login attempts
* Source IP addresses
* Usernames
* Commands entered
* Session duration
* Authentication activity
* Potential attacker behavior

Example searches can be created to analyze Cowrie events based on source IP, username, command, and event type.

## 9. Security Observations

During testing, Cowrie can capture activities such as:

* Unauthorized SSH connection attempts
* Failed authentication attempts
* Successful honeypot logins
* Commands executed during sessions
* Session duration
* Attacker IP addresses
* SSH client information

These events can be analyzed to understand common SSH attack behavior.

## 10. Project Objectives

The main objectives of this project are:

1. Deploy an SSH honeypot using Cowrie.
2. Capture unauthorized SSH activity.
3. Record attacker commands and session information.
4. Analyze honeypot logs.
5. Integrate security logs with Splunk.
6. Gain practical experience in security monitoring and threat detection.

## 11. Screenshots

Screenshots demonstrating the project setup and testing will be included in this section.

## Conclusion

This project demonstrates the deployment of a Cowrie SSH honeypot and the monitoring of SSH activity in a controlled lab environment.

The project provides practical experience with honeypot deployment, Linux administration, SSH monitoring, log analysis, and security monitoring using Splunk.

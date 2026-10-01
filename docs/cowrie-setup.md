# Cowrie Honeypot Setup

This document describes the installation, configuration, testing, and monitoring of the Cowrie SSH honeypot.

## 1. System Preparation

Update the system packages before installing Cowrie.

```bash
sudo apt update && sudo apt upgrade -y
```

## 2. Install Required Dependencies

Install the packages required for Cowrie.

```bash
sudo apt install -y git python3 python3-pip python3-venv virtualenv libssl-dev libffi-dev build-essential libpython3-dev authbind
```

### Purpose of the packages

* **git** – Used to download the Cowrie source code.
* **python3** – Required to run Cowrie.
* **python3-pip** – Python package installer.
* **python3-venv / virtualenv** – Used to create an isolated Python environment.
* **libssl-dev / libffi-dev** – Required for dependencies.
* **build-essential** – Provides required compilation tools.
* **libpython3-dev** – Required for building Python-related dependencies.
* **authbind** – Allows a non-root process to listen on privileged ports.

## 3. Download Cowrie

Clone the official Cowrie repository and enter the project directory.

```bash
git clone https://github.com/cowrie/cowrie.git
cd cowrie
```

## 4. Create a Python Virtual Environment

Create and activate a dedicated Python environment for Cowrie.

```bash
virtualenv --python=python3 cowrie-env
source cowrie-env/bin/activate
```

The virtual environment keeps Cowrie's Python dependencies isolated from the system Python installation.

## 5. Install Cowrie Dependencies

Upgrade pip and install the required Python packages.

```bash
pip install --upgrade pip
pip install --upgrade -r requirements.txt
```

## 6. Configure Cowrie

Create the active Cowrie configuration file from the default configuration.

```bash
cp etc/cowrie.cfg.dist etc/cowrie.cfg
```

Open the configuration file:

```bash
nano etc/cowrie.cfg
```

## 7. Configure SSH Listening Port

Cowrie can be configured to listen on port **2222**.

```ini
[ssh]
listen_endpoints = tcp:2222:interface=0.0.0.0
```

Port 2222 can be used to avoid conflicts with an existing SSH service running on port 22.

## 8. Enable JSON Logging

Enable JSON event logging in `cowrie.cfg`.

```ini
[output_jsonlog]
enabled = true
```

JSON logs are useful for log analysis and integration with monitoring platforms such as Splunk.

## 9. Enable Text Logging

Make sure text logging is enabled.

```ini
[output_textlog]
enabled = true
```

## 10. Start and Check Cowrie

Start Cowrie:

```bash
bin/cowrie start
```

Check the status:

```bash
bin/cowrie status
```

Stop Cowrie:

```bash
bin/cowrie stop
```

Restart Cowrie after configuration changes:

```bash
bin/cowrie restart
```

## 11. Test the Honeypot

A test SSH connection can be made to the Cowrie service.

```bash
ssh -p 2222 root@localhost
```

Alternatively, use the IP address of the honeypot:

```bash
ssh root@<honeypot-ip> -p 2222
```

This test verifies that SSH connections are being received by Cowrie and recorded in its logs.

## 12. View Cowrie Logs

View the text log in real time:

```bash
tail -F var/log/cowrie/cowrie.log
```

View JSON events:

```bash
jq . var/log/cowrie/cowrie.json | less -R
```

List captured files:

```bash
ls -lah var/lib/cowrie/downloads/
```

Cowrie session recordings can also be replayed using:

```bash
bin/playlog var/lib/cowrie/tty/<session-file>
```

## 13. HoneyFS – Fake Files and Folders

Cowrie provides a simulated filesystem called **HoneyFS**.

Navigate to the HoneyFS directory:

```bash
cd /home/cowrie/cowrie/honeyfs
```

Create a fake folder:

```bash
mkdir secrets
chmod 755 secrets
```

Create a fake file:

```bash
echo "This is a fake password file for attackers." > secrets/passwords.txt
```

Rebuild the filesystem:

```bash
cd /home/cowrie/cowrie
./bin/createfs -l honeyfs -o data/fs.pickle
```

Restart Cowrie:

```bash
bin/cowrie restart
```

The fake files can then be viewed during a test SSH session.

## 14. Log Analysis

View the Cowrie text logs:

```bash
tail -F var/log/cowrie/cowrie.log
```

View JSON event logs:

```bash
jq . var/log/cowrie/cowrie.json | less -R
```

Filter events by source IP:

```bash
grep '"src_ip":' var/log/cowrie/cowrie.json | cut -d '"' -f 4 | sort | uniq -c | sort -nr
```

List captured files:

```bash
ls -lah var/lib/cowrie/downloads/
```

Replay an attacker session:

```bash
bin/playlog var/lib/cowrie/tty/<session-file>
```

## 15. Monitoring

Cowrie logs can be monitored using security and log-analysis tools.

Examples include:

* Splunk
* ELK Stack
* Fail2Ban

In this project, Cowrie JSON logs can be used as a source for security monitoring and analysis.

## 16. Updating Cowrie

To update Cowrie:

```bash
bin/cowrie stop
git pull
source cowrie-env/bin/activate
pip install --upgrade -r requirements.txt
bin/cowrie start
```

This updates the Cowrie source code and its Python dependencies before restarting the honeypot.

## 17. Project Objective

The objective of this project is to deploy an SSH honeypot using Cowrie, capture simulated attacker activity, collect authentication and command events, and analyze the resulting logs.

The project demonstrates practical experience with:

* Linux administration
* SSH
* Python virtual environments
* Honeypot deployment
* Security monitoring
* Log analysis
* JSON-based security events
* Splunk integration

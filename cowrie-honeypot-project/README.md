# SSH Honeypot Monitoring & Attacker Behavior Analysis – Cowrie Project

This project demonstrates how to deploy, configure, and monitor an SSH honeypot using Cowrie on a Kali Linux virtual machine.
The honeypot captures attacker behavior in real time, including:

- SSH login attempts

- executed commands

- system enumeration attempts

- failed commands

- session replay (TTY logs)

- IP address, timestamp, session ID

- potential malware download attempts

- The project mimics the workflow of a SOC Analyst, focusing on threat detection, behavioral analysis, and log interpretation.

## 1. Environment Setup

- OS: Kali Linux (VirtualBox)

- Network Mode: Bridged Adapter

- Honeypot: Cowrie (SSH mode)

- Listening Port: 22

- Python: Virtual environment (cowrie-env)

## 2. Installing Cowrie

```git clone https://github.com/cowrie/cowrie.git```

```cd cowrie```

```python3 -m venv cowrie-env```

```source cowrie-env/bin/activate```

```pip install --upgrade pip```

```pip install -e ```

## 3. Configuring Cowrie to Listen on Port 22

Edit Cowrie’s configuration file:

```nano etc/cowrie.cfg```

Change this line:

```listen_endpoints = tcp:2222:interface=0.0.0.0```

To:

```listen_endpoints = tcp:22:interface=0.0.0.0```

Allow Python to bind to privileged port 22:

```sudo touch /etc/authbind/byport/22```

```sudo chown <username> /etc/authbind/byport/22```

```sudo chmod 755 /etc/authbind/byport/22```

## 4. Starting the Honeypot

``` source cowrie-env/bin/activate ```

```cowrie start```

Check status:

```cowrie status```

Verify port binding:

```sudo ss -tulnp | grep 22```

Expected:

```tcp   LISTEN   0   50   0.0.0.0:22   ... ```

## 5. Attacker Simulation (from another device)

From a phone or another device on the same network (using Termux/Termius):

```ssh root@<honeypot-ip>```

Cowrie accepts any password and drops the attacker into a simulated server:

```root@svr04:~#```

Every command typed is logged.

## 6. Real-Time Log Monitoring
JSON Log (attacker commands, IP, timestamps)

```tail -f var/log/cowrie/cowrie.json```

Example entry:
{"eventid":"cowrie.command.input","input":"uname","session":"abc123","protocol":"ssh"}

Standard Log:

 ```tail -f var/log/cowrie/cowrie.log ```

## 7. TTY Session Replay

Cowrie records full terminal sessions.

Play them back:
cowrie-tty-playback var/lib/cowrie/tty/<session-id>
This replays exactly what the attacker typed, keystroke by keystroke.

## 8. Examples of Captured Behavior

- System fingerprinting

- Attempts to read system info (cat /etc/*release, uname)

- Directory enumeration

- Attempts to view history

- Incorrect POSIX syntax (typical for automated botnet scripts)

- Rapid session open/close

- Brute-force login attempts

- Example logs can be stored in the /logs folder of this repository.

## 9. SOC-Style Analysis

- This honeypot provides insight into:

- attacker tactics and behavior

- SSH enumeration techniques

- login attempts and credential patterns

- command sequences

- how bots behave once landing on a system

- potential malware download attempts

This setup is excellent for studying attacker behavior and practicing incident analysis.

## 10. Skills Demonstrated

- Honeypot deployment (Cowrie)

- Linux system administration

- Virtualization & networking (bridged mode)

- Monitoring SSH traffic

- JSON log analysis

- TTY session analysis

- Threat behavior profiling

- Incident investigation workflow

- Understanding indicators of compromise (IOCs)

## 11. Repository Structure

```.
├── logs/
│   ├── example-cowrie-session.json
│   ├── attacker-commands.txt
│   └── tty-session.txt
│
├── screenshots/
│   ├── port22-listening.png
│   ├── phone-ssh-connection.png
│   └── cowrie-json-log.png
│
└── README.md
```

## 12. Future Improvements

- Integrate with Splunk / ELK (SIEM)

- GeoIP lookup for attacker IPs

- Automated alerting using webhooks

- Deploy additional honeypots (HTTP, FTP, SMB)

- Expose honeypot to WAN for global attacks (with strict isolation)

## 13. Conclusion

This project replicates a real-world attacker interaction within a controlled environment using Cowrie. It is ideal for learning cybersecurity, SOC operations, and attacker behavior analysis.


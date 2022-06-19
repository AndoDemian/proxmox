<h1 align="center" id="heading"> <img src="https://github.com/home-assistant/brands/raw/master/core_integrations/proxmoxve/icon.png?raw=true" alt="proxmox" width="50" style="vertical-align:middle"/> Proxmox </h1>

**Warning**  
PVE 7 only

---

## Change default port from 8006 to 443

Add the following iptables rules:
```bash
iptables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT --to-ports 8006
ip6tables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT --to-ports 8006
```

Make the rules persistent
```bash
sudo apt-get install iptables-persistent
netfilter-persistent save
```

The 8006 port is still there and working in case you have integrations/monitoring in place.

---

## Fix OS Updates for Non-Enterprise

Disabling Enterprise Repository
```bash
sed -i "s/^deb/#deb/g" /etc/apt/sources.list.d/pve-enterprise.list
```

Place the new repo
```bash
echo "deb [arch=amd64] http://download.proxmox.com/debian/pve bullseye pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list
```

Place the gpg key
```bash
wget https://enterprise.proxmox.com/debian/proxmox-release-bullseye.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg 
```

---

## Disable Subscription Pop-up
```bash
echo "DPkg::Post-Invoke { \"dpkg -V proxmox-widget-toolkit | grep -q '/proxmoxlib\.js$'; if [ \$? -eq 1 ]; then { echo 'Removing subscription nag from UI...'; sed -i '/data.status/{s/\!//;s/Active/NoMoreNagging/}' /usr/share/javascript/proxmox-widget-toolkit/proxmoxlib.js; }; fi\"; };" > /etc/apt/apt.conf.d/no-nag-script
apt --reinstall install proxmox-widget-toolkit &>/dev/null
```

---
## Authors

- [@AndoDemian](https://github.com/AndoDemian)
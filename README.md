# Install Asterisk as a Home Assistant Add-on

This guide explains how to **install and configure Asterisk as an add-on in Home Assistant** to access your smart home via phone calls.

---

## 🔧 **1. Prerequisites**
- A running **Home Assistant (Supervisor mode)**
- A **microphone & speaker** for voice commands (if desired)
- A **stable internet connection** for external calls
- A **SIP client** on your phone (e.g., Linphone, Zoiper)

---

## 🚀 **2. Installing the Asterisk Add-on**
1. **Open Home Assistant** and go to **Settings → Add-ons**.
2. Click on **Add-on Store** and search for **Asterisk**.
3. If Asterisk is not available:
   - Click on the three dots (⋮) → "Add repository"
   - Add the following repository:
     ```
     https://github.com/hassio-addons/repository
     ```
   - After that, Asterisk should be installable.
4. Click **Install** and wait for the process to complete.
5. Enable **“Start on boot”** and start the add-on.

---

## ⚙️ **3. Configuring Asterisk via Add-on Settings**

1. Open the **Asterisk Add-on** in Home Assistant.
2. Go to the **"Configuration"** tab.
3. Adjust the configuration and add a SIP user:
   ```yaml
   ami_password: "UR_OWN_PASSWORD"
   auto_add: true
   auto_add_secret: ""
   video_support: false
   generate_ssl_cert: false
   certfile: fullchain.pem
   keyfile: privkey.pem
   additional_sounds: []
   mailbox: false
   mailbox_port: 12345
   mailbox_password: ""
   mailbox_extension: "100"
   mailbox_google_api_key: ""
   log_level: info

   sip:
     enabled: true
     bindport: 5060
     bindaddr: 0.0.0.0
     allow: ulaw,alaw
     users:
       - name: homeuser
         secret: YOUR_SECURE_PASSWORD
         type: friend
         host: dynamic
         context: default
   ```
4. Save the changes and restart the add-on.

---

## 📲 **4. Setting Up a SIP Client on Your Phone**
1. **Download a SIP app** such as **Zoiper or Linphone**.
2. **Create a new SIP account**:
   - **Username:** `homeuser`
   - **Password:** `YOUR_SECURE_PASSWORD`
   - **Server:** `YOUR_HA_IP:5060`
3. **Save and connect**.
4. Test calling **100** to check if the connection works.

---

## 🌍 **5. Enabling External Access**
If you want to access Asterisk from outside your home network, there are two options:

### 🔗 **Option 1: Port Forwarding** (Not Recommended, Security Risk)
- Open **port 5060** in your router and forward it to your **Home Assistant IP**.
- Security risk: Potential target for attacks.

### 🔒 **Option 2: VPN (Recommended)**
- Use a **VPN (e.g., WireGuard or Tailscale)** to securely connect to your home network.
- This protects your SIP system from unauthorized access.

---

## ✅ **6. Automating Home Assistant with Asterisk**
You can use calls to control your smart home.

### Example: **Turn on Lights via Call**
1. Go to **Settings → Automations**.
2. Create a new automation with the following YAML code:
   ```yaml
   alias: Control Lights via Call
   trigger:
     - platform: event
       event_type: asterisk_call
       event_data:
         exten: "100"
   action:
     - service: light.toggle
       target:
         entity_id: light.living_room
   ```
3. When you call **100**, the light toggles on or off.

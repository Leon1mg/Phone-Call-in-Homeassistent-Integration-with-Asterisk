# Asterisk als Home Assistant Add-on installieren

Diese Anleitung beschreibt, wie du **Asterisk als Add-on in Home Assistant** installierst und konfigurierst, um per Telefon auf dein Smart Home zuzugreifen.

---

## 🔧 **1. Voraussetzungen**
- Ein laufender **Home Assistant (Supervisor-Modus)**
- Ein **Mikrofon & Lautsprecher** für Sprachbefehle (falls gewünscht)
- Eine **stabile Internetverbindung** für externe Anrufe
- Ein **SIP-Client** auf deinem Handy (z. B. Linphone, Zoiper)

---

## 🚀 **2. Installation von Asterisk Add-on**
1. **Öffne Home Assistant** und gehe zu **Einstellungen → Add-ons**.
2. Klicke auf **Add-on-Store** und suche nach **Asterisk**.
3. Falls Asterisk nicht verfügbar ist:
   - Klicke auf die drei Punkte (⋮) → "Repository hinzufügen"
   - Füge folgendes Repository hinzu:
     ```
     https://github.com/hassio-addons/repository
     ```
   - Danach sollte Asterisk installierbar sein.
4. Klicke auf **Installieren** und warte, bis der Prozess abgeschlossen ist.
5. Aktiviere **„Starten bei Boot“** und starte das Add-on.

---

## ⚙️ **3. Asterisk konfigurieren über die Add-on-Einstellungen**

1. Öffne das **Asterisk Add-on** in Home Assistant.
2. Gehe zum Tab **„Konfiguration“**.
3. Passe die Konfiguration an und füge einen SIP-Benutzer hinzu:
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
         secret: DEIN_SICHERES_PASSWORT
         type: friend
         host: dynamic
         context: default
   ```
4. Speichere die Änderungen und starte das Add-on neu.

---

## 📲 **4. SIP-Client auf dem Handy einrichten**
1. **Lade eine SIP-App** wie **Zoiper oder Linphone** herunter.
2. **Erstelle ein neues SIP-Konto**:
   - **Benutzername:** `homeuser`
   - **Passwort:** `DEIN_SICHERES_PASSWORT`
   - **Server:** `DEINE_HA_IP:5060`
3. **Speichern und verbinden**.
4. Teste den Anruf an **100**, um zu prüfen, ob die Verbindung funktioniert.

---

## 🌍 **5. Zugriff von außerhalb einrichten**
Wenn du von außerhalb auf Asterisk zugreifen willst, gibt es zwei Wege:

### 🔗 **Option 1: Portfreigabe** (Unsicher, nicht empfohlen)
- Öffne in deinem Router **Port 5060** und leite ihn an die **Home Assistant IP** weiter.
- Sicherheitsrisiko: Mögliches Ziel für Angriffe.

### 🔒 **Option 2: VPN (Empfohlen)**
- Nutze ein **VPN (z. B. WireGuard oder Tailscale)**, um sicher in dein Heimnetz zu gelangen.
- Dies schützt dein SIP-System vor unerlaubtem Zugriff.

---

## ✅ **6. Home Assistant Automatisierung mit Asterisk**
Du kannst Anrufe nutzen, um dein Smart Home zu steuern.

### Beispiel: **Licht per Anruf einschalten**
1. Gehe zu **Einstellungen → Automatisierungen**.
2. Erstelle eine neue Automatisierung mit folgendem YAML-Code:
   ```yaml
   alias: Licht per Anruf steuern
   trigger:
     - platform: event
       event_type: asterisk_call
       event_data:
         exten: "100"
   action:
     - service: light.toggle
       target:
         entity_id: light.wohnzimmer
   ```
3. Wenn du **100 anrufst**, schaltet sich das Licht um.


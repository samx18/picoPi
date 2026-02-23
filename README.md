# Hardware Checklist
- 
- **Power**: Pi 3 Model B wants **5V / 2.5A**
- **microSD**: 16GB minimum for Lite (32GB recommended)
- Ethernet is easiest for first boot; Wi-Fi is fine too.
# Raspberry Pi Setup
## Flash the OS
Use **Raspberry Pi Imager** (official tool).
### In Raspberry Pi Imager
1. **Choose Device**: Raspberry Pi 3 in my case
2. **Choose OS**: _Raspberry Pi OS Lite (64-bit)_ (preferred for modern tooling + ARM64 binaries)
3. **Choose Storage**: your microSD card
4. Click the **gear / settings**  and set:
    - **Hostname**: `<whatever name>`
    - **Enable SSH**: ✅ (prefer “public-key auth” if you have an SSH key; otherwise password)
    - **Username + password**
    - **Wi-Fi** (SSID/password) + **country**
    - **Locale/timezone**
5. Write the card.
## First boot + basic system setup
1. Put the SD in the Pi, connect network, power on.
2. From your laptop:
   ```
   ssh <username>@<hostname>.local
   ```
   ``
3. Update everything:
   ```
   sudo apt update && sudo apt full-upgrade -y  
   sudo reboot
   ```

### Optional (but recommended) hardening for an always-on gateway

- If you used password SSH, consider switching to SSH keys and then disabling password auth.
- Enable a firewall (simple):
  
  ```
  sudo apt install -y ufw
  sudo ufw allow OpenSSH
  sudo ufw enable
  ```

# Picoclaw Setup
## Install PicoClaw (single binary)
### Pick the right build

For Raspberry Pi OS **64-bit**, use the **Linux arm64** release asset. The current releases include `picoclaw_Linux_arm64.tar.gz`

## Initialize PicoClaw + configure hosted LLM
### Create initial config
`picoclaw onboard`
- config file at `~/.picoclaw/config.json`
### Edit config
nano ~/.picoclaw/config.json

**Minimal hosted-provider config (example: OpenAI)**  
(Replace placeholders)

#### Agent Setup
```
{
  "agents": {
    "defaults": {
      "workspace": "~/.picoclaw/workspace",
      "restrict_to_workspace": true,
      "provider": "openai",
      "model": "gpt-4o-mini",
      "max_completion_tokens": 2048,
      "temperature": 0.3,
      "max_tool_iterations": 10
    }
  }
```

#### Provider Setup

```
 "openai": {
      "api_key": "<Your API Key>",
      "api_base": ""
    },
```

#### Channel Setup

```
 "telegram": {
      "enabled": true,
      "token": "<YourToken>",
      "proxy": "",
      "allow_from": [<your userID]
    },
```

# Add a chat channel
## Create a Telegram bot + token (BotFather)
Optional but recommended: restrict who can talk to your bot.
- Message **`@userinfobot`** in Telegram to get your numeric ID.

## Enable Telegram channel in PicoClaw config

```
"channels": {
  "telegram": {
    "enabled": true,
    "token": "<Your Token>",
    "proxy": "",
    "allow_from": ["YOUR_TELEGRAM_USER_ID"]
  }
}

```

## Run the Telegram gateway

```
picoclaw gateway
```
Now DM your new bot on Telegram (“hi”) — you should get a response.

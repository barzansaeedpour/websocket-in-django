"# websocket-in-django" 

# Django WebSocket with Redis via WSL (Ubuntu)

This guide explains how to set up Redis on **Ubuntu (WSL)** and connect it to a **Django Channels** WebSocket application running on Windows.

---

## 🧱 Prerequisites

- Windows 10/11 with **WSL and Ubuntu** installed
- Python + Django + Channels installed on Windows
- Redis setup inside WSL

---

## 🛠️ Step 1: Install Redis in WSL (Ubuntu)

Open your WSL terminal and run:

```bash
sudo apt update
sudo apt install redis-server
```

---

## ▶️ Step 2: Start Redis Server

Start Redis:

```bash
sudo service redis-server start
```

Verify it's running:

```bash
redis-cli ping
```

Expected output:

```
PONG
```

---

## 🔧 Step 3: Configure Redis for External Access

### 3.1 Edit Redis Config File

```bash
sudo nano /etc/redis/redis.conf
```

- Change:
  ```conf
  bind 127.0.0.1 ::1
  ```
  to:
  ```conf
  bind 0.0.0.0
  ```

- Disable protected mode:
  ```conf
  protected-mode no
  ```

Save the file and exit (`Ctrl+X`, then `Y`, then `Enter`).

### 3.2 Restart Redis

```bash
sudo service redis-server restart
```

---

## 🌐 Step 4: Get WSL IP Address

```bash
ip addr | grep inet
```

Look for something like `inet 172.26.112.1`. This is your WSL IP.

---

## ⚙️ Step 5: Update Django Settings

In your Django project `settings.py`:

```python
CHANNEL_LAYERS = {
    "default": {
        "BACKEND": "channels_redis.core.RedisChannelLayer",
        "CONFIG": {
            "hosts": [("172.26.112.1", 6379)],  # Replace with your WSL IP
        },
    },
}
```

---

## ▶️ Step 6: Run Django

Start Redis in WSL:

```bash
sudo service redis-server start
```

Then run Django (on Windows):

```bash
python manage.py runserver
```

Your Django WebSocket app should now be connected to Redis!

---

## 📝 Notes

- WSL IP may change on reboot. Run `ip addr` again if needed.
- For persistent IP or easier networking, consider:
  - Running Django inside WSL
  - Using Docker for Redis
  - Bridging networks

---

## ✅ Troubleshooting

- Make sure Redis is running: `sudo service redis-server status`
- Ensure Django can reach Redis at the correct IP
- Confirm no firewall is blocking port `6379`

---

## 🧪 Test Redis

```bash
redis-cli
127.0.0.1:6379> set testkey "hello"
127.0.0.1:6379> get testkey
"hello"
```

---

## 📚 Resources

- [Django Channels Docs](https://channels.readthedocs.io/)
- [Redis Configuration Reference](https://redis.io/docs/latest/operate/configuration/)
- [WSL Guide](https://learn.microsoft.com/en-us/windows/wsl/)
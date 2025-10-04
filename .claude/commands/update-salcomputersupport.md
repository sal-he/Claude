---
description: Update packages on salcomputersupport.com server and reboot
allowed-tools: Bash
---

Update all packages on salcomputersupport.com server and reboot it automatically.

```bash
echo "Starting package update on salcomputersupport.com..."
ssh sal@salcomputersupport.com 'function apt-updater { sudo apt-get update && sudo apt-get dist-upgrade -Vy && sudo apt-get autoremove -y && sudo apt-get autoclean && sudo apt-get clean && sudo reboot; }; apt-updater'

echo "salcomputersupport.com will automatically reboot after updates complete."
echo "Waiting for server to come back online..."

# Wait for server to come back online
echo "Checking salcomputersupport.com..."
while ! ping -c 1 -W 3 salcomputersupport.com > /dev/null 2>&1; do
    sleep 5
done
echo "✅ salcomputersupport.com is back online and updated!"

echo "🎉 Server has been updated, rebooted, and is back online!"
```

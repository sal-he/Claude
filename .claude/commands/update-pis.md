---
description: Update all packages on three Pi servers (zabbix, raspberrypi, ubuntu-server) and reboot them
allowed-tools: Bash
---

Update all packages on the three Pi servers and reboot them automatically.
Updates zabbix first, then the other two servers in parallel:

```bash
# Update zabbix first
echo "Starting package update on zabbix (192.168.86.166)..."
ssh sal@192.168.86.166 'function apt-updater { sudo apt-get update && sudo apt-get dist-upgrade -Vy && sudo apt-get autoremove -y && sudo apt-get autoclean && sudo apt-get clean && sudo reboot; }; apt-updater'

echo "Zabbix will automatically reboot after updates complete."
echo "Waiting for zabbix to come back online..."

# Wait for zabbix to come back online
echo "Checking zabbix..."
while ! ping -c 1 -W 3 192.168.86.166 > /dev/null 2>&1; do
    sleep 5
done
echo "✅ zabbix is back online and updated!"

# Now start updates on the other two servers in parallel
echo "Starting package updates on raspberrypi and ubuntu-server..."
ssh -o StrictHostKeyChecking=no sal@192.168.86.36 'function apt-updater { sudo apt-get update && sudo apt-get dist-upgrade -Vy && sudo apt-get autoremove -y && sudo apt-get autoclean && sudo apt-get clean && sudo reboot; }; apt-updater' &
ssh -o StrictHostKeyChecking=no sal@192.168.86.25 'function apt-updater { sudo apt-get update && sudo apt-get dist-upgrade -Vy && sudo apt-get autoremove -y && sudo apt-get autoclean && sudo apt-get clean && sudo reboot; }; apt-updater' &
wait

echo "Started package updates on raspberrypi and ubuntu-server"
echo "Both servers will automatically reboot after updates complete."
echo "Waiting for servers to come back online..."

# Wait for remaining servers to come back online
echo "Checking raspberrypi..."
while ! ping -c 1 -W 3 192.168.86.36 > /dev/null 2>&1; do
    sleep 5
done
echo "✅ raspberrypi is back online and updated!"

echo "Checking ubuntu-server..."
while ! ping -c 1 -W 3 192.168.86.25 > /dev/null 2>&1; do
    sleep 5
done
echo "✅ ubuntu-server is back online and updated!"

echo "🎉 All Pi servers have been updated, rebooted, and are back online!"
```


To report charging or discharging status

cat /sys/class/power_supply/qcom-smbchg-usb/status


To disconnect
echo 0 | sudo tee /sys/class/power_supply/qcom-smbchg-usb/online

To reconnect
echo 1 | sudo tee /sys/class/power_
supply/qcom-smbchg-usb/online

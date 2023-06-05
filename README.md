# ht1303
for ht1303 lora package forwarder



make clean all






How to auto-start the with systemd
==================================

## Create a new systemd service

Update the lora_pkt_fwd.service file with proper paths and options, then

```console
sudo cp lora_pkt_fwd.service /etc/systemd/system
```

### Enable the service for autostart with

```console
git clone https://github.com/livinghuang/ht1303.git
cd ht1303/
tar -xvf sx130x_hal.tar
cd sx130x_hal/
make clean all
cd packet_forwarder/
nano reset_lgw.sh
```

to set the reset pin base the convert board and ht1303 module
in the converter board do not use power control. it also could mark 
```nano reset_lgw.sh
SX1302_RESET_PIN=17   # SX1302 reset
```


cd tools/
cd systemd/
sudo cp lora_pkt_fwd.conf /etc/rsyslog.d
sudo systemctl restart rsyslog
sudo systemctl start lora_pkt_fwd.service
sudo systemctl status lora_pkt_fwd.service
sudo systemctl daemon-reload
sudo systemctl enable lora_pkt_fwd.service
sudo journalctl -u lora_pkt_fwd -f
sudo reboot
```

### The following commands to disable the service, manually start/stop it:

```console
sudo systemctl disable lora_pkt_fwd.service
```

```console
sudo systemctl start lora_pkt_fwd.service
```

```console
sudo systemctl stop lora_pkt_fwd.service
```

## Configure rsyslog to redirect the packet forwarder logs into a dedicated file

```console
sudo cp lora_pkt_fwd.conf /etc/rsyslog.d
sudo systemctl restart rsyslog
```

### See the logs

```console
sudo journalctl -u lora_pkt_fwd -f
```

or

```console
cat /var/log/lora_pkt_fwd.log
```

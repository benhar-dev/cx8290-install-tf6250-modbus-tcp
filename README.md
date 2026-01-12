# Installing TF6250 Modbus TCP on the CX8290

## Disclaimer

This guide is a personal project and not a peer-reviewed publication or sponsored document. It is provided “as is,” without any warranties—express or implied—including, but not limited to, accuracy, completeness, reliability, or suitability for any purpose. The author(s) shall not be held liable for any errors, omissions, delays, or damages arising from the use or display of this information.

All opinions expressed are solely those of the author(s) and do not necessarily represent those of any organization, employer, or other entity. Any assumptions or conclusions presented are subject to revision or rethinking at any time.

Use of this information, code, or scripts provided is at your own risk. Readers are encouraged to independently verify facts. This content does not constitute professional advice, and no client or advisory relationship is formed through its use.

## Description

This is a simple "how to" guide for installing the basic configuration of Modbus TCP server on a CX8290 running Beckhoff Realtime Linux.

## Manual Steps

1. Install Driver

```bash
sudo apt update && sudo apt install tf6250-modbus-tcp
```

2. Configure Firewall
   Create the rule file:

```bash
sudo nano /etc/nftables.conf.d/60-modbus-tcp.conf
```

Paste the following content to allow traffic on port 502 (this example enables both interface end0 and tctap0):

```bash
table inet filter {
  chain input {
    # accept Modbus on the top X001 port of the CX8290
    iifname { "end0", "tctap0" } tcp dport 502 accept

    ## to enable them individually, you can use the following,
    #iifname "end0" tcp dport 502 accept
    #iifname "tctap0" tcp dport 502 accept
  }
}
```

3. Apply & Verify

```bash
sudo systemctl reload nftables
sudo nft list ruleset
```

## Quick Start for X001, X101 and X102 (One-Liner)

Run this single command to install the driver, write the config file, and reload the firewall automatically for X001, X101 and X102:

```bash
sudo apt update && \
sudo apt install -y tf6250-modbus-tcp && \
printf "table inet filter {\n  chain input {\n    # accept Modbus\n    iifname { \"end0\", \"tctap0\" } tcp dport 502 accept\n  }\n}\n" | sudo tee /etc/nftables.conf.d/60-modbus-tcp.conf > /dev/null && \
sudo systemctl reload nftables && \
sudo nft list ruleset
```

## Quick Start for X001 (One-Liner)

Run this single command to install the driver, write the config file, and reload the firewall automatically for X001:

```bash
sudo apt update && \
sudo apt install -y tf6250-modbus-tcp && \
printf "table inet filter {\n  chain input {\n    # accept Modbus\n    iifname \"end0\" tcp dport 502 accept\n  }\n}\n" | sudo tee /etc/nftables.conf.d/60-modbus-tcp.conf > /dev/null && \
sudo systemctl reload nftables && \
sudo nft list ruleset
```

## Quick Start for X101, X102 (One-Liner)

Run this single command to install the driver, write the config file, and reload the firewall automatically for X101 and X102:

```bash
sudo apt update && \
sudo apt install -y tf6250-modbus-tcp && \
printf "table inet filter {\n  chain input {\n    # accept Modbus\n    iifname \"tctap0\" tcp dport 502 accept\n  }\n}\n" | sudo tee /etc/nftables.conf.d/60-modbus-tcp.conf > /dev/null && \
sudo systemctl reload nftables && \
sudo nft list ruleset
```

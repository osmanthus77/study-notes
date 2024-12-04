#!/usr/bin/env bash

echo "====> Install softwares via apt-get <===="

echo "==> disabling the release upgrader"
sudo sed -i.bak 's/^Prompt=.*$Prompt=never/' /etc/update-manager/release-upgrades


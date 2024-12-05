#!/usr/bin/env bash

echo "====> Install softwares via apt-get <===="

echo "==> disabling the release upgrader"
sudo sed -i.bak 's/^Prompt=.*$Prompt=never/' /etc/update-manager/release-upgrades   #修改系统文件并替换其中一行内容
#sudo（superuser do管理员权限）。sed（stream editor对文件或输入流进行批量文本处理），后输出处理后的文本，源文件内容不变。
#-i（in-place editing就地编辑，直接修改文件内容）。.bak（-i的扩展，修改文件前保存副本后缀.bak）。
#'...'sed的正则表达式。s/模式/替换文本/。^行的开头（Prompt=开头的行），


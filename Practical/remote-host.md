# Remote Host UMinho

## access 
`ssh vad@193.136.9.210  -p 22321`

**gestor vmware : 193.136.9.244**

**ssh** : <https://www.hanselman.com/blog/how-to-set-up-a-tab-profile-in-windows-terminal-to-automatically-ssh-into-a-linux-box>

**connect VSCode :** <https://medium.com/@sujaypillai/connect-to-your-remote-servers-from-visual-studio-code-eb5a5875e348#:~:text=Connect%20to%20a%20remote%20host,file%20>(%2FUsers%2Fsujay%2F.

**Remote host connection from w8** : mstsc.exe

## Monitoring 

**check activity on the host** :
> /var/log/auth.log | grep sshd
> .bash\_history

**Initramfs boot issue :**
<https://linuxhint.com/fix-initramfs-ubuntu/#:~:text=The%20%E2%80%9Cinitramfs%E2%80%9D%20error%20can%20affright,%E2%80%9D%20or%20%E2%80%9Cfsck%E2%80%9D%20utility>.

## Configuration 

reboot **periodically** : <https://askubuntu.com/questions/13730/how-can-i-schedule-a-nightly-reboot>

**Antivirus and rootkits**  ClamAV <https://upcloud.com/community/tutorials/scan-ubuntu-server-malware/>

`sudo freshclam && sudo clamscan –log=”/home/vad/ClamAv.log” --infected --recursive --exclude-dir="^/sys" --exclude-dir=”^/home/vad/ns-3-dev”  --exclude-dir=“^/home/vad/linux” /
`
multiple dir to exclude <https://serverfault.com/questions/957666/how-to-make-clamdscan-exclude-folders-and-only-log-infected>
--exclude-dir="/tmp/testClam/test/|/tmp/testClam/test1/"


# Command reference

Some commands I always need but forget.


```
# check running containers
docker ps

# grab a shell to a container
docker exec -it mgmt-framework sh




# relevant log location
sudo tail -f /var/log/syslog 
more /var/log/syslog | egrep -i ERR
/var/log/telemetry.log
/var/log/auth.log.1
/var/log/swss/

# check system uptime in seconds:
cat /proc/uptime | cut -d' ' -f1

# check the last reboot:
last reboot


# who and how am I logged in:
show users
who
echo $SSH_CONNECTION
# most detailed information:
sudo netstat -tnp | grep sshd


# show CPU bound network packets:
sudo tcpdump -i PortChannel2 tcp

# show CPU and memory stats:
top

# show memory utilization:
vmstat
```


## working with systemd:

```
# checking the status of a service managed by systemd:
sudo systemctl status lldp.service

# restart service
sudo systemctl restart ntp

# View all SONiC-related services
systemctl list-unit-files | grep -E "swss|syncd|bgp|database|mgmt"

# Common services:
- database.service
- swss.service
- syncd.service
- bgp.service
- teamd.service
- lldp.service
- pmon.service
- snmp.service
- mgmt-framework.service


# Find specific service file
systemctl cat database.service

# Show service status
systemctl status mgmt-framework.service

# Reload systemd after changes
systemctl daemon-reload


```
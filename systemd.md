In SONiC OS, systemd is used to manage the boot sequence of, amongst others, containers.

There is a cpp systemd generator and there are a variety of jinja templates to define the systemd configuration.



Service configuration flow:
1. Template Processing: During image build, Jinja2 templates are rendered with platform-specific variables
2. Service Generation: The build system creates .service files and container control scripts
3. Runtime Configuration: systemd-sonic-generator runs at boot to:
- Read the list of services from generated_services.conf
- Create appropriate service instances based on platform configuration
- Install services into systemd targets (e.g., sonic.target, multi-user.target)


You can find the templates in `sonic-net_sonic-buildimage/files/build_templates`. Example is the `sonic-net_sonic-buildimage/files/build_templates/lldp.service.j2` template.



On live devices, you can find the rendered systemd files in `/lib/systemd/s`. Here is an example of a systemd file for the mgmt-framework.service:
```
nw-read@device:~$ sudo cat /lib/systemd/system/mgmt-framework.service
[Unit]
Description=Management Framework container
Requires=database.service
After=database.service swss.service syncd.service
BindsTo=sonic.target
After=sonic.target
Before=ntp-config.service

[Service]
User=admin
ExecStartPre=/usr/local/bin/mgmt-framework.sh start
ExecStart=/usr/local/bin/mgmt-framework.sh wait
ExecStop=/usr/local/bin/mgmt-framework.sh stop
```

There are three 'levels' of disabling a systemd service:

1. `stop`: stops the service right now, does not affect boot behavior
2. `disable`: deletes its unit symlink. As a result, the service won’t be loaded automatically on reboot. A disabled service can be started with another service that needs it. 
3. `masked`: its unit file is symlinked to /dev/null making it 'impossible' to load the service, even if another enabled service requires it.


Useful systemd commands:
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
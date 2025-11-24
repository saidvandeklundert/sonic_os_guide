## Monit in SONiC


Monit is a utility for monitoring services on a Unix system. It is used by SONiC to periodically check a variety of datapoints like (container-) memory usage, log-file accessibility, disk-space and more.




Example implementation of a check:

- A script that checks is build into the user binaries of the system:

`/usr/local/bin/vnet_route_check.py`

This example script is a tool that verifies VNET routes consistancy between SONiC and vendor SDK DBs.

Returns `0` if there is no inconsistancy and `-1` if there is inconsistancy found.

- Monit configuration file was updated to periodically invoke a script:

```
# route_check.py Verify routes between APPL-DB & ASIC-DB are in sync.
# For any discrepancy, details are logged and a non-zero code is returned
# which would trigger a monit alert.
# Hence for any discrepancy, there will be log messages for "ERR" level
# from both route_check.py & monit.
#
check program routeCheck with path "/usr/local/bin/route_check.py"
    every 5 cycles
    if status != 0 for 3 cycle then alert repeat every 1 cycles
```

Checking Monit:

```
monit status                # check monit status
cat /etc/monit/monitrc      # check monit config file (default config file location)


show system-health detail   # check a variety of health indicators, including the Monit indicators


journalctl -u monit         # check the Monit event queue

tail -f /var/log/syslog     # check the syslog file, which monit is configured to log to
```
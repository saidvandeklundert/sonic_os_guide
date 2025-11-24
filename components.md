

`database container`: hosts the Redis database. For more details about Redis in SONiC, see [Redis in SONiC](redis_in_sonic.md) for more details.

`swss`: the SWitch State Service provides a database interface that allows for state representation of network applications and switch hardware. The service is run in the `swss` container.

`sonic-net_sonic-buildimage/rules/config`: has configuration parameters for all sorts of items. For instance, what containers to include in the build process (gnmi, mgmt-framework, etc), what the default password is and whether or not to include it, setting docker registry and much more.

`sonic-config-engine`: Utilities for generating SONiC configuration files
`sonic-frr-mgmt-framework`: Utility to dynamically configuration FRR based on config DB data update event

`sonic-bgpcfgd` : Utility to dynamically generate BGP configuration for FRR

`sonic-mgmt`: testing framework for SONiC. Contains code to setup the testbed and it contains the tests.


`teamd`: responsible for LAG. The `teammgrd` process maintains the LAG and `teamsyncd` handles communication with other systems.
`bgp container`: runs the FRR routing-stack and takes care of _all_ routing (BGP, static routing, etc). The `sonic-swss/fpmsyncd/fpmsyncd.cpp` ensures the FIB is pushed to `APPL_DB` and kept in sync.

## monitoring related SONiC components

`monit`: utility for monitoring services on a Unix system. Documented in the [Monit section](#monit).
`systemd`: system and service manager for Linux. In SONiC OS, systemd is used to manage the boot sequence of, amongst others, containers. , see [systemd](systemd.md) for more details.
`supervisord`: a process control system.
`pmon`: platform monitor, Docker container maintained as part of SONiC. Primarily monitoring and logging sensor readings from hardware using custom scripts.

## FRR management framework

`sonic-config-engine`: Utilities for generating SONiC configuration files

Package contains the file `sonic-net_sonic-buildimage/src/sonic-frr-mgmt-framework/frrcfgd/frrcfgd.py`. This has several Daemons, like BGPDaemon, that run and syn the configDB with FRR continously.



## sonic-bgpcfgd

Contains `sonic-net_sonic-buildimage/src/sonic-bgpcfgd/bgpmon/bgpmon.py` to populate STATE_DB.

It also maintains the state for the static route. For instance, it contains `sonic-net_sonic-buildimage/src/sonic-bgpcfgd/bgpcfgd/managers_static_rt.py` which updates the state of the static routes constantly.




## Monit


Monit is a utility for monitoring services on a Unix system. It is used by SONiC to periodically check a variety of datapoints like (container-) memory usage, log-file accessibility, disk-space and more.




Example implementation of a check:

- A script that checks is build into the user binaries of the system:

`/usr/local/bin/vnet_route_check.py`

This example script is a tool that verifies VNET routes consistancy between SONiC and vendor SDK DBs.

Returns `0` if there is no inconsistancy and `-1` if there is incosistancy found.

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






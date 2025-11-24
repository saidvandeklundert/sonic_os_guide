

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

- `monit`: utility for monitoring services on a Unix system. Documented in the [Monit section](monit.md).
- `systemd`: system and service manager for Linux. In SONiC OS, systemd is used to manage the boot sequence of, amongst others, containers. , see [systemd](systemd.md) for more details.
- `supervisord`: a process control system.
- `pmon`: platform monitor, Docker container maintained as part of SONiC. Primarily monitoring and logging sensor readings from hardware using custom scripts.

## FRR management framework

`sonic-config-engine`: Utilities for generating SONiC configuration files

Package contains the file `sonic-net_sonic-buildimage/src/sonic-frr-mgmt-framework/frrcfgd/frrcfgd.py`. This has several Daemons, like BGPDaemon, that run and syn the configDB with FRR continously.



## sonic-bgpcfgd

Contains `sonic-net_sonic-buildimage/src/sonic-bgpcfgd/bgpmon/bgpmon.py` to populate STATE_DB.

It also maintains the state for the static route. For instance, it contains `sonic-net_sonic-buildimage/src/sonic-bgpcfgd/bgpcfgd/managers_static_rt.py` which updates the state of the static routes constantly.

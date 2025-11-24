# FRR in SONiC

SONiC relies on FRR to provide a routing stack. 


The `sonic-bgpcfgd` package enables the sync of BGP and static route configuration between SONiC and the container running FRR.


`sonic-frr` is where the FRR-snapshot that is used in SONiC is kept. This way, a version of FRR is kept inside the SONiC organization inside git.


`sonic-frr-mgmt-framework`: utility to dynamically configuration FRR based on config DB data update event. Has the entry point in `sonic-frr-mgmt-framework/frrcfgd/frrcfgd.py` and uses templates to build FRR configurations that need to be applied to FRR. Top level templates can be found in `sonic-frr-mgmt-framework/templates/frr/frr.conf.j2`.


To populate state information to stateDB, the `sonic-bgpcfgd/bgpmon/bgpmon.py` code is used.


[router-id HLD](https://github.com/sonic-net/SONiC/blob/master/doc/BGP/BGP-router-id.md)
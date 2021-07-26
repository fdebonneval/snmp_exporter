# Selecting the usefull MIBs

# snmp_exporter
## Start snmp_exporter in a container
Run the container
```
 docker run --rm -it -p 9116:9116 -v $(pwd):/data  ubuntu:20.04 bash -c 'apt update && apt install -y wget && wget https://github.com/prometheus/snmp_exporter/releases/download/v0.20.0/snmp_exporter-0.20.0.linux-amd64.tar.gz && tar zxvf snmp_exporter-0.20.0.linux-amd64.tar.gz && bash'
```

Lauch the app
```
# /snmp_exporter-0.20.0.linux-amd64/snmp_exporter --config.file=/data/snmp.yml
```


## PYSMI

Run a Python container
```
docker run --rm -v /usr/share/snmp/mibs/:/mibs -it python:3 bash
```

```
$ pip3 install pysmi
```

Use the app to generate json version of the needed view
```
$ mibdump.py --mib-source=file:///mibs/ --generate-mib-texts  --destination-format json BACHMANN-BLUENET2-MIB
``` 

# use snmp_exporter "generator"

## Build generator Docker image
```
docker build -t snmp_exporter_generator https://raw.githubusercontent.com/prometheus/snmp_exporter/main/generator/Dockerfile
```

## Use generator image
```
docker run --rm -it -v $(pwd):/data -e MIBDIRS=/data/mibs --workdir=/data snmp_exporter_generator generate
```

## Get inside to troubleshoot
```
docker run --rm -it -v $(pwd):/data -e MIBDIRS=/data/mibs --workdir=/data --entrypoint=bash snmp_exporter_generator
```

# Bachmann

## blueNet2DeviceNumberOfPhases
```
  "blueNet2DeviceNumberOfPhases": {
    "name": "blueNet2DeviceNumberOfPhases",
    "oid": "1.3.6.1.4.1.31770.2.2.4.2.1.20",
    "nodetype": "column",
    "class": "objecttype",
    "syntax": {
      "type": "Unsigned32",
      "class": "type"
    },
    "maxaccess": "read-only",
    "status": "current",
    "description": "Number of phases available in this device. This variable will give the number of rows of the phases table blueNet2PhaseTable belonging to this device."
  },
```
Test of the "blueNet2DeviceNumberOfPhases" OID
```
$ snmpwalk -M mibs/ -v2c -c ubone42 -O f pdu0020.dyn.c-t.mx 1.3.6.1.4.1.31770.2.2.4.2.1.20
.iso.org.dod.internet.private.enterprises.31770.2.2.4.2.1.20.1 = Gauge32: 3
$ snmpwalk -M mibs/ -v2c -c ubone42 -O f pdu0016.dyn.c-t.mx 1.3.6.1.4.1.31770.2.2.4.2.1.20
.iso.org.dod.internet.private.enterprises.31770.2.2.4.2.1.20.1 = Gauge32: 1
```

## blueNet2DeviceNumberOfFuses
```
"blueNet2DeviceNumberOfFuses": {
    "name": "blueNet2DeviceNumberOfFuses",
    "oid": "1.3.6.1.4.1.31770.2.2.4.2.1.21",
    "nodetype": "column",
    "class": "objecttype",
    "syntax": {
      "type": "Unsigned32",
      "class": "type"
    },
    "maxaccess": "read-only",
    "status": "current",
    "description": "Number of fuses available in this device. This variable will give the number of rows of the fuse table blueNet2FuseTable belonging to this device."
  },
```
Test of the "blueNet2DeviceNumberOfFuses" OID
```
$ snmpwalk -M mibs/ -v2c -c ubone42 -O f pdu0016.dyn.c-t.mx 1.3.6.1.4.1.31770.2.2.4.2.1.21
.iso.org.dod.internet.private.enterprises.31770.2.2.4.2.1.21.1 = Gauge32: 2
$ snmpwalk -M mibs/ -v2c -c ubone42 -O f pdu0020.dyn.c-t.mx 1.3.6.1.4.1.31770.2.2.4.2.1.21
.iso.org.dod.internet.private.enterprises.31770.2.2.4.2.1.21.1 = Gauge32: 6
```

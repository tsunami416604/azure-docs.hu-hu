---
title: Oracle Data Guard valósítja meg az Azure Linux virtuális gép |} Microsoft Docs
description: Gyorsan karban lehessen Oracle Data Guard mentése és az Azure környezetben futna.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: rclaus
ms.openlocfilehash: f77a34fe4157e6c7ec763701e59db3330a1003c0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657937"
---
# <a name="implement-oracle-data-guard-on-an-azure-linux-virtual-machine"></a>Oracle Data Guard valósítja meg az Azure Linux virtuális gép 

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a cikk ismerteti az Azure CLI használata az Oracle-adatbázishoz 12c adatbázis az Azure piactér lemezképről való telepítéséhez. Ez a cikk megjeleníti a, lépésről lépésre, hogyan kell telepíteni és konfigurálni a Data Guard egy Azure virtuális gépen (VM).

Mielőtt elkezdené, győződjön meg arról, hogy telepítve van-e az Azure parancssori felület. További információkért lásd: a [Azure parancssori felület telepítési útmutató](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>A környezet előkészítése
### <a name="assumptions"></a>Előfeltételek

Oracle Data Guard telepítéséhez szüksége az azonos rendelkezésre állási készlet két Azure virtuális gépek létrehozásához:

- Az elsődleges virtuális gép (myVM1) rendelkezik egy futó Oracle-példány.
- A készenléti állapotban lévő virtuális gép (myVM2) van az Oracle szoftvereket csak.

A Piactéri lemezképhez, amely a virtuális gépek létrehozására használhatja az Oracle: Oracle-adatbázis-Ee:12.1.0.2:latest.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure-előfizetéshez használatával a [az bejelentkezési](/cli/azure/reference-index#az_login) parancsot, és kövesse a képernyőn megjelenő utasításokat.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot használatával a [az csoport létrehozása](/cli/azure/group#az_group_create) parancsot. Egy Azure erőforráscsoport egy olyan logikai tároló, amelyre erőforrások telepítése és kezelése. 

Az alábbi példa létrehoz egy erőforráscsoportot `myResourceGroup` a a `westus` helye:

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Rendelkezésre állási csoport létrehozása nem kötelező, de ajánlott. További információkért lásd: [Azure rendelkezésre állási készletek irányelvek](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális Gépet a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot. 

Az alábbi példakód létrehozza nevű két virtuális gép `myVM1` és `myVM2`. SSH-kulcsok, azt is hoz létre, ha még nem léteznek a kulcs alapértelmezett helye. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

Hozzon létre myVM1 (elsődleges):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Miután létrehozta a virtuális Gépet, az Azure parancssori felület információit mutatja meg az alábbi példához hasonló. Vegye figyelembe a értékének `publicIpAddress`. Ez a cím a virtuális gép elérésére használhat.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

Hozzon létre myVM2 (készenléti):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --admin-username azureuser \
     --generate-ssh-keys \
```

Vegye figyelembe a értékének `publicIpAddress` myVM2 létrehozása után.

### <a name="open-the-tcp-port-for-connectivity"></a>Nyissa meg a TCP-portot a kapcsolatot

Ez a lépés konfigurál külső végpontok száma, amelyek lehetővé teszik a távelérést az Oracle-adatbázishoz.

Nyissa meg a myVM1 portja:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Az eredmény a következő válasz hasonlóan kell kinéznie:

```bash
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

Nyissa meg a myVM2 portja:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Cserélje le az IP-cím a `publicIpAddress` értéket a virtuális géphez.

```bash 
$ ssh azureuser@<publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Létrehozza az adatbázist az myVM1 (elsődleges)

Az Oracle szoftver már telepítve van a Piactéri lemezképhez, így a következő lépés az adatbázis telepítéséhez. 

Az Oracle felügyelő váltani:

```bash
$ sudo su - oracle
```

Az adatbázis létrehozása:

```bash
$ dbca -silent \
   -createDatabase \
   -templateName General_Purpose.dbc \
   -gdbname cdb1 \
   -sid cdb1 \
   -responseFile NO_VALUE \
   -characterSet AL32UTF8 \
   -sysPassword OraPasswd1 \
   -systemPassword OraPasswd1 \
   -createAsContainerDatabase true \
   -numberOfPDBs 1 \
   -pdbName pdb1 \
   -pdbAdminPassword OraPasswd1 \
   -databaseType MULTIPURPOSE \
   -automaticMemoryManagement false \
   -storageType FS \
   -ignorePreReqs
```
Kimenetének létrehozása a következő válasz hasonlóan kell kinéznie:

```bash
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
```

A ORACLE_SID és ORACLE_HOME változók megadása:

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

Szükség esetén is hozzáadhat ORACLE_HOME és ORACLE_SID /home/oracle/.bashrc fájlt úgy, hogy ezek a beállítások lesznek mentve a későbbi bejelentkezések során:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="configure-data-guard"></a>Data Guard konfigurálása

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Archív napló mód a myVM1 (elsődleges) engedélyezése

```bash
$ sqlplus / as sysdba
SQL> SELECT log_mode FROM v$database;

LOG_MODE
------------
NOARCHIVELOG

SQL> SHUTDOWN IMMEDIATE;
SQL> STARTUP MOUNT;
SQL> ALTER DATABASE ARCHIVELOG;
SQL> ALTER DATABASE OPEN;
```
Kényszerített naplózását, és ellenőrizze, hogy legalább egy naplófájlban:

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

Készenléti ismét: naplók létrehozása:

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 50M;
```

Kapcsolja be a Flashback (így helyreállítási sokkal könnyebben) és készenléti\_fájl\_felügyeleti automatikus. Lépjen ki az SQL * Plus ezt követően.

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
SQL> EXIT;
```

### <a name="set-up-service-on-myvm1-primary"></a>Állítsa be a szolgáltatást a myVM1 (elsődleges)

Szerkessze, vagy hozzon létre a tnsnames.ora fájlt, amely a $ORACLE_HOME\network\admin mappában.

Adja hozzá az alábbi bejegyzéseket:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Szerkessze, vagy hozzon létre a listener.ora fájlt, amely a $ORACLE_HOME\network\admin mappában.

Adja hozzá az alábbi bejegyzéseket:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Data Guard Broker engedélyezése:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```
Indítsa el a figyelő:

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="set-up-service-on-myvm2-standby"></a>A myVM2 szolgáltatás beállítása (készenléti)

SSH-kapcsolatot myVM2:

```bash 
$ ssh azureuser@<publicIpAddress>
```

Jelentkezzen be Oracle:

```bash
$ sudo su - oracle
```

Szerkessze, vagy hozzon létre a tnsnames.ora fájlt, amely a $ORACLE_HOME\network\admin mappában.

Adja hozzá az alábbi bejegyzéseket:

```bash
cdb1 =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM1)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )

cdb1_stby =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SID = cdb1)
    )
  )
```

Szerkessze, vagy hozzon létre a listener.ora fájlt, amely a $ORACLE_HOME\network\admin mappában.

Adja hozzá az alábbi bejegyzéseket:

```bash
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = myVM2)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (GLOBAL_DBNAME = cdb1_DGMGRL)
      (ORACLE_HOME = /u01/app/oracle/product/12.1.0/dbhome_1)
      (SID_NAME = cdb1)
    )
  )

ADR_BASE_LISTENER = /u01/app/oracle
```

Indítsa el a figyelő:

```bash
$ lsnrctl stop
$ lsnrctl start
```


### <a name="restore-the-database-to-myvm2-standby"></a>Az adatbázis visszaállításához myVM2 (készenléti)

A paraméter fájl /tmp/initcdb1_stby.ora létrehozása a következő tartalommal:
```bash
*.db_name='cdb1'
```

Mappák létrehozása:

```bash
mkdir -p /u01/app/oracle/oradata/cdb1/pdbseed
mkdir -p /u01/app/oracle/oradata/cdb1/pdb1
mkdir -p /u01/app/oracle/fast_recovery_area/cdb1
mkdir -p /u01/app/oracle/admin/cdb1/adump
```

Hozzon létre egy jelszót fájlt:

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```
Indítsa el az adatbázist a myVM2:

```bash
$ export ORACLE_SID=cdb1
$ sqlplus / as sysdba

SQL> STARTUP NOMOUNT PFILE='/tmp/initcdb1_stby.ora';
SQL> EXIT;
```

Állítsa vissza az adatbázist a RMAN eszközzel:

```bash
$ rman TARGET sys/OraPasswd1@cdb1 AUXILIARY sys/OraPasswd1@cdb1_stby
```

A következő parancsokat a RMAN:
```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

A parancs befejezésekor meg kell jelennie a következőhöz hasonló üzenetek jelenhetnek meg. Kilépés RMAN.
```bash
media recovery complete, elapsed time: 00:00:00
Finished recover at 29-JUN-17
Finished Duplicate Db at 29-JUN-17

RMAN> EXIT;
```

Szükség esetén is hozzáadhat ORACLE_HOME és ORACLE_SID /home/oracle/.bashrc fájlt úgy, hogy ezek a beállítások lesznek mentve a későbbi bejelentkezések során:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

Data Guard Broker engedélyezése:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>Data Guard Broker myVM1 (elsődleges) konfigurálása

Indítsa el a Data Guard kezelőjét, és jelentkezzen be SYS és jelszó használatával. (Ne használja az operációs rendszer hitelesítési.) Hajtsa végre a következő:

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> CREATE CONFIGURATION my_dg_config AS PRIMARY DATABASE IS cdb1 CONNECT IDENTIFIER IS cdb1;
Configuration "my_dg_config" created with primary database "cdb1"
DGMGRL> ADD DATABASE cdb1_stby AS CONNECT IDENTIFIER IS cdb1_stby MAINTAINED AS PHYSICAL;
Database "cdb1_stby" added
DGMGRL> ENABLE CONFIGURATION;
Enabled.
```

Tekintse át a konfigurációt:
```bash
DGMGRL> SHOW CONFIGURATION;

Configuration - my_dg_config

  Protection Mode: MaxPerformance
  Members:
  cdb1      - Primary database
    cdb1_stby - Physical standby database

Fast-Start Failover: DISABLED

Configuration Status:
SUCCESS   (status updated 26 seconds ago)
```

Az Oracle Data Guard beállítása befejeződött. A következő szakasz bemutatja, hogyan a kapcsolat tesztelése és vált át.

### <a name="connect-the-database-from-the-client-machine"></a>Csatlakoztassa az adatbázist az ügyfélszámítógépen.

Frissítse vagy hozza létre a tnsnames.ora fájlt az ügyfélszámítógépen. A fájl általában $ORACLE_HOME\network\admin van.

Cserélje le az IP-címet a `publicIpAddress` myVM1 és myVM2 értékeit:

```bash
cdb1=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM1 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1)
    )
  )

cdb1_stby=
  (DESCRIPTION=
    (ADDRESS=
      (PROTOCOL=TCP)
      (HOST=<myVM2 IP address>)
      (PORT=1521)
    )
    (CONNECT_DATA=
      (SERVER=dedicated)
      (SERVICE_NAME=cdb1_stby)
    )
  )
```

Indítsa el az SQL * Plus:

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```
## <a name="test-the-data-guard-configuration"></a>A Data Guard konfigurációjának tesztelése

### <a name="switch-over-the-database-on-myvm1-primary"></a>Váltson át az adatbázist a myVM1 (elsődleges)

Átállás elsődleges készenléti (a cdb1_stby cdb1):

```bash
$ dgmgrl sys/OraPasswd1@cdb1
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1_stby;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1_stby"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1_stby" is opening...
Operation requires start up of instance "cdb1" on database "cdb1"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1_stby"
DGMGRL>
```

A készenléti adatbázis is csatlakozhat.

Indítsa el az SQL * Plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="switch-over-the-database-on-myvm2-standby"></a>Váltson át az adatbázist a myVM2 (készenléti)

Vált át, futtassa a következő myVM2:
```bash
$ dgmgrl sys/OraPasswd1@cdb1_stby
DGMGRL for Linux: Version 12.1.0.2.0 - 64bit Production

Copyright (c) 2000, 2013, Oracle. All rights reserved.

Welcome to DGMGRL, type "help" for information.
Connected as SYSDBA.
DGMGRL> SWITCHOVER TO cdb1;
Performing switchover NOW, please wait...
Operation requires a connection to instance "cdb1" on database "cdb1"
Connecting to instance "cdb1"...
Connected as SYSDBA.
New primary database "cdb1" is opening...
Operation requires start up of instance "cdb1" on database "cdb1_stby"
Starting instance "cdb1"...
ORACLE instance started.
Database mounted.
Switchover succeeded, new primary is "cdb1"
```

Ebben az esetben most kell kapcsolódnia kell az elsődleges adatbázis.

Indítsa el az SQL * Plus:

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

Befejezte a telepítésével és konfigurálásával Data Guard Oracle Linux rendszeren.


## <a name="delete-the-virtual-machine"></a>A virtuális gép törlése

Ha már nincs szüksége a virtuális Gépet, a következő paranccsal távolítsa el az erőforráscsoportot, virtuális gép és az összes kapcsolódó erőforrások:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Magas rendelkezésre állású virtuális gépek létrehozása](../../linux/create-cli-complete.md)

[Virtuális gép telepítése az Azure parancssori felület minták felfedezése](../../linux/cli-samples.md)

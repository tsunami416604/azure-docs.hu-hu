---
title: Az Oracle Data Guard megvalósítása Egy Azure Linux virtuális gépen | Microsoft dokumentumok
description: Gyorsan működésbe hozhatja az Oracle Data Guardot azure-környezetében.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: BorisB2015
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: borisb
ms.openlocfilehash: 96528dc34305e77602634110a0153f7623a15c96
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676767"
---
# <a name="implement-oracle-data-guard-on-an-azure-linux-virtual-machine"></a>Az Oracle Data Guard megvalósítása Egy Azure Linux-alapú virtuális gépen 

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez a cikk ismerteti, hogyan azure CLI egy Oracle Database 12c adatbázis üzembe helyezéséhez az Azure Marketplace-rendszerkép. Ez a cikk ezután bemutatja, lépésről lépésre, hogyan telepítheti és konfigurálhatja a Data Guard egy Azure virtuális gépen (VM).

Mielőtt elkezdené, győződjön meg arról, hogy az Azure CLI telepítve van. További információt az [Azure CLI telepítési útmutatójában talál.](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="prepare-the-environment"></a>A környezet előkészítése
### <a name="assumptions"></a>Feltételezések

Az Oracle Data Guard telepítéséhez két Azure-beli virtuális gépet kell létrehoznia ugyanazon a rendelkezésre állási csoporton:

- Az elsődleges virtuális gép (myVM1) egy futó Oracle-példánysal rendelkezik.
- A készenléti virtuális gép (myVM2) csak az Oracle szoftvert telepíti.

A virtuális gépek létrehozásához használt Marketplace-lemezkép az Oracle:Oracle-Database-Ee:12.1.0.2:latest.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure-előfizetésbe az [az bejelentkezési](/cli/azure/reference-index) paranccsal, és kövesse a képernyőn megjelenő utasításokat.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport egy logikai tároló, amelyben az Azure-erőforrások üzembe helyezése és kezelése. 

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myResourceGroup` helyen:

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Egy rendelkezésre állási csoport létrehozása nem kötelező, de azt javasoljuk, hogy. További információt az [Azure rendelkezésre állási készleteire vonatkozó irányelvek című témakörben talál.](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gép az [az vm create](/cli/azure/vm) paranccsal. 

A következő példa két `myVM1` nevű `myVM2`virtuális gépet hoz létre. SSH-kulcsokat is létrehoz, ha azok még nem léteznek az alapértelmezett kulcshelyen. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

MyVM1 létrehozása (elsődleges):
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

A virtuális gép létrehozása után az Azure CLI a következő példához hasonló információkat jelenít meg. Figyelje meg `publicIpAddress`az értékét. Ezzel a címmel érheti el a virtuális gép.

```output
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

MyVM2 létrehozása (készenlét):

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

Vegye figyelembe `publicIpAddress` a myVM2 létrehozása utáni értékét.

### <a name="open-the-tcp-port-for-connectivity"></a>A TCP-port megnyitása a kapcsolathoz

Ez a lépés külső végpontokat konfigurál, amelyek lehetővé teszik az Oracle-adatbázis távoli elérését.

Nyissa meg a myVM1 portját:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Az eredménynek a következő válaszhoz hasonlóan kell kinéznie:

```output
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

Nyissa meg a myVM2 portját:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVM2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Cserélje le az `publicIpAddress` IP-címet a virtuális gép értékére.

```bash 
$ ssh azureuser@<publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Az adatbázis létrehozása a myVM1-en (elsődleges)

Az Oracle szoftver már telepítve van a Marketplace-lemezképre, így a következő lépés az adatbázis telepítése. 

Váltás az Oracle felügyelőre:

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

A kimenetek nek a következő válaszhoz hasonlóan kell kinézniük:

```output
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

Állítsa be a ORACLE_SID és ORACLE_HOME változókat:

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
```

A /home/oracle/.bashrc fájlhoz hozzáadhat ORACLE_HOME és ORACLE_SID, így a rendszer a későbbi bejelentkezésekhez menti ezeket a beállításokat menti:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

## <a name="configure-data-guard"></a>Adatvédő konfigurálása

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>Archív napló mód engedélyezése a myVM1-en (elsődleges)

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

Engedélyezze a kényszerítésnaplózást, és győződjön meg arról, hogy legalább egy naplófájl található:

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
```

Készenléti ismétlési naplók létrehozása:

```bash
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo01.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo02.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo03.log') SIZE 50M;
SQL> ALTER DATABASE ADD STANDBY LOGFILE ('/u01/app/oracle/oradata/cdb1/standby_redo04.log') SIZE 50M;
```

Kapcsolja be a Flashback (ami a helyreállítás\_sokkal könnyebb), és állítsa standby FILE\_MANAGEMENT automatikus. Lépjen ki az SQL*Plus-ból.

```bash
SQL> ALTER DATABASE FLASHBACK ON;
SQL> ALTER SYSTEM SET STANDBY_FILE_MANAGEMENT=AUTO;
SQL> EXIT;
```

### <a name="set-up-service-on-myvm1-primary"></a>Szolgáltatás beállítása a myVM1 szolgáltatáson (elsődleges)

A $ORACLE_HOME\network\admin mappában található tnsnames.ora fájl szerkesztése vagy létrehozása.

Adja hozzá a következő bejegyzéseket:

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

A figyelő.ora fájl szerkesztése vagy létrehozása, amely a $ORACLE_HOME\network\admin mappában található.

Adja hozzá a következő bejegyzéseket:

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

Adatvédő bróker engedélyezése:

```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

Indítsa el a figyelőt:

```bash
$ lsnrctl stop
$ lsnrctl start
```

### <a name="set-up-service-on-myvm2-standby"></a>Szolgáltatás beállítása myVM2-n (készenléti állapot)

SSH a myVM2-re:

```bash 
$ ssh azureuser@<publicIpAddress>
```

Jelentkezzen be Oracle néven:

```bash
$ sudo su - oracle
```

A $ORACLE_HOME\network\admin mappában található tnsnames.ora fájl szerkesztése vagy létrehozása.

Adja hozzá a következő bejegyzéseket:

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

A figyelő.ora fájl szerkesztése vagy létrehozása, amely a $ORACLE_HOME\network\admin mappában található.

Adja hozzá a következő bejegyzéseket:

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

Indítsa el a figyelőt:

```bash
$ lsnrctl stop
$ lsnrctl start
```


### <a name="restore-the-database-to-myvm2-standby"></a>Az adatbázis visszaállítása myVM2 -re (készenlét)

Hozza létre a /tmp/initcdb1_stby paraméterfájlt a következő tartalommal:

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

Jelszófájl létrehozása:

```bash
$ orapwd file=/u01/app/oracle/product/12.1.0/dbhome_1/dbs/orapwcdb1 password=OraPasswd1 entries=10
```

Indítsa el az adatbázist a myVM2-n:

```bash
$ export ORACLE_SID=cdb1
$ sqlplus / as sysdba

SQL> STARTUP NOMOUNT PFILE='/tmp/initcdb1_stby.ora';
SQL> EXIT;
```

Az adatbázis visszaállítása az RMAN eszközzel:

```bash
$ rman TARGET sys/OraPasswd1@cdb1 AUXILIARY sys/OraPasswd1@cdb1_stby
```

Futtassa a következő parancsokat az RMAN-ben:

```bash
DUPLICATE TARGET DATABASE
  FOR STANDBY
  FROM ACTIVE DATABASE
  DORECOVER
  SPFILE
    SET db_unique_name='CDB1_STBY' COMMENT 'Is standby'
  NOFILENAMECHECK;
```

A parancs befejezésekor a következőhöz hasonló üzeneteknek kell megjelennie. Kifelé az RMAN-ből.

```output
media recovery complete, elapsed time: 00:00:00
Finished recover at 29-JUN-17
Finished Duplicate Db at 29-JUN-17
```

```bash
RMAN> EXIT;
```

A /home/oracle/.bashrc fájlhoz hozzáadhat ORACLE_HOME és ORACLE_SID, így a rendszer a későbbi bejelentkezésekhez menti ezeket a beállításokat menti:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
```

Adatvédő bróker engedélyezése:
```bash
$ sqlplus / as sysdba
SQL> ALTER SYSTEM SET dg_broker_start=true;
SQL> EXIT;
```

### <a name="configure-data-guard-broker-on-myvm1-primary"></a>Adatvédő-bróker konfigurálása myVM1-en (elsődleges)

Indítsa el a Data Guard Manager-t, és jelentkezzen be SYS és jelszó használatával. (Ne használjon operációsrendszer-hitelesítést.) Hajtsa végre a következőket:

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

Befejezte az Oracle Data Guard beállítását. A következő szakasz bemutatja, hogyan tesztelheti a kapcsolatot, és átválthat.

### <a name="connect-the-database-from-the-client-machine"></a>Az adatbázis csatlakoztatása az ügyfélgépről

Frissítse vagy hozza létre a tnsnames.ora fájlt az ügyfélszámítógépen. Ez a fájl általában a $ORACLE_HOME\network\admin mappában található.

Cserélje le az `publicIpAddress` IP-címeket a myVM1 és a myVM2 értékeire:

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

Sql*Plus indítása:

```bash
$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

## <a name="test-the-data-guard-configuration"></a>Az Adatvédő konfigurációjának tesztelése

### <a name="switch-over-the-database-on-myvm1-primary"></a>Az adatbázis váltása a myVM1-en (elsődleges)

Váltás elsődlegesről készenléti állapotra (cdb1 cdb1_stby):

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

Most már csatlakozhat a készenléti adatbázishoz.

Sql*Plus indítása:

```bash

$ sqlplus sys/OraPasswd1@cdb1_stby
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

### <a name="switch-over-the-database-on-myvm2-standby"></a>Az adatbázis átkapcsolása a myVM2-n (készenlét)

Az átkapcsoláshoz futtassa a következőket a myVM2-n:

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

Még egyszer, most már képesnek kell lennie arra, hogy csatlakozzon az elsődleges adatbázishoz.

Sql*Plus indítása:

```bash

$ sqlplus sys/OraPasswd1@cdb1
SQL*Plus: Release 12.2.0.1.0 Production on Wed May 10 14:18:31 2017

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
```

Befejezte a Data Guard telepítését és konfigurálását oracle Linuxon.


## <a name="delete-the-virtual-machine"></a>Törölje a következő virtuális gépet:

Ha már nincs szüksége a virtuális gépre, a következő paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gépés az összes kapcsolódó erőforrást:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Magas rendelkezésre állású virtuális gépek létrehozása](../../linux/create-cli-complete.md)

[Fedezze fel a virtuális gép üzembe helyezését az Azure CLI-mintákban](../../linux/cli-samples.md)

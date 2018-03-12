---
title: "Oracle Golden kapu valósítja meg az Azure Linux virtuális gép |} Microsoft Docs"
description: "Gyorsan karban lehessen az Oracle Golden mentése és az Azure-alapú környezetben futó."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: rclaus
ms.openlocfilehash: c99023d794dfb3b78b26ef721d89302e126f5cb1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Oracle Golden kapu valósítja meg az Azure Linux virtuális gép 

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató részletesen használata az Azure parancssori felület telepítése az Azure piactér gyűjtemény lemezképről való 12c Oracle-adatbázishoz. 

Ez a dokumentum lépésről lépésre bemutatja létrehozása, telepítése és konfigurálása az Oracle Golden kapu egy Azure virtuális gépen.

A kezdés előtt győződjön meg arról, hogy az Azure CLI telepítve van. További információért lásd az [Azure CLI telepítési útmutatóját](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>A környezet előkészítése

Az Oracle Golden kapu telepítés elvégzéséhez szüksége az azonos rendelkezésre állási készlet két Azure virtuális gépek létrehozásához. A Piactéri lemezképet hozhat létre a virtuális gépek **Oracle: Oracle-adatbázis-Ee:12.1.0.2:latest**.

Szükség Unix szerkesztő vi ismernie kell, és megismerheti a x11 (X Windows).

Az alábbiakban áttekintjük a környezet konfigurációjának összefoglalása:
> 
> |  | **Elsődleges hely** | **Hely replikálása** |
> | --- | --- | --- |
> | **Oracle-kiadás** |Oracle 12c kiadás 2 – (12.1.0.2) |Oracle 12c kiadás 2 – (12.1.0.2)|
> | **Számítógép neve** |myVM1 |myVM2 |
> | **Operációs rendszer** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **Oracle SID** |CDB1 |CDB1 |
> | **Replikációs séma** |TEST|TEST |
> | **Tulajdonos vagy replicate Golden kapu** |C##GGADMIN |REPUSER |
> | **Golden kapu folyamat** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be Azure előfizetés a [az bejelentkezési](/cli/azure/reference-index#az_login) parancsot. Ezután kövesse a képernyőn megjelenő utasításokat.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Egy Azure erőforráscsoport egy olyan logikai tároló mely Azure-erőforrások vannak telepítve és onnan, ami által kezelhető legyen. 

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myResourceGroup` helyen.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

A következő lépés az opcionális de javasolt. További információkért lásd: [Azure rendelkezésre állási készletek útmutató](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines).

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm#az_vm_create) paranccsal. 

Az alábbi példakód létrehozza nevű két virtuális gép `myVM1` és `myVM2`. SSH-kulcsok létrehozása, ha még nem léteznek a kulcs alapértelmezett helye. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

#### <a name="create-myvm1-primary"></a>Hozzon létre myVM1 (elsődleges):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

A virtuális gép létrehozása után az Azure parancssori felület információit mutatja meg az alábbi példához hasonló. (Vegye figyelembe a `publicIpAddress`. Ez a cím eléréséhez használt virtuális gép.)

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

#### <a name="create-myvm2-replicate"></a>Hozzon létre myVM2 (replikáció):
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Vegye figyelembe a `publicIpAddress` , valamint a létrehozása után.

### <a name="open-the-tcp-port-for-connectivity"></a>Nyissa meg a TCP-portot a kapcsolatot

A következő lépés, hogy konfigurálja a külső végpontok száma, amelyek lehetővé teszik, hogy távolról fér hozzá az Oracle-adatbázishoz. A külső végpontok száma konfigurálásához futtassa a következő parancsokat.

#### <a name="open-the-port-for-myvm1"></a>Nyissa meg a myVM1 portja:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Az eredményeket a következő válasz hasonlóan kell kinéznie:

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

#### <a name="open-the-port-for-myvm2"></a>Nyissa meg a myVM2 portja:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>Csatlakozás a virtuális géphez

Használja az alábbi parancsot egy SSH-munkamenet létrehozásához a virtuális géphez. Cserélje le az IP-címet a virtuális gépe `publicIpAddress` címére.

```bash 
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>Létrehozza az adatbázist az myVM1 (elsődleges)

Az Oracle szoftver már telepítve van a Piactéri lemezképhez, így a következő lépés az adatbázis telepítéséhez. 

Futtassa a szoftvert az "oracle" felügyelő:

```bash
sudo su - oracle
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
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

A ORACLE_SID és ORACLE_HOME változók megadása.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Szükség esetén adhat hozzá ORACLE_HOME és ORACLE_SID .bashrc fájlt úgy, hogy ezek a beállítások a későbbi bejelentkezések lesznek mentve:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Indítsa el az Oracle-figyelő
```bash
$ sudo su - oracle
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Az adatbázis létrehozása a következőn myVM2 (replikáció)

```bash
sudo su - oracle
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
A ORACLE_SID és ORACLE_HOME változók megadása.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Szükség esetén is hozzáadott ORACLE_HOME és ORACLE_SID .bashrc fájlt úgy, hogy ezek a beállítások lesznek mentve a későbbi bejelentkezések.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Indítsa el az Oracle-figyelő
```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Golden kapu beállítása 
Golden kapu konfigurálásához lépéseket az ebben a szakaszban.

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
Kényszerített naplózását, és ellenőrizze, hogy legalább egy naplófájlt.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Golden kapu szoftver letöltése
Töltse le, és az Oracle Golden kapu szoftver előkészítése, végezze el az alábbi lépéseket:

1. Töltse le a **fbo_ggs_Linux_x64_shiphome.zip** fájlt a [Oracle Golden kapu letöltési oldala](http://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html). A letöltési címben **Oracle GoldenGate 12.x.x.x az Oracle Linux x86-64**, kell lennie a .zip fájlokat letölteni.

2. Miután letöltötte a .zip fájlokat az ügyfélszámítógépre, használja a biztonságos másolási protokoll (SCP) a fájlok másolása a virtuális Gépet:

  ```bash
  $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
  ```

3. Helyezze át a .zip fájlokat a **/ opt** mappát. Az alábbiak szerint módosítsa a fájlok tulajdonosa:

  ```bash
  $ sudo su -
  # mv <folder>/*.zip /opt
  ```

4. Bontsa ki a fájlokat (telepítés a Linux csomagolja ki segédprogram Ha még nincs telepítve van):

  ```bash
  # yum install unzip
  # cd /opt
  # unzip fbo_ggs_Linux_x64_shiphome.zip
  ```

5. Engedély módosítása:

  ```bash
  # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
  ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Az ügyfél és a virtuális gép számára való futás x11 (csak Windows-ügyfelek) előkészítése
Ez az egy opcionális lépés. Ezt a lépést kihagyhatja, ha a Linux ügyfelet kell használnia, vagy már rendelkezik x11 beállítása.

1. Töltse le a PuTTY és Xming a Windows rendszerű számítógépen:

  * [Töltse le a PuTTY](http://www.putty.org/)
  * [Töltse le a Xming](https://xming.en.softonic.com/)

2.  Miután telepítette a PuTTY, a PuTTY mappában (például, C:\Program Files\PuTTY), futtassa a puttygen.exe (PuTTY kulcs generátor).

3.  A PuTTY Megosztottelérésikulcs-készítő:

  - A kulcs létrehozásához válassza ki a **Generate** gombra.
  - Másolja a kulcs tartalmát (**Ctrl + C**).
  - Válassza ki a **mentés titkos kulcs** gombra.
  - Figyelmen kívül hagyhatja a figyelmeztetést, amely akkor jelenik meg, majd válassza ki **OK**.

    ![A PuTTY megosztottelérésikulcs-készítő oldalát bemutató képernyőkép](./media/oracle-golden-gate/puttykeygen.png)

4.  A virtuális gépen, futtassa az alábbi parancsokat:

  ```bash
  # sudo su - oracle
  $ mkdir .ssh (if not already created)
  $ cd .ssh
  ```

5. Hozzon létre egy fájlt **authorized_keys**. A kulcs tartalmának beillesztése a fájlban, és mentse a fájlt.

  > [!NOTE]
  > A kulcs hossza csak a karakterlánc `ssh-rsa`. A kulcs tartalmát is, egy egyszerű szövegsor kell lennie.
  >  

6. Indítsa el a PuTTY alkalmazást. Az a **kategória** ablaktáblán válassza előbb **kapcsolat** > **SSH** > **Auth**. Az a **hitelesítéshez titkos kulcsfájl** mezőben tallózással keresse meg a korábban létrehozott kulcsot.

  ![A titkos kulcs beállítása oldalát bemutató képernyőkép](./media/oracle-golden-gate/setprivatekey.png)

7. Az a **kategória** ablaktáblán válassza előbb **kapcsolat** > **SSH** > **X11**. Válassza ki a **engedélyezése X11 továbbítási** mezőbe.

  ![Az engedélyezés X11 oldalát bemutató képernyőkép](./media/oracle-golden-gate/enablex11.png)

8. Az a **kategória** ablaktáblában lépjen **munkamenet**. Adja meg a gazdagép adatokat, majd válassza ki **nyitott**.

  ![A munkamenet oldalát bemutató képernyőkép](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Golden kapu szoftver telepítése

Oracle Golden kapu telepítéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a oracle felhasználóként. (Kell használva jelentkezhet be a jelszó megadása nélkül.) Győződjön meg arról, hogy fut-e Xming, a telepítés megkezdése előtt.
 
  ```bash
  $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
  $ ./runInstaller
  ```
2. Válassza ki az "Oracle adatbázis 12c Oracle GoldenGate". Válassza ki **tovább** a folytatáshoz.

  ![A telepítő telepítési válasszon oldalát bemutató képernyőkép](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Módosíthatja a szoftver helyét. Válassza ki a **Start Manager** mezőbe, majd adja meg az adatbázis helyén. A folytatáshoz kattintson a **Tovább** gombra.

  ![A telepítés válasszon oldalát bemutató képernyőkép](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Módosítsa a készlet könyvtárat, majd válassza ki **tovább** a folytatáshoz.

  ![A telepítés válasszon oldalát bemutató képernyőkép](./media/oracle-golden-gate/golden_gate_install_03.png)

5. Az a **összegzés** képernyőn válassza ki **telepítése** folytatja.

  ![A telepítő telepítési válasszon oldalát bemutató képernyőkép](./media/oracle-golden-gate/golden_gate_install_04.png)

6. "Legfelső szintű" parancsfájllal kérheti. Ha igen, nyisson meg egy külön munkamenet ssh a virtuális gépre, a sudo legfelső szintű, és futtassa a parancsfájl. Válassza ki **OK** továbbra is.

  ![A telepítés válasszon oldalát bemutató képernyőkép](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Ha a telepítés befejeződött, válassza ki a **Bezárás** a folyamat befejezéséhez.

  ![A telepítés válasszon oldalát bemutató képernyőkép](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Állítsa be a szolgáltatást a myVM1 (elsődleges)

1. Hozzon létre, vagy frissítse a tnsnames.ora fájlt:

  ```bash
  $ cd $ORACLE_HOME/network/admin
  $ vi tnsnames.ora

  cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

  pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
  ```

2. A Golden kapu tulajdonosi és felhasználói fiókokat hozhat létre.

  > [!NOTE]
  > A tulajdonos fióknak C ## előtaggal kell rendelkeznie.
  >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Hozza létre a Golden kapu tesztfelhasználói fiókja:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ sqlplus system/OraPasswd1@pdb1
  SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
  SQL> GRANT connect, resource, dba TO test;
  SQL> ALTER USER test QUOTA 100M on USERS;
  SQL> connect test/test@pdb1
  SQL> @demo_ora_create
  SQL> @demo_ora_insert
  SQL> EXIT;
  ```

4. A kivonat paraméterfájl konfigurálása.

 Indítsa el az arany kapu parancssori felület (ggsci):

  ```bash
  $ sudo su - oracle
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
  Successfully logged into database  pdb1
  GGSCI>  ADD SCHEMATRANDATA pdb1.test
  2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
  2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

  GGSCI> EDIT PARAMS EXTORA
  ```
5. Adja hozzá a következőket a KIVONATOT paraméterfájl (vi parancsok segítségével). Nyomja meg az Esc billentyűt, ': wq! " sikerült menteni a fájlt. 

  ```bash
  EXTRACT EXTORA
  USERID C##GGADMIN, PASSWORD ggadmin
  RMTHOST 10.0.0.5, MGRPORT 7809
  RMTTRAIL ./dirdat/rt  
  DDL INCLUDE MAPPED
  DDLOPTIONS REPORT 
  LOGALLSUPCOLS
  UPDATERECORDFORMAT COMPACT
  TABLE pdb1.test.TCUSTMER;
  TABLE pdb1.test.TCUSTORD;
  ```
6. Regisztráció nyerje ki – integrált kivonat:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci

  GGSCI> dblogin userid C##GGADMIN, password ggadmin
  Successfully logged into database CDB$ROOT.

  GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

  2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

  GGSCI> exit
  ```
7. Kinyerési ellenőrzőpontok felállítása, és indítsa el a valós idejű kivonat:

  ```bash
  $ ./ggsci
  GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
  EXTRACT (Integrated) added.

  GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
  RMTTRAIL added.

  GGSCI>  START EXTRACT EXTORA

  Sending START request to MANAGER ...
  EXTRACT EXTORA starting

  GGSCI > info all

  Program     Status      Group       Lag at Chkpt  Time Since Chkpt

  MANAGER     RUNNING
  EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
  ```
Ebben a lépésben a kiindulási Állapotváltozás, később, a másik szakaszban használt keresése:

  ```bash
  $ sqlplus / as sysdba
  SQL> alter session set container = pdb1;
  SQL> SELECT current_scn from v$database;
  CURRENT_SCN
  -----------
      1857887
  SQL> EXIT;
  ```

  ```bash
  $ ./ggsci
  GGSCI> EDIT PARAMS INITEXT
  ```

  ```bash
  EXTRACT INITEXT
  USERID C##GGADMIN, PASSWORD ggadmin
  RMTHOST 10.0.0.5, MGRPORT 7809
  RMTTASK REPLICAT, GROUP INITREP
  TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
  ```

  ```bash
  GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
  ```

### <a name="set-up-service-on-myvm2-replicate"></a>A myVM2 szolgáltatás beállítása (replikáció)


1. Hozzon létre, vagy frissítse a tnsnames.ora fájlt:

  ```bash
  $ cd $ORACLE_HOME/network/admin
  $ vi tnsnames.ora

  cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

  pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
  ```

2. Hozzon létre egy párhuzamos fiókot:

  ```bash
  $ sqlplus / as sysdba
  SQL> alter session set container = pdb1;
  SQL> create user repuser identified by rep_pass container=current;
  SQL> grant dba to repuser;
  SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
  SQL> connect repuser/rep_pass@pdb1 
  SQL> EXIT;
  ```

3. Golden kapu teszt felhasználói fiók létrehozása:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ sqlplus system/OraPasswd1@pdb1
  SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
  SQL> GRANT connect, resource, dba TO test;
  SQL> ALTER USER test QUOTA 100M on USERS;
  SQL> connect test/test@pdb1
  SQL> @demo_ora_create
  SQL> EXIT;
  ```

4. A paraméterfájl REPLICAT replikálni a módosításokat: 

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS REPORA  
  ```
  REPORA paraméter fájl tartalma:

  ```bash
  REPLICAT REPORA
  ASSUMETARGETDEFS
  DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
  DDL INCLUDE MAPPED
  DDLOPTIONS REPORT
  DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
  USERID repuser@pdb1, PASSWORD rep_pass
  MAP pdb1.test.*, TARGET pdb1.test.*;
  ```

5. Replicat ellenőrzőpont beállítása:

  ```bash
  GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
  GGSCI> EDIT PARAMS INITREP

  ```

  ```bash
  REPLICAT INITREP
  ASSUMETARGETDEFS
  DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
  USERID repuser@pdb1, PASSWORD rep_pass
  MAP pdb1.test.*, TARGET pdb1.test.*;   
  ```

  ```bash
  GGSCI> ADD REPLICAT INITREP, SPECIALRUN
  ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>A replikáció (myVM1 és myVM2) beállítása

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. A replikáció myVM2 beállítása (replikáció)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```
Frissítse a fájlt a következő:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```
Indítsa újra a kezelő szolgáltatás:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. Állítsa be a replikáció myVM1 (elsődleges)

Indítsa el a kezdeti betöltés és a hibák:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```
#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. A replikáció myVM2 beállítása (replikáció)

A módosítás előtt beszerzett számot száma az Állapotváltozás:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```
A replikáció már megkezdődött, és új rekordok teszt táblák beszúrásával tesztelheti.


### <a name="view-job-status-and-troubleshooting"></a>Feladat állapotának megtekintése és hibaelhárítása

#### <a name="view-reports"></a>Jelentések megtekintése
MyVM1 jelentések megtekintéséhez futtassa a következő parancsokat:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
MyVM2 jelentések megtekintéséhez futtassa a következő parancsokat:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Nézet állapota és előzményei
A myVM1 állapota és előzményei megtekintéséhez futtassa a következő parancsokat:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

A myVM2 állapota és előzményei megtekintéséhez futtassa a következő parancsokat:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Ezzel befejezte a telepítési és konfigurációs Golden kapu Oracle Linux.


## <a name="delete-the-virtual-machine"></a>A virtuális gép törlése

Ha már nincs szükség, az alábbi parancs segítségével távolítsa el az erőforráscsoportot, virtuális gép és az összes kapcsolódó erőforrások.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

[Magas rendelkezésre állású virtuális gép létrehozása oktatóanyag](../../linux/create-cli-complete.md)

[A virtuális gépek parancssori felületen való üzembe helyezését ismertető minták megismerése](../../linux/cli-samples.md)

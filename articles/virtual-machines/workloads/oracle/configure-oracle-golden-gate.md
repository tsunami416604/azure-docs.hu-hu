---
title: Az Oracle Golden Gate megvalósítása Azure Linux virtuális gépen | Microsoft dokumentumok
description: Gyorsan működésbe hozhatja az Oracle Golden Gate-et az Azure-környezetben.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 0706b7d3c238c154d3694b5760266299a7d788ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536870"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Az Oracle Golden Gate megvalósítása Egy Azure Linux-alapú virtuális gépen 

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató bemutatja, hogyan használhatja az Azure CLI-t egy Oracle 12c-adatbázis üzembe helyezéséhez az Azure Marketplace-katalógus lemezképből. 

Ez a dokumentum bemutatja, hogyan hozhat létre, telepíthet és konfigurálhat Oracle Golden Gate-et egy Azure-beli virtuális gépen. Ebben az oktatóanyagban két virtuális gép van beállítva egy rendelkezésre állási készlet ben egy régióban. Ugyanaz az oktatóanyag használható az OracleGolden Gate beállításához különböző rendelkezésre állási zónákban egyetlen Azure-régióban, vagy két különböző régióban beállított virtuális gépekhez.

A kezdés előtt győződjön meg arról, hogy az Azure CLI telepítve van. További információért lásd az [Azure CLI telepítési útmutatóját](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>A környezet előkészítése

Az Oracle Golden Gate telepítésének végrehajtásához két Azure-beli virtuális gépet kell létrehoznia ugyanazon a rendelkezésre állási csoporton. A virtuális gépek létrehozásához használt **Marketplace-lemezkép az Oracle:Oracle-Database-Ee:12.1.0.2:latest**.

Azt is meg kell ismernie a Unix szerkesztő vi és egy alapvető ismerete x11 (X Windows).

Az alábbiakban összefoglaljuk a környezet konfigurációját:
> 
> |  | **Elsődleges hely** | **Hely replikálása** |
> | --- | --- | --- |
> | **Oracle kiadás** |Oracle 12c Release 2 – (12.1.0.2) |Oracle 12c Release 2 – (12.1.0.2)|
> | **Gép neve** |myVM1 |myVM2 |
> | **Operációs rendszer** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **Oracle SID** |CDB1 |CDB1 |
> | **Replikációs séma** |TEST|TEST |
> | **Golden Gate tulajdonosa/replikálása** |C##GGADMIN |FELHASZNÁLÓ |
> | **Golden Gate folyamat** |EXTORA KÖZÖTT |REPORA Között|


### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure-előfizetésbe az [az bejelentkezési](/cli/azure/reference-index) paranccsal. Ezután kövesse a képernyőn megjelenő utasításokat.

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport egy logikai tároló, amelybe az Azure-erőforrások üzembe helyezése és azok kezelhetők. 

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `myResourceGroup` helyen.

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

A következő lépés nem kötelező, de ajánlott. További információt az [Azure rendelkezésre állási készletei ről szóló útmutatóban talál.](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. 

A következő példa két `myVM1` nevű `myVM2`virtuális gépet hoz létre. Hozzon létre SSH-kulcsokat, ha azok még nem léteznek az alapértelmezett kulcshelyen. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.

#### <a name="create-myvm1-primary"></a>MyVM1 létrehozása (elsődleges):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

A virtuális gép létrehozása után az Azure CLI a következő példához hasonló információkat jelenít meg. (Vegye figyelembe `publicIpAddress`a. Ez a cím a virtuális gép elérésére szolgál.)

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

#### <a name="create-myvm2-replicate"></a>MyVM2 létrehozása (ismétlés):

```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

Vegye figyelembe `publicIpAddress` a is, miután jött létre.

### <a name="open-the-tcp-port-for-connectivity"></a>A TCP-port megnyitása a kapcsolathoz

A következő lépés a külső végpontok konfigurálása, amelyek lehetővé teszik az Oracle adatbázis távoli elérését. A külső végpontok konfigurálásához futtassa a következő parancsokat.

#### <a name="open-the-port-for-myvm1"></a>Nyissa meg a myVM1 portját:

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

Az eredményeknek a következő válaszhoz hasonlóan kell kinézniük:

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

#### <a name="open-the-port-for-myvm2"></a>Nyissa meg a myVM2 portját:

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

### <a name="create-the-database-on-myvm1-primary"></a>Az adatbázis létrehozása a myVM1-en (elsődleges)

Az Oracle szoftver már telepítve van a Marketplace-lemezképre, így a következő lépés az adatbázis telepítése. 

Futtassa a szoftvert, mint az "oracle" superuser:

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
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

Állítsa be a ORACLE_SID és ORACLE_HOME változókat.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

A .bashrc fájlhoz hozzáadhat ORACLE_HOME és ORACLE_SID, így a későbbi bejelentkezésekhez a rendszer menti ezeket a beállításokat:

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Oracle-figyelő indítása

```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>Az adatbázis létrehozása a myVM2-n (replikáló)

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

Állítsa be a ORACLE_SID és ORACLE_HOME változókat.

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

Tetszés szerint ORACLE_HOME és ORACLE_SID is hozzáadhat a .bashrc fájlhoz, így ezek a beállítások mentésre kerülnek a későbbi bejelentkezésekhez.

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Oracle-figyelő indítása

```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Golden Gate konfigurálása 
A Golden Gate konfigurálásához kövesse el az ebben a szakaszban ismertetett lépéseket.

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
Engedélyezze a kényszerítésnaplózást, és győződjön meg arról, hogy legalább egy naplófájl található.

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Golden Gate szoftver letöltése
Az Oracle Golden Gate szoftver letöltéséhez és előkészítéséhez hajtsa végre az alábbi lépéseket:

1. Töltse le a **fbo_ggs_Linux_x64_shiphome.zip** fájlt az [Oracle Golden Gate letöltési oldaláról.](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html) A letöltési cím oracle **GoldenGate 12.x.x.x Oracle Linux x86-64**, ott kell lennie egy sor .zip fájlokat letölteni.

2. Miután letöltötte a .zip fájlokat az ügyfélszámítógépre, a Secure Copy Protocol (SCP) segítségével másolja a fájlokat a virtuális gépre:

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. Helyezze át a .zip fájlokat a **/opt** mappába. Ezután változtassa meg a fájlok tulajdonosát az alábbiak szerint:

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. Csomagolja ki a fájlokat (telepítse a Linux unzip segédprogramot, ha még nincs telepítve):

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. Engedély módosítása:

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>Az ügyfél és a virtuális gép előkészítése az x11 futtatására (csak Windows-ügyfelek esetén)
Ez egy nem kötelező lépés. Kihagyhatja ezt a lépést, ha Linux klienst használ, vagy már rendelkezik x11 beállítással.

1. Töltse le a PuTTY-t és az Xming-et Windows rendszerű számítógépére:

   * [Letöltés PuTTY](https://www.putty.org/)
   * [Xming letöltése](https://xming.en.softonic.com/)

2. A PuTTY telepítése után a PuTTY mappában (például C:\Program Files\PuTTY) futtassa a puttygen.exe fájlt (PuTTY kulcsgenerátor).

3. A PuTTY kulcs generátor:

   - Kulcs létrehozásához kattintson a **Létrehozás** gombra.
   - Másolja a billentyű tartalmába (**Ctrl+C**).
   - Válassza a **Személyes kulcs mentése** gombot.
   - Hagyja figyelmen kívül a megjelenő figyelmeztetést, majd kattintson **az OK gombra.**

   ![Képernyőkép a PuTTY kulcsgenerátor lapról](./media/oracle-golden-gate/puttykeygen.png)

4. A virtuális gépen futtassa ezeket a parancsokat:

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. **Hozzon**létre egy authorized_keys nevű fájlt. Illessze be a kulcs tartalmát a fájlba, majd mentse a fájlt.

   > [!NOTE]
   > A kulcsnak tartalmaznia kell a karakterláncot `ssh-rsa`. A kulcs tartalmának is egyetlen szövegsornak kell lennie.
   >  

6. Indítsa el a PuTTY alkalmazást. A **Kategória** ablaktáblán válassza a **Connection** > **SSH** > **Auth**lehetőséget. A **hitelesítéshez szükséges személyes kulcsfájlmezőben** keresse meg a korábban létrehozott kulcsot.

   ![Képernyőkép a Személyes kulcs beállítása lapról](./media/oracle-golden-gate/setprivatekey.png)

7. A **Kategória** ablaktáblán válassza a **Connection** > **SSH** > **X11**lehetőséget. Ezután jelölje be az **X11 továbbítás engedélyezése jelölőnégyzetet.**

   ![Képernyőkép az X11 engedélyezése lapról](./media/oracle-golden-gate/enablex11.png)

8. A **Kategória** ablaktáblán nyissa meg a **Munkamenet**lehetőséget. Adja meg az állomás adatait, majd válassza a **Megnyitás**gombot.

   ![Képernyőkép a munkamenet lapról](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>A Golden Gate szoftver telepítése

Az Oracle Golden Gate telepítéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be orákulumként. (A rendszer jelszó kérése nélkül tud bejelentkezni.) A telepítés megkezdése előtt győződjön meg arról, hogy az Xming fut.

   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```

2. Válassza az "Oracle GoldenGate for Oracle Database 12c" lehetőséget. Ezután a folytatáshoz válassza a **Tovább** gombot.

   ![Képernyőkép a telepítő telepítés kiválasztása lapjáról](./media/oracle-golden-gate/golden_gate_install_01.png)

3. Módosítsa a szoftver helyét. Ezután jelölje be a **Start Manager** jelölőnégyzetet, és adja meg az adatbázis helyét. A folytatáshoz kattintson a **Tovább** gombra.

   ![Képernyőkép a Telepítés kiválasztása lapról](./media/oracle-golden-gate/golden_gate_install_02.png)

4. Módosítsa a készletkönyvtárat, majd a Folytatáshoz válassza a **Tovább** gombot.

   ![Képernyőkép a Telepítés kiválasztása lapról](./media/oracle-golden-gate/golden_gate_install_03.png)

5. Az **Összegzés** képernyőn válassza a **Telepítés** gombot a folytatáshoz.

   ![Képernyőkép a telepítő telepítés kiválasztása lapjáról](./media/oracle-golden-gate/golden_gate_install_04.png)

6. Előfordulhat, hogy a rendszer kéri, hogy futtasson egy parancsfájlt "root" néven. Ha igen, nyisson meg egy külön munkamenetet, ssh a virtuális gépre, sudo a root, majd futtassa a parancsfájlt. Válassza **az OK** folytatás lehetőséget.

   ![Képernyőkép a Telepítés kiválasztása lapról](./media/oracle-golden-gate/golden_gate_install_05.png)

7. Amikor a telepítés befejeződött, a folyamat befejezéséhez válassza a **Bezárás** gombot.

   ![Képernyőkép a Telepítés kiválasztása lapról](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>Szolgáltatás beállítása a myVM1 szolgáltatáson (elsődleges)

1. A tnsnames.ora fájl létrehozása vagy frissítése:

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

2. Hozza létre a Golden Gate tulajdonosi és felhasználói fiókjait.

   > [!NOTE]
   > A tulajdonosi fióknak C## előtaggal kell rendelkeznie.
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

3. A Golden Gate teszt felhasználói fiókjának létrehozása:

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

4. Konfigurálja a kibontási paraméterfájlt.

   A Golden gate parancssori felület (ggsci) indítása:

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

5. Adja hozzá a következőket az EXTRACT paraméterfájlhoz (vi parancsokkal). Nyomja le az Esc billentyűt: ':wq! a fájl mentéséhez. 

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

6. Regiszterkivonat-integrált kivonat:

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```

7. Hozzon létre kivonat-ellenőrzőpontokat, és indítsa el a valós idejű kivonatot:

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

   Ebben a lépésben egy másik szakaszban találja a kezdő SCN-t, amelyet később fog használni:

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

### <a name="set-up-service-on-myvm2-replicate"></a>Szolgáltatás beállítása myVM2-en (replikálás)


1. A tnsnames.ora fájl létrehozása vagy frissítése:

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

2. Replikálási fiók létrehozása:

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Hozzon létre egy Golden Gate teszt felhasználói fiókot:

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

4. REPLIKÁLt paraméterfájl a módosítások replikálásához: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```

   A REPORA paraméterfájl tartalma:

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

5. Replikált ellenőrzőpont beállítása:

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

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>A replikáció beállítása (myVM1 és myVM2)

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1. A replikáció beállítása a myVM2-n (ismétlés)

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```

Frissítse a fájlt a következőkkel:

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```

Ezután indítsa újra a Kezelő szolgáltatást:

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2. A replikáció beállítása a myVM1-en (elsődleges)

Indítsa el a kezdeti terhelést, és ellenőrizze a hibákat:

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```

#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3. A replikáció beállítása a myVM2-n (ismétlés)

Módosítsa az SCN-számot a korábban kapott számmal:

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```

A replikáció megkezdődött, és tesztelheti, ha új rekordokat szúr be a TEST táblákba.


### <a name="view-job-status-and-troubleshooting"></a>Feladat állapotának és hibaelhárításának megtekintése

#### <a name="view-reports"></a>Jelentések megtekintése
A myVM1-en lévő jelentések megtekintéséhez futtassa a következő parancsokat:

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
A myVM2-en lévő jelentések megtekintéséhez futtassa a következő parancsokat:

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>Állapot és előzmények megtekintése
A myVM1 állapotának és előzményének megtekintéséhez futtassa a következő parancsokat:

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

A myVM2 állapotának és előzményének megtekintéséhez futtassa a következő parancsokat:

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
Ezzel befejeződik a Golden Gate telepítése és konfigurálása az Oracle linuxon.


## <a name="delete-the-virtual-machine"></a>Törölje a következő virtuális gépet:

Ha már nincs rá szükség, a következő parancs használható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás eltávolítására.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

[Magas rendelkezésre állású virtuális gép létrehozása oktatóanyag](../../linux/create-cli-complete.md)

[A virtuális gépek parancssori felületen való üzembe helyezését ismertető minták megismerése](../../linux/cli-samples.md)

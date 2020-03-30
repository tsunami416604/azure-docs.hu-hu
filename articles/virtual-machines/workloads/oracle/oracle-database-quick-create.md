---
title: Oracle-adatbázis létrehozása Azure-beli virtuális gépben | Microsoft dokumentumok
description: Gyorsan beszerezhet idálkodhat az Oracle Database 12c adatbázissal az Azure-környezetben.
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
ms.openlocfilehash: 9f4b9d53aaa1cac17fbaae4b638e144654fad4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535629"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Oracle-adatbázis létrehozása Azure-beli virtuális gépben

Ez az útmutató az Azure CLI használatával egy Azure virtuális gép üzembe helyezéséhez az [Oracle piactér imázsa lemezkép](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) egy Oracle 12c adatbázis létrehozása érdekében. A kiszolgáló telepítése után SSH-n keresztül csatlakozik az Oracle adatbázis konfigurálásához. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Virtuális gép (VM) létrehozásához használja az [az vm create](/cli/azure/vm) parancsot. 

Az alábbi példa egy `myVM` nevű virtuális gépet hoz létre. SSH-kulcsokat is létrehoz, ha azok még nem léteznek az alapértelmezett kulcshelyen. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása után az Azure CLI a következő példához hasonló információkat jelenít meg. Figyelje meg `publicIpAddress`a értékét. Ezzel a címmel érheti el a virtuális gép.

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

SSH-munkamenet létrehozásához a virtuális gép, használja a következő parancsot. Cserélje le az `publicIpAddress` IP-címet a virtuális gép értékére.

```bash
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>Az adatbázis létrehozása

Az Oracle szoftver már telepítve van a Marketplace-lemezképre. Hozzon létre egy mintaadatbázist az alábbiak szerint. 

1.  Váltson az *Oracle* rendszergazda, majd inicializálja a figyelő naplózás:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    A kimenet a következő példához hasonló:

    ```output
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  Az adatbázis létrehozása:

    ```bash
    dbca -silent \
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

    Az adatbázis létrehozása néhány percet vesz igénybe.

3. Oracle változók beállítása

A csatlakozás előtt két környezeti változót kell beállítania: *ORACLE_HOME* és *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```

A .bashrc fájlhoz ORACLE_HOME és ORACLE_SID változókat is hozzáadhat. Ezzel menti a környezeti változókat a jövőbeli bejelentkezések. Erősítse meg `~/.bashrc` a következő állításokat adtak hozzá a fájlhoz szerkesztő vel választott.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express kapcsolat

Az adatbázis felfedezéséhez használható grafikus felhasználói felület-kezelő eszköz beállítása az Oracle EM Express beállításával. Az Oracle EM Express szolgáltatáshoz való csatlakozáshoz először be kell állítania a portot az Oracle programban. 

1. Csatlakozás az adatbázishoz az sqlplus használatával:

    ```bash
    sqlplus / as sysdba
    ```

2. A csatlakozás után állítsa be az 5502-es portot az EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Nyissa meg a PDB1 tárolót, ha még nem nyitotta meg, de először ellenőrizze az állapotot:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    A kimenet a következő példához hasonló:

    ```output
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Ha a `PDB1` OPEN_MODE nem READ WRITE, majd futtassa a következő parancsokat a PDB1 megnyitásához:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Az sqlplus `quit` munkamenet befejezéséhez be `exit` kell írnia, és be kell írnia az oracle-felhasználó kijelentkezéséhez.

## <a name="automate-database-startup-and-shutdown"></a>Adatbázis indításának és leállításának automatizálása

Az Oracle-adatbázis alapértelmezés szerint nem indul el automatikusan a virtuális gép újraindításakor. Az Oracle-adatbázis automatikus indításának beállításához először jelentkezzen be gyökérként. Ezután hozzon létre és frissítsen néhány rendszerfájlt.

1. Bejelentkezés gyökérként

    ```bash
    sudo su -
    ```

2.  A kedvenc szerkesztőhasználatával szerkesztheti `/etc/oratab` a fájlt, `Y`és módosítsa az alapértelmezett értéket: `N`

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Hozzon létre `/etc/init.d/dbora` egy nevű fájlt, és illessze be a következő tartalmat:

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  Módosítsa a *chmod* fájlok engedélyeit az alábbiak szerint:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Szimbolikus hivatkozások létrehozása indításhoz és leállításhoz az alábbiak szerint:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  A módosítások teszteléséhez indítsa újra a virtuális gép:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Nyitott portok a kapcsolathoz

A végső feladat néhány külső végpont konfigurálása. Az Azure Network Security Group, amely védi a virtuális gépet, először lépjen ki az SSH-munkamenet a virtuális gép (ki kellett volna rúgni az SSH újraindításakor az előző lépésben). 

1.  Az Oracle-adatbázis távoli eléréséhez használt végpont megnyitásához hozzon létre egy hálózati biztonsági csoport szabályt az [az network nsg szabállyal](/cli/azure/network/nsg/rule) az alábbiak szerint: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Az Oracle EM Express távoli eléréséhez használt végpont megnyitásához hozzon létre egy hálózati biztonsági csoport szabályt [az az network nsg szabállyal](/cli/azure/network/nsg/rule) az alábbiak szerint:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Ha szükséges, szerezze be a virtuális gép nyilvános IP-címét az [az hálózati nyilvános ip show-val](/cli/azure/network/public-ip) az alábbiak szerint:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Csatlakoztassa az EM Express-t a böngészőjéről. Győződjön meg róla, hogy a böngésző kompatibilis az EM Express programmal (Flash telepítés szükséges): 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

Az **SYS-fiókkal** jelentkezhet be, és bejelölheti a **sysdba jelölőnégyzetet.** Használja a telepítés során beállított **OraPasswd1** jelszót. 

![Képernyőkép az Oracle OEM Express bejelentkezési lapjáról](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután befejezte az első Oracle-adatbázis feltárását az Azure-ban, és a virtuális gépre már nincs szükség, az [az csoport törlési](/cli/azure/group) parancsával eltávolíthatja az erőforráscsoportot, a virtuális gépés az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

További információ [az Azure-beli Oracle-megoldásokról.](oracle-considerations.md) 

Próbálja ki az [Oracle Automated Storage Management telepítése és konfigurálása](configure-oracle-asm.md) oktatóanyagot.

---
title: "Oracle-adatbázishoz egy Azure virtuális gép létrehozása |} Microsoft Docs"
description: "Gyorsan karban lehessen az Oracle-adatbázishoz 12c adatbázis mentése és az Azure környezetben futó."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/17/2017
ms.author: rclaus
ms.openlocfilehash: 8683b016c4db2c66fb1dd994405b70c3d137a7fc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Oracle-adatbázishoz egy Azure virtuális gép létrehozása

Ez az útmutató részletek központi telepítése az Azure virtuális gépként az Azure parancssori felület használatával a [Oracle piactér gyűjtemény kép](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) létrehozásához 12 c Oracle-adatbázishoz. Ha a kiszolgáló van telepítve, kapcsolódni fog SSH-kapcsolaton keresztül az Oracle-adatbázishoz való konfigurálásához. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet (VM), használja a [az virtuális gép létrehozása](/cli/azure/vm#create) parancsot. 

Az alábbi példa egy `myVM` nevű virtuális gépet hoz létre. SSH-kulcsok, azt is hoz létre, ha még nem léteznek a kulcs alapértelmezett helye. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

A virtuális gép létrehozása után a Azure CLI-t az alábbi példához hasonló információkat jeleníti meg. Vegye figyelembe a következő `publicIpAddress`. Ez a cím a virtuális gép elérésére használhat.

```azurecli
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

A virtuális gép SSH-munkamenetet létrehozni, használja a következő parancsot. Cserélje le az IP-cím a `publicIpAddress` értéket a virtuális gép számára.

```bash 
ssh <publicIpAddress>
```

## <a name="create-the-database"></a>Az adatbázis létrehozása

Az Oracle szoftver már telepítve van a Piactéri lemezképhez. Hozzon létre egy adatbázist az alábbiak szerint. 

1.  Váltás a *oracle* felügyelő, majd a naplózás a figyelő inicializálása:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    A kimenet a következő példához hasonló:

    ```bash
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

3. Oracle változók megadása

Csatlakozás előtt be kell állítani a két környezeti változók: *ORACLE_HOME* és *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
ORACLE_HOME és ORACLE_SID változók .bashrc fájl adhat hozzá. Ez szeretné menteni a környezeti változók a későbbi bejelentkezések. Győződjön meg arról, a következő utasításokat lettek hozzáadva a `~/.bashrc` a választott szerkesztővel fájlt.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express kapcsolat

A grafikus felhasználói felület segítségével megismerkedhet az adatbázist, és állítsa be az Oracle EM Express felügyeleti eszköz. Szeretne csatlakozni az Oracle EM Express, az Oracle port először meg kell adnia. 

1. Kapcsolódás saját adatbázishoz sqlplus használatával:

    ```bash
    sqlplus / as sysdba
    ```

2. A csatlakozás után állítsa be a port 5502 EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Nyissa meg a tároló PDB1, ha nem már megnyitott, de az első ellenőrizze az állapotát:

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    A kimenet a következő példához hasonló:

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. Ha a OPEN_MODE `PDB1` nincs OLVASÁSI ÍRNI, majd a PDB1 nyissa meg a következőket parancsok futtatásával:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Önnek kell beírnia `quit` sqlplus munkamenet és típus befejezéséhez `exit` oracle felhasználó kijelentkezik.

## <a name="automate-database-startup-and-shutdown"></a>Adatbázis indítási és leállítási automatizálásához

Alapértelmezés szerint az Oracle-adatbázishoz nem indul el automatikusan a virtuális gép újraindításakor. Az Oracle-adatbázishoz be automatikus indításra, először jelentkezzen be rendszergazdaként. Ezt követően létrehozása, és egyes rendszer fájlok frissítése.

1. Jelentkezzen a legfelső szintű
    ```bash
    sudo su -
    ```

2.  A kedvenc szerkesztővel szerkessze a fájlt `/etc/oratab` , és módosítsa az alapértelmezett `N` való `Y`:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Hozzon létre egy fájlt `/etc/init.d/dbora` , majd illessze be az alábbiakat:

    ```
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

4.  A fájlok engedélyeinek módosítása *chmod* az alábbiak szerint:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Indítási és leállítási szimbolikus hivatkozásokat hoz létre a a következőképpen:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  A módosítások ellenőrzéséhez indítsa újra a virtuális Gépet:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>A hálózati kapcsolatot megnyitott portok

Az utolsó feladat be nem konfigurálhatja az egyes külső végpontok száma. Az Azure hálózati biztonsági csoport, amely védi a virtuális gép beállításához lépjen ki az SSH-munkamenetet a virtuális gép (kell rendelkeznie lett kezdődött SSH kívül ha újraindítás az előző lépésben). 

1.  Nyissa meg a végpont, amelyekkel távolról fér hozzá az Oracle-adatbázishoz, hozzon létre egy hálózati biztonsági csoport szabály [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#create) az alábbiak szerint: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Nyissa meg a végpont Oracle EM Express távoli eléréséhez használt, hozzon létre egy hálózati biztonsági csoport szabály [az hálózati nsg-szabály létrehozása](/cli/azure/network/nsg/rule#create) az alábbiak szerint:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Ha szükséges, szerezze be a nyilvános IP-címet a virtuális gép újra [az hálózati nyilvános ip-megjelenítése](/cli/azure/network/public-ip#show) az alábbiak szerint:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Csatlakoztassa a böngésző EM Express. Ügyeljen arra, hogy a böngésző kompatibilis EM expressz (Flash esetén): 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

A bejelentkezhet a **SYS** fiókot, és ellenőrizze a **SYSDBA csoport szerint** jelölőnégyzetet. A jelszó használata **OraPasswd1** telepítése során beállított. 

![Az Oracle OEM Express bejelentkezési oldalát bemutató képernyőkép](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután befejezte az első Oracle adatbázis Azure felfedezése és a virtuális gép már nem szükséges, használhatja a [az csoport törlése](/cli/azure/group#delete) parancsot a távolítsa el az erőforráscsoportot, a virtuális gép, és minden kapcsolódó erőforrásokat.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Tudnivalók más [Oracle megoldások Azure](oracle-considerations.md). 

Próbálja meg a [telepítése és konfigurálása Oracle automatikus tárhely-kezelés](configure-oracle-asm.md) oktatóanyag.
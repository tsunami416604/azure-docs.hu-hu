---
title: Oracle-adatbázis létrehozása az Azure virtuális gép |} A Microsoft Docs
description: Gyorsan, az Oracle Database 12c adatbázis mentése és az Azure-környezetben futó.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 62f7c22bb2db933ff1e11c96aac8c02c8d19cd58
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492928"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Egy Azure virtuális gépen futó Oracle-adatbázis létrehozása

Ez az útmutató ismerteti az Azure CLI használatával az Azure virtuális gép üzembe helyezéséhez a [Oracle marketplace image z galerie](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview) 12 c Oracle-adatbázis létrehozásához. A kiszolgáló üzembe helyezését követően csatlakozik SSH-n keresztül annak érdekében, hogy az Oracle-adatbázis konfigurálása. 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet (VM), használja a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot. 

Az alábbi példa egy `myVM` nevű virtuális gépet hoz létre. SSH-kulcsokat, azt is hoz létre, ha azok még nem léteznek a kulcsok alapértelmezett helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

Miután létrehozta a virtuális Gépet, az Azure CLI az alábbi példához hasonló információkat jeleníti meg. Jegyezze fel az értékét a `publicIpAddress`. Ez a cím a virtuális gép eléréséhez használt.

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

A virtuális Gépet hoz létre az SSH-munkamenet, használja a következő parancsot. Cserélje le az IP-címet a `publicIpAddress` értéket a virtuális géphez.

```bash 
ssh <publicIpAddress>
```

## <a name="create-the-database"></a>Az adatbázis létrehozása

Az Oracle-szoftver már telepítve van a Piactéri lemezképet. Hozzon létre egy mintaadatbázist a következőképpen. 

1.  Váltson a *oracle* superuser, akkor a naplózáshoz a figyelő inicializálása:

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

2.  Az adatbázis létrehozásához:

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

3. Oracle-változók beállítása

Csatlakozás előtt be kell állítani a két környezeti változó: *ORACLE_HOME* és *ORACLE_SID*.

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
Változók ORACLE_HOME és ORACLE_SID a .bashrc fájlt adhat hozzá. Ez lenne mentése a későbbi bejelentkezések környezeti változókat. Erősítse meg a következő utasításokat lettek hozzáadva a `~/.bashrc` fájlt tetszőleges szerkesztővel.

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express-kapcsolatok

A grafikus felügyeleti eszköz, amely segítségével ismerje meg az adatbázis, Oracle-EM Express beállítása. Ha csatlakozni szeretne Oracle-EM Express, a port, az Oracle-ben először meg kell adnia. 

1. Csatlakozás az adatbázishoz sqlplus használatával:

    ```bash
    sqlplus / as sysdba
    ```

2. A csatlakozás után a port 5502 beállítása az EM Express

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. Nyissa meg a tároló PDB1 Ha még nem nyitott, de az első ellenőrzési állapota:

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

4. Ha az a OPEN_MODE `PDB1` nem OLVASHATÓ ÍRNI, majd futtassa a következőket parancsokat PDB1 megnyitásához:

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

Önnek kell beírnia `quit` körű a sqlplus munkamenet és a típus `exit` oracle felhasználó kijelentkeztetése.

## <a name="automate-database-startup-and-shutdown"></a>Adatbázis-indítási és leállítási automatizálása

Az Oracle-adatbázis alapértelmezés szerint automatikusan a virtuális gép újraindításakor nem indul el. Az Oracle-adatbázis automatikus indításra állítva, először jelentkezzen be rendszergazdaként. Ezután hozzon létre, és néhány rendszer fájlok frissítése.

1. Jelentkezzen be gyökér szintű
    ```bash
    sudo su -
    ```

2.  Használja kedvenc szerkesztőjében, szerkessze a fájlt `/etc/oratab` , és módosítsa az alapértelmezett `N` való `Y`:

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  Hozzon létre egy fájlt `/etc/init.d/dbora` , és illessze be az alábbiakat:

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

4.  A fájlok engedélyeinek módosítása *chmod* módon:

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  Az indítási és leállítási szimbolikus hivatkozások a következőképpen hozhat létre:

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  Tesztelheti a módosításokat, indítsa újra a virtuális Gépet:

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>Portok megnyitása a hálózati kapcsolatot

A végső feladat, hogy néhány külső végpontok konfigurálása. Az Azure hálózati biztonsági csoport, amely védelmet nyújt a virtuális gép beállításához, lépjen ki az SSH-munkamenet a virtuális gépen (kell rendelkezik lett problémaelhárító SSH ki, amikor a rendszer újraindítása az előző lépésben). 

1.  Nyissa meg a végpont, amelyekkel távolról az Oracle-adatbázis eléréséhez, hozzon létre egy hálózati biztonsági csoport szabályt az [az network nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) módon: 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  Nyissa meg a végpont, amely Oracle-EM Express távolról elérésére használja, hozzon létre egy hálózati biztonsági csoport szabályt az [az network nsg-szabály létrehozása](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) módon:

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. Szükség esetén kérje le újra a virtuális gép nyilvános IP-cím [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) módon:

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  Csatlakozás EM Express a böngészőben. Ellenőrizze, hogy a böngésző összeegyeztethető EM Express (Flash telepítése szükség): 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

Használatával bejelentkezhet a **SYS** fiókra, és ellenőrizze a **SYSDBA csoport mint** jelölőnégyzetet. A jelszó használata **OraPasswd1** állíthat be a telepítés során. 

![Az Oracle OEM Express bejelentkezési oldalát bemutató képernyőkép](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett az első Oracle-adatbázis felfedezése az Azure-ban és a virtuális gép már nem szükséges, használja a [az csoport törlése](/cli/azure/group#az_group_delete) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás.

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

További információk egyéb [az Azure-ban Oracle-megoldásokról](oracle-considerations.md). 

Próbálja ki a [telepítése és az Oracle automatikus Tároláskezelés konfigurálása](configure-oracle-asm.md) oktatóanyag.
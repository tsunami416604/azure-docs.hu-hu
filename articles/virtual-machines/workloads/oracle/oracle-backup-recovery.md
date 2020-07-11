---
title: Egy Oracle Database 12c-adatbázis biztonsági mentése és helyreállítása Azure Linux rendszerű virtuális gépen | Microsoft Docs
description: Megtudhatja, hogyan készíthet biztonsági másolatot egy Oracle Database 12c-adatbázisról az Azure-környezetben.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rgardler
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogardle
ms.openlocfilehash: 5a491bf85546c982db126ef4d6260a46ca8bf88b
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224451"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Oracle Database 12c-adatbázis biztonsági mentése és helyreállítása Azure-beli linuxos virtuális gépen

Az Azure CLI-vel Azure-erőforrásokat hozhat létre és kezelhet a parancssorban, illetve parancsfájlokat is használhat. Ebben a cikkben az Azure CLI-szkriptekkel telepítünk egy Oracle Database 12c-adatbázist egy Azure Marketplace Gallery-rendszerképből.

Mielőtt elkezdené, győződjön meg arról, hogy az Azure CLI telepítve van. További információ: az [Azure CLI telepítési útmutatója](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>A környezet előkészítése

### <a name="step-1-prerequisites"></a>1. lépés: előfeltételek

*   A biztonsági mentési és helyreállítási folyamat végrehajtásához először létre kell hoznia egy linuxos virtuális gépet, amelyen Oracle Database 12c telepített példánya van. A virtuális gép létrehozásához használt Piactéri rendszerkép neve *Oracle: Oracle-Database-EE: 12.1.0.2: Latest*.

    Az Oracle-adatbázisok létrehozásával kapcsolatos információkért tekintse meg az [Oracle Create Database](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create)rövid útmutatóját.


### <a name="step-2-connect-to-the-vm"></a>2. lépés: Kapcsolódás a virtuális géphez

*   Ha Secure Shell-(SSH-) munkamenetet szeretne létrehozni a virtuális géppel, használja a következő parancsot. Cserélje le az IP-címet és az állomásnév kombinációját a `publicIpAddress` virtuális gép értékére.

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>3. lépés: az adatbázis előkészítése

1.  Ez a lépés azt feltételezi, hogy rendelkezik egy *myVM*nevű virtuális gépen futó Oracle-példánnyal (cdb1).

    Futtassa az *Oracle* rendszeradminisztrátori gyökerét, majd inicializálja a figyelőt:

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
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

2.  Választható Győződjön meg arról, hogy az adatbázis archiválási napló módban van:

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
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

3.  Választható Hozzon létre egy táblázatot a véglegesítés teszteléséhez:

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```

4.  A biztonságimásolat-fájl helyének és méretének ellenőrzése vagy módosítása:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```

5. Az adatbázis biztonsági mentése az Oracle Recovery Manager (Oláh Anna) használatával:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>4. lépés: az alkalmazás-konzisztens biztonsági mentés Linux rendszerű virtuális gépekhez

Az alkalmazás-konzisztens biztonsági mentések a Azure Backup új szolgáltatása. Létrehozhatja és kiválaszthatja a virtuális gép pillanatképe előtt és után végrehajtandó parancsfájlokat (a pillanatkép előtti és utáni pillanatképet is).

1. Töltse le a JSON-fájlt.

    VMSnapshotScriptPluginConfig.jsletöltése innen: https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig . A fájl tartalma a következőhöz hasonlóan néz ki:

    ```output
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. Hozza létre a/etc/Azure mappát a virtuális gépen:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Másolja a JSON-fájlt.

    Másolja VMSnapshotScriptPluginConfig.jst a/etc/Azure mappába.

4. Szerkessze a JSON-fájlt.

    Szerkessze a fájl VMSnapshotScriptPluginConfig.jsét, hogy tartalmazza a `PreScriptLocation` és a `PostScriptlocation` paramétereket. Például:

    ```output
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. Hozza létre a pillanatkép előtti és utáni parancsfájlokat.

    Íme egy példa a pillanatkép előtti és a pillanatkép utáni parancsfájlokra a "hideg biztonsági mentéshez" (offline biztonsági mentés, leállítás és újraindítás után):

    Pre_script. sh/etc/Azure/esetén:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Post_script. sh/etc/Azure/esetén:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Íme egy példa a pillanatkép előtti és a pillanatkép utáni parancsfájlokra a "gyors biztonsági mentéshez" (online biztonsági mentés):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Post_script. sh/etc/Azure/esetén:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    A/etc/Azure/pre_script. SQL esetében módosítsa a fájl tartalmát a követelmények szerint:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    A/etc/Azure/post_script. SQL esetében módosítsa a fájl tartalmát a követelmények szerint:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Fájl engedélyeinek módosítása:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Tesztelje a parancsfájlokat.

    A parancsfájlok teszteléséhez először jelentkezzen be root-ként. Ezt követően győződjön meg arról, hogy nincsenek hibák:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

További információ: [alkalmazás-konzisztens biztonsági mentés Linux rendszerű virtuális gépekhez](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>5. lépés: az Azure Recovery Services-tárolók használata a virtuális gép biztonsági mentésére

1.  A Azure Portal **Recovery Services**-tárolók keresése elemre.

    ![Recovery Services-tárolók lapja](./media/oracle-backup-recovery/recovery_service_01.png)

2.  A **Recovery Services** -tárolók panelen új tároló hozzáadásához kattintson a **Hozzáadás**gombra.

    ![Recovery Services-tárolók hozzáadása lap](./media/oracle-backup-recovery/recovery_service_02.png)

3.  A folytatáshoz kattintson a **myVault**elemre.

    ![Recovery Services-tárolók részletes lapja](./media/oracle-backup-recovery/recovery_service_03.png)

4.  A **myVault** panelen kattintson a **biztonsági mentés**elemre.

    ![Recovery Services-tárolók biztonsági másolatának lapja](./media/oracle-backup-recovery/recovery_service_04.png)

5.  A **biztonsági mentés célja** panelen használja az **Azure** és a **virtuális gép**alapértelmezett értékeit. Kattintson az **OK** gombra.

    ![Recovery Services-tárolók részletes lapja](./media/oracle-backup-recovery/recovery_service_05.png)

6.  A **biztonsági mentési**szabályzathoz használja az **DefaultPolicy**-t, vagy válassza az **új szabályzat létrehozása**lehetőséget. Kattintson az **OK** gombra.

    ![Recovery Services-tárolók biztonsági mentési szabályzatának részletes lapja](./media/oracle-backup-recovery/recovery_service_06.png)

7.  A **virtuális gépek kijelölése** panelen jelölje be a **myVM1** jelölőnégyzetet, majd kattintson az **OK**gombra. Kattintson a **biztonsági mentés engedélyezése** gombra.

    ![A tároló elemeinek Recovery Services a biztonsági mentés részletei lapra](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Miután rákattintott a **biztonsági mentés engedélyezése**lehetőségre, a biztonsági mentési folyamat nem indul el, amíg az ütemezett idő lejár. Ha azonnali biztonsági mentést szeretne beállítani, hajtsa végre a következő lépést.

8.  A **myVault-Backup elemek** panelen, a biztonsági másolati elemek **száma**területen válassza ki a biztonsági másolati elemek darabszámát.

    ![Recovery Services-tárolók myVault részletei lap](./media/oracle-backup-recovery/recovery_service_08.png)

9.  A **biztonsági mentési elemek (Azure-beli virtuális gép)** panel jobb oldalán kattintson a három pontra (**..**.), majd a **biztonsági mentés**elemre.

    ![Recovery Services-tárolók biztonsági mentése – parancs](./media/oracle-backup-recovery/recovery_service_09.png)

10. Kattintson a **Backup (biztonsági mentés** ) gombra. Várjon, amíg a biztonsági mentési folyamat befejeződik. Ezután folytassa [a 6. lépéssel: az adatbázisfájlok eltávolítása](#step-6-remove-the-database-files).

    A biztonsági mentési feladat állapotának megtekintéséhez kattintson a **feladatok**lehetőségre.

    ![Recovery Services-tárolók feladatainak lapja](./media/oracle-backup-recovery/recovery_service_10.png)

    A biztonsági mentési feladatok állapota a következő képen jelenik meg:

    ![Recovery Services-tárolók feladatainak állapota](./media/oracle-backup-recovery/recovery_service_11.png)

11. Egy alkalmazással konzisztens biztonsági mentés esetén a naplófájlban felmerülő hibák elhárítása. A naplófájl a következő helyen található:/var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>6. lépés: az adatbázisfájlok eltávolítása 
A cikk későbbi részében megtudhatja, hogyan tesztelheti a helyreállítási folyamatot. A helyreállítási folyamat tesztelése előtt el kell távolítania az adatbázisfájlok fájljait.

1.  Távolítsa el az Tablespace és a backup fájlokat:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  Választható Az Oracle-példány leállítása:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>A törölt fájlok visszaállítása a Recovery Services-tárolóból
A törölt fájlok visszaállításához hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg a *myVault* Recovery Services-tárolók elemét. Az **Áttekintés** panel **biztonsági másolati elemek**területén válassza ki az elemek számát.

    ![Recovery Services-tárolók myVault biztonsági másolati elemei](./media/oracle-backup-recovery/recovery_service_12.png)

2. A **biztonsági másolati elemek**száma területen válassza ki az elemek számát.

    ![Recovery Services tárolók Azure-beli virtuális gép biztonsági mentési elemeinek száma](./media/oracle-backup-recovery/recovery_service_13.png)

3. A **Myvm1** panelen kattintson a **File Recovery (előzetes verzió)** elemre.

    ![Képernyőkép a Recovery Services-tárolók fájljának helyreállítási oldaláról](./media/oracle-backup-recovery/recovery_service_14.png)

4. A **fájl-helyreállítás (előzetes verzió)** panelen kattintson a **parancsfájl letöltése**elemre. Ezután mentse a letöltési (. sh) fájlt az ügyfélszámítógép egyik mappájába.

    ![Parancsfájl-mentési beállítások letöltése](./media/oracle-backup-recovery/recovery_service_15.png)

5. Másolja az. sh fájlt a virtuális gépre.

    Az alábbi példa bemutatja, hogyan helyezheti át a fájlt a virtuális gépre a biztonságos másolás (SCP) parancs használatával. A vágólapra másolhatja is a tartalmat, majd beillesztheti a tartalmat a virtuális gépen beállított új fájlba.

    > [!IMPORTANT]
    > Az alábbi példában ellenőrizze, hogy az IP-cím és a mappa értékét frissíti-e. Az értékeknek arra a mappára kell leképezni, ahová a fájlt mentette.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```

6. Módosítsa a fájlt úgy, hogy az a gyökér tulajdonosa legyen.

    A következő példában módosítsa a fájlt úgy, hogy az a gyökeréhez legyen rendelve. Ezután módosítsa az engedélyeket.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```

    Az alábbi példa azt mutatja be, hogy mit kell látni az előző szkript futtatása után. Ha a rendszer felszólítja a folytatásra, írja be az **Y**értéket.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. A csatlakoztatott kötetekhez való hozzáférés megerősítve.

    A kilépéshez írja be a **q**kifejezést, majd keresse meg a csatlakoztatott köteteket. A hozzáadott kötetek listájának létrehozásához írja be a következőt a parancssorba: **DF-k**.

    ![A DF-k parancs](./media/oracle-backup-recovery/recovery_service_16.png)

8. A következő szkripttel másolja vissza a hiányzó fájlokat a mappákba:

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```

9. A következő parancsfájlban a Oláh Anna használatával állítsa helyre az adatbázist:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

10. Válassza le a lemezt.

    A Azure Portal a **fájl-helyreállítás (előzetes verzió) panelen** kattintson a **lemezek leválasztása**elemre.

    ![Lemezek leválasztása parancs](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>A teljes virtuális gép visszaállítása

A törölt fájlok Recovery Services tárolóból való visszaállítása helyett visszaállíthatja a teljes virtuális gépet.

### <a name="step-1-delete-myvm"></a>1. lépés: a myVM törlése

*   A Azure Portal nyissa meg a **myVM1** -tárolót, majd válassza a **Törlés**lehetőséget.

    ![Tár törlési parancsa](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>2. lépés: a virtuális gép helyreállítása

1.  Nyissa meg **Recovery Services**-tárolókat, majd válassza a **myVault**lehetőséget.

    ![myVault-bejegyzés](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Az **Áttekintés** panel **biztonsági másolati elemek**területén válassza ki az elemek számát.

    ![elemek biztonsági mentése myVault](./media/oracle-backup-recovery/recover_vm_03.png)

3.  A **biztonsági mentési elemek (Azure-beli virtuális gép)** panelen válassza a **myvm1**lehetőséget.

    ![Helyreállítási virtuális gép lapja](./media/oracle-backup-recovery/recover_vm_04.png)

4.  A **myvm1** panelen kattintson a három pontra (**..**.), majd a **virtuális gép visszaállítása**gombra.

    ![VM-parancs visszaállítása](./media/oracle-backup-recovery/recover_vm_05.png)

5.  A **visszaállítási pont kiválasztása** panelen jelölje ki a visszaállítani kívánt elemet, majd kattintson **az OK**gombra.

    ![A visszaállítási pont kiválasztása](./media/oracle-backup-recovery/recover_vm_06.png)

    Ha engedélyezte az alkalmazás-konzisztens biztonsági mentést, megjelenik egy függőleges kék sáv.

6.  A **konfiguráció visszaállítása** panelen jelölje ki a virtuális gép nevét, válassza ki az erőforráscsoportot, majd kattintson az **OK**gombra.

    ![Konfigurációs értékek visszaállítása](./media/oracle-backup-recovery/recover_vm_07.png)

7.  A virtuális gép visszaállításához kattintson a **visszaállítás** gombra.

8.  A visszaállítási folyamat állapotának megtekintéséhez kattintson a **feladatok**, majd a **biztonsági mentési feladatok**elemre.

    ![Biztonsági mentési feladatok állapotának parancsa](./media/oracle-backup-recovery/recover_vm_08.png)

    Az alábbi ábrán a visszaállítási folyamat állapota látható:

    ![A visszaállítási folyamat állapota](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>3. lépés: a nyilvános IP-cím beállítása
A virtuális gép visszaállítása után állítsa be a nyilvános IP-címet.

1.  A keresőmezőbe írja be a **nyilvános IP-címet**.

    ![Nyilvános IP-címek listája](./media/oracle-backup-recovery/create_ip_00.png)

2.  A **nyilvános IP-címek** panelen kattintson a **Hozzáadás**gombra. A **nyilvános IP-cím létrehozása** panelen a **név**mezőben válassza ki a nyilvános IP-címet. Az **Erőforráscsoport** területen válassza a **Meglévő használata** lehetőséget. Ezután kattintson a **Létrehozás**gombra.

    ![IP-cím létrehozása](./media/oracle-backup-recovery/create_ip_01.png)

3.  Ha a nyilvános IP-címet a virtuális gép hálózati adapteréhez szeretné rendelni, keresse meg és válassza ki a **myVMip**. Ezután kattintson a **hozzárendelés**elemre.

    ![IP-cím hozzárendelése](./media/oracle-backup-recovery/create_ip_02.png)

4.  Az **erőforrástípus**mezőben válassza a **hálózati adapter**lehetőséget. Válassza ki a myVM-példány által használt hálózati adaptert, majd kattintson **az OK**gombra.

    ![Adja meg az erőforrás típusát és a NIC-értékeket](./media/oracle-backup-recovery/create_ip_03.png)

5.  Keresse meg és nyissa meg a portálról portolt myVM-példányt. A virtuális géphez hozzárendelt IP-cím megjelenik a myVM **Áttekintés** paneljén.

    ![IP-cím értéke](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>4. lépés: Kapcsolódás a virtuális géphez

*   A virtuális géphez való kapcsolódáshoz használja a következő parancsfájlt:

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>5. lépés: annak tesztelése, hogy az adatbázis elérhető-e
*   A kisegítő lehetőségek teszteléséhez használja a következő parancsfájlt:

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Ha az adatbázis- **indítási** parancs hibát generál, az adatbázis helyreállításához tekintse meg a [6. lépés: a Oláh Anna használata az adatbázis helyreállításához](#step-6-optional-use-rman-to-recover-the-database)című témakört.

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>6. lépés: (nem kötelező) az adatbázis helyreállításához használja a Oláh Anna
*   Az adatbázis helyreállításához használja a következő parancsfájlt:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

A Oracle Database 12c-adatbázis biztonsági mentése és helyreállítása egy Azure Linux rendszerű virtuális gépen befejeződött.

## <a name="delete-the-vm"></a>A virtuális gép törlése

Ha már nincs szüksége a virtuális gépre, a következő paranccsal távolíthatja el az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

[Oktatóanyag: kiválóan elérhető virtuális gépek létrehozása](../../linux/create-cli-complete.md)

[A virtuális gépek üzembe helyezésének megismerése Azure CLI-mintákkal](../../linux/cli-samples.md)




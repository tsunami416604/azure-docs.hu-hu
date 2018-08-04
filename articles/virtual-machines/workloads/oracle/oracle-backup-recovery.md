---
title: Biztonsági mentése és helyreállítása, az Oracle Database 12c adatbázis egy Azure-beli Linuxos virtuális gépen |} A Microsoft Docs
description: Ismerje meg, hogyan biztonsági mentése és helyreállítása, az Oracle Database 12c adatbázis az Azure-környezetben.
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
ms.openlocfilehash: 93fbd5bbba91b45e1afd123a2466b249302e2354
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492840"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Biztonsági mentése és helyreállítása, az Oracle Database 12c adatbázis egy Azure-beli Linuxos virtuális gépen

Azure CLI segítségével létrehozása és kezelése az Azure-erőforrások parancssorból vagy szkriptek. Ez a cikk az Azure CLI-szkriptek használjuk egy Oracle Database 12c adatbázis az Azure Marketplace-en katalógus-lemezkép telepítése.

Mielőtt elkezdené, győződjön meg arról, hogy az Azure CLI telepítve van-e. További információkért lásd: a [Azure CLI telepítési útmutatóját](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>A környezet előkészítése

### <a name="step-1-prerequisites"></a>1. lépés: Előfeltételek

*   A biztonsági mentési és helyreállítási folyamat végrehajtásához először létre kell hoznia egy Linux rendszerű virtuális gép, amely rendelkezik az Oracle Database 12c telepített példányát. A Piactéri lemezképet hozhat létre a virtuális gép neve *Oracle: Oracle-adatbázis – Ee:12.1.0.2:latest*.

    Megtudhatja, hogyan hozhat létre egy Oracle database, tekintse meg a [Oracle database gyors üzembe helyezése létrehozása](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>2. lépés: Kapcsolódás a virtuális Géphez

*   Secure Shell (SSH)-munkamenetet létrehozni a virtuális géppel, a következő paranccsal. Cserélje le az IP-cím és a gazdagép neve együtt a `publicIpAddress` értéket a virtuális géphez.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>3. lépés: Az adatbázis előkészítése

1.  Ez a lépés azt feltételezi, hogy rendelkezik-e egy nevű virtuális gépen futó Oracle-példányok (cdb1) *myVM*.

    Futtassa a *oracle* superuser legfelső szintű, és ezután inicializálása a figyelőt:

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

2.  (Nem kötelező) Ellenőrizze, hogy az adatbázis archív log módban van:

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
3.  (Nem kötelező) Hozzon létre egy táblát a véglegesítés teszteléséhez:

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
4.  Győződjön meg arról, vagy módosítsa a biztonságimásolat-fájl helye és mérete:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Oracle Recovery Manager (RMAN) használatával az adatbázis biztonsági mentése:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>4. lépés: Linux rendszerű virtuális gépek alkalmazáskonzisztens biztonsági mentés

Alkalmazáskonzisztens biztonsági mentést egy olyan új szolgáltatás az Azure Backup. Hozzon létre, és jelölje be a parancsfájlok végrehajtása előtt és után a virtuális gép pillanatképét, (pillanatkép előtti és utáni pillanatkép).

1. Töltse le a JSON-fájlt.

    Töltse le a VMSnapshotScriptPluginConfig.json https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig. A fájl tartalmának keresse meg a következőhöz hasonló:

    ```azurecli
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

2. A /etc/azure mappa létrehozása a virtuális gépen:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Másolja a JSON-fájlt.

    VMSnapshotScriptPluginConfig.json a /etc/azure mappába másolja.

4. Szerkessze a JSON-fájlt.

    A VMSnapshotScriptPluginConfig.json-fájl szerkesztése a `PreScriptLocation` és `PostScriptlocation` paramétereket. Példa:

    ```azurecli
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

5. A pillanatkép előtti és utáni parancsfájl-fájlokat hoz létre.

    Íme egy példa egy "offline biztonsági mentés" pillanatkép előtti és utáni szkriptekkel (egy offline biztonsági mentés leállítása és újraindítása):

    A /etc/azure/pre_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    A /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Íme egy példa egy "gyakran használt adatok biztonsági mentésének" pillanatkép előtti és utáni szkriptekkel (online biztonsági mentés):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    A /etc/azure/post_script.sh:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    /Etc/azure/pre_script.sql módosítsa a igényeknek a fájl tartalmát:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    /Etc/azure/post_script.sql módosítsa a igényeknek a fájl tartalmát:

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

    Tesztelje a parancsfájlokat, először jelentkezzen be rendszergazdaként. Ezt követően ellenőrizze, hogy nincsenek-e hibák:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

További információkért lásd: [Linux rendszerű virtuális gépek alkalmazáskonzisztens biztonsági mentés](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>5. lépés: Használja az Azure Recovery Services-tárolók biztonsági mentése a virtuális gép

1.  Az Azure Portalon keresse meg **Recovery Services-tárolók**.

    ![Recovery Services-tárolók oldalon](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Az a **Recovery Services-tárolók** adjon hozzá egy új tároló paneljén kattintson **Hozzáadás**.

    ![Recovery Services-tárolók oldal hozzáadása](./media/oracle-backup-recovery/recovery_service_02.png)

3.  A folytatáshoz kattintson a **myVault**.

    ![Recovery Services-tárolók Részletek lap](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Az a **myVault** panelen kattintson a **Backup**.

    ![Recovery Services-tárolók biztonságimásolat-készítési lapján](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Az a **biztonsági mentés célja** panelen használja az alapértelmezett értékeket, **Azure** és **virtuális gép**. Kattintson az **OK** gombra.

    ![Recovery Services-tárolók Részletek lap](./media/oracle-backup-recovery/recovery_service_05.png)

6.  A **biztonsági mentési szabályzat**, használjon **DefaultPolicy**, vagy válasszon ki **hozzon létre új házirendet**. Kattintson az **OK** gombra.

    ![Recovery Services-tárolók, biztonsági mentési házirend részletei lap](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Az a **válassza ki a virtuális gépek** panelen válasszon ki a **myVM1** jelölőnégyzetet, majd kattintson a **OK**. Kattintson a **biztonsági mentés engedélyezése** gombra.

    ![Recovery Services-tárolók elemek a biztonsági mentés részletei lap](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Miután rákattintott **biztonsági mentés engedélyezése**, a biztonsági mentési folyamat nem indul el, amíg le nem jár az ütemezett időpont. Állítsa be az azonnali biztonsági mentés, hajtsa végre a következő lépéssel.

8.  Az a **myVault - biztonsági másolati elemek** panel alatt **biztonsági MENTÉSI elemek száma**, válassza ki a biztonsági másolati elemek száma.

    ![Recovery Services-tárolók myVault részletei oldalon](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Az a **biztonsági mentési elemek (Azure virtuális gép)** paneljén, az oldal jobb oldalán kattintson a három pontra (**...** ) gombra, majd **biztonsági mentés**.

    ![Biztonsági mentés most parancsot a Recovery Services-tárolók](./media/oracle-backup-recovery/recovery_service_09.png)

10. Kattintson a **Backup** gombra. Várjon, amíg a biztonsági mentési folyamat befejezéséhez. Folytassa a [6. lépés: távolítsa el az adatbázisfájlok](#step-6-remove-the-database-files).

    A biztonsági mentési feladat állapotának megtekintéséhez kattintson **feladatok**.

    ![Recovery Services-tárolók feladat lap](./media/oracle-backup-recovery/recovery_service_10.png)

    A biztonsági mentési feladat állapotát az alábbi képen a jelenik meg:

    ![Recovery Services-tárolók feladat állapota lap](./media/oracle-backup-recovery/recovery_service_11.png)

11. Az alkalmazáskonzisztens biztonsági mentés cím esetleges hibákat a naplófájlban. A naplófájl nem található: /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0.

### <a name="step-6-remove-the-database-files"></a>6. lépés: Távolítsa el az adatbázisfájlok 
Ez a cikk későbbi részében fogja ismerje meg a helyreállítási folyamat tesztelése. Mielőtt tesztelhetné a helyreállítási folyamat, el kell távolítania az adatbázisfájlokat.

1.  Távolítsa el a táblaterületen. keresse és a biztonsági mentési fájlokat:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Nem kötelező) Állítsa le az Oracle-példány:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>A törölt fájlok visszaállítása a Recovery Services-tárolók
A törölt fájlok visszaállításához kövesse az alábbi lépéseket:

1. Az Azure Portalon keresse meg a *myVault* Recovery Services-tárolók elemet. Az a **áttekintése** panel alatt **biztonsági mentési elemek**, válassza ki az elemek számát.

    ![Recovery Services tároló myVault biztonsági másolati elemek](./media/oracle-backup-recovery/recovery_service_12.png)

2. A **biztonsági MENTÉSI elemek száma**, válassza ki az elemek számát.

    ![Recovery Services-tárolók az Azure virtuális gép biztonsági másolati elemek száma](./media/oracle-backup-recovery/recovery_service_13.png)

3. Az a **myvm1** panelen kattintson a **fájlhelyreállítás (előzetes verzió)**.

    ![Képernyőkép a Recovery Services-tárolók helyreállítás lapja](./media/oracle-backup-recovery/recovery_service_14.png)

4. Az a **fájlhelyreállítás (előzetes verzió)** ablaktáblán kattintson a **szkript letöltése**. Az ügyfélszámítógépen, majd mentse a download (.sh) fájlt egy mappába.

    ![Töltse le a szkript fájlba menti beállításai](./media/oracle-backup-recovery/recovery_service_15.png)

5. Másolja a .sh-fájlját a virtuális géphez.

    Az alábbi példa bemutatja, hogyan használhatja a biztonságos másolás (scp) paranccsal helyezze át a fájlt a virtuális Gépet. Is másolhatja a tartalmát a vágólapra, majd illessze be a tartalmát egy új fájlba, amely a virtuális gép be van állítva.

    > [!IMPORTANT]
    > A következő példában győződjön meg arról, hogy frissíti az IP cím és a mappa értékeket. Az értékek képezze le a mappába, ahová mentette a fájlt.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. A fájl módosítani, hogy a legfelső szintű tulajdonában van.

    A következő példában a fájl módosítása, hogy a legfelső szintű tulajdonában van. Ezután módosítsa az engedélyeket.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    Az alábbi példa bemutatja, lásd a fenti szkript futtatása után. Ha van, a folytatás megerősítését kéri, adja meg **Y**.

    ```bash
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

7. A csatlakoztatott kötetek való hozzáférés ellenőrzése.

    Adja meg a kilépéshez **q**, majd keresse meg a csatlakoztatott kötetek. A felvett kötetek listájának létrehozásához parancsot a parancssorba írja be **df -k**.

    ![A df -k parancs](./media/oracle-backup-recovery/recovery_service_16.png)

8. A következő parancsfájl használatával másolja vissza a hiányzó fájlokat a mappák:

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
9. Az alábbi szkriptben RMAN használatával állítsa helyre az adatbázist:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. A lemez leválasztásához.

    Az Azure Portalon az a **fájlhelyreállítás (előzetes verzió)** panelen kattintson a **lemezek leválasztása**.

    ![Válassza le a lemezek parancs](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>A teljes virtuális gép helyreállítása

Ahelyett, hogy a törölt fájlok visszaállítása a Recovery Services-tárolók, visszaállíthatja a teljes virtuális Gépet.

### <a name="step-1-delete-myvm"></a>1. lépés: Törlés myVM

*   Az Azure Portalon nyissa meg a **myVM1** tárba, és válassza ki **törlése**.

    ![Tároló törlése paranccsal](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>2. lépés: A virtuális gép helyreállítása

1.  Lépjen a **Recovery Services-tárolók**, majd válassza ki **myVault**.

    ![myVault bejegyzés](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Az a **áttekintése** panel alatt **biztonsági mentési elemek**, válassza ki az elemek számát.

    ![myVault elemek biztonsági mentése](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Az a **biztonsági mentési elemek (Azure virtuális gép)** panelen válassza ki **myvm1**.

    ![A helyreállítási virtuális gép lap](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Az a **myvm1** panelen kattintson a három pont (**...** ) gombra, majd **virtuális gép visszaállítása**.

    ![A restore parancs a virtuális gép](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Az a **visszaállítási pont kiválasztása** panelt, válassza ki a visszaállítani kívánt elemet, és kattintson **OK**.

    ![A visszaállítási pont kijelölése](./media/oracle-backup-recovery/recover_vm_06.png)

    Ha engedélyezte az alkalmazáskonzisztens biztonsági mentés, egy függőleges kék sáv jelenik meg.

6.  Az a **konfiguráció visszaállítása** panelen válassza ki a virtuális gép nevét, válassza ki az erőforráscsoportot, és kattintson a **OK**.

    ![Állítsa vissza a konfigurációs értékek](./media/oracle-backup-recovery/recover_vm_07.png)

7.  A virtuális gép visszaállításához kattintson a **visszaállítása** gombra.

8.  A visszaállítási folyamat állapotának megtekintéséhez kattintson **feladatok**, és kattintson a **biztonsági mentési feladatok**.

    ![Biztonsági mentési feladatok állapot parancs](./media/oracle-backup-recovery/recover_vm_08.png)

    Az alábbi ábra a visszaállítási folyamat állapotát jeleníti meg:

    ![A visszaállítási folyamat állapota](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>3. lépés: A nyilvános IP-cím beállítása
A virtuális gép visszaállítása után állítsa be a nyilvános IP-címet.

1.  A Keresés mezőbe írja be a **nyilvános IP-cím**.

    ![Nyilvános IP-címek listája](./media/oracle-backup-recovery/create_ip_00.png)

2.  Az a **nyilvános IP-címek** panelen kattintson a **Hozzáadás**. Az a **nyilvános IP-cím létrehozása** panelen a **neve**, válassza ki a nyilvános IP-név. Az **Erőforráscsoport** területen válassza a **Meglévő használata** lehetőséget. Ezt követően kattintson a **Create** (Létrehozás) gombra.

    ![IP-cím létrehozása](./media/oracle-backup-recovery/create_ip_01.png)

3.  A hálózati adaptert a virtuális gép nyilvános IP-cím társítása, keresése és kiválasztása **myVMip**. Kattintson a **társítása**.

    ![IP-cím társítása](./media/oracle-backup-recovery/create_ip_02.png)

4.  A **erőforrástípus**válassza **hálózati adapter**. Válassza ki a hálózati adaptert, amely a myVM példány által használt, és kattintson a **OK**.

    ![Válassza ki az erőforrás típusa és a hálózati adapter értékek](./media/oracle-backup-recovery/create_ip_03.png)

5.  Keresse meg és nyissa meg az, hogy van-e már myVM példányát a portálról. A virtuális Géphez társított IP-cím jelenik meg a myVM **áttekintése** panelen.

    ![IP-címének értékét](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>4. lépés: Kapcsolódás a virtuális Géphez

*   A virtuális Géphez való csatlakozáshoz, használja a következő szkriptet:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>5. lépés: Ellenőrizze, hogy az adatbázis érhető el
*   Kisegítő lehetőségek teszteléséhez használja a következő szkriptet:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Ha az adatbázis **indítási** parancs hibát generál, állítsa helyre az adatbázist, lásd: [6. lépés: az adatbázis helyreállításához használja RMAN](#step-6-optional-use-rman-to-recover-the-database).

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>6. lépés: (Nem kötelező) használata RMAN az adatbázis helyreállítása
*   Állítsa helyre az adatbázist, használja a következő parancsfájlt:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

A biztonsági mentés és helyreállítás az Oracle Database 12c adatbázis-beli Linux rendszerű virtuális gépen már befejeződött.

## <a name="delete-the-vm"></a>A virtuális gép törlése

Ha már nincs szüksége a virtuális Gépet, a következő paranccsal eltávolítható az erőforráscsoport, a virtuális gép és minden kapcsolódó erőforrás:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Magas rendelkezésre állású virtuális gépek létrehozása](../../linux/create-cli-complete.md)

[Ismerje meg a virtuális gép üzembe helyezés az Azure CLI-minták](../../linux/cli-samples.md)




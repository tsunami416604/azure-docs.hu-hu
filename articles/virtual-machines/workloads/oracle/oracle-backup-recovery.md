---
title: "Biztonsági mentése és helyreállítása Azure Linux virtuális géphez az Oracle-adatbázishoz 12c adatbázis |} Microsoft Docs"
description: "Útmutató: biztonsági mentése és helyreállítása az Oracle-adatbázishoz 12c adatbázis az Azure környezetben."
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
ms.date: 5/17/2017
ms.author: rclaus
ms.openlocfilehash: 9a2293f13b90e9a4cb11b4169fad969dd622a9a6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Készítsen biztonsági másolatot, és az Azure Linux virtuális gép Oracle-adatbázishoz 12c-adatbázis helyreállítása

Azure CLI segítségével létrehozása és kezelése az Azure-erőforrások parancsot a parancssorba, vagy parancsfájlok használata. Ebben a cikkben az Azure parancssori felület parancsfájlok használjuk az Oracle-adatbázishoz 12c adatbázis Azure piactér gyűjtemény lemezkép telepítéséhez.

Mielőtt elkezdené, győződjön meg arról, hogy telepítve van-e az Azure parancssori felület. További információkért lásd: a [Azure parancssori felület telepítési útmutató](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="prepare-the-environment"></a>A környezet előkészítése

### <a name="step-1-prerequisites"></a>1. lépés: Előfeltételek

*   A biztonsági mentési és helyreállítási folyamat végrehajtásához először létre kell hoznia egy Linux virtuális Gépet, amely rendelkezik az Oracle-adatbázishoz 12c telepített példánya. A Piactéri lemezképet hozhat létre a virtuális gép neve *Oracle: Oracle-adatbázis-Ee:12.1.0.2:latest*.

    Az Oracle-adatbázis létrehozásához, lásd: a [Oracle adatbázis gyors üzembe helyezés létrehozása](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create).


### <a name="step-2-connect-to-the-vm"></a>2. lépés: Csatlakoztassa a virtuális Gépet

*   Secure Shell (SSH) munkamenetet létrehozni a virtuális gép, használja a következő parancsot. Az IP-cím és a gazdagép neve együtt cserélje le a `publicIpAddress` értéket a virtuális gép számára.

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>3. lépés: Az adatbázis előkészítése

1.  Ez a lépés feltételezi, hogy rendelkezik-e egy egy nevű virtuális gépen futó Oracle-példányok (cdb1) *myVM*.

    Futtassa a *oracle* felügyelő legfelső szintű és a figyelő majd inicializálása:

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

2.  (Választható) Ellenőrizze, hogy az adatbázis archív napló módban van:

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
3.  (Választható) Hozzon létre egy táblát, a véglegesítés teszteléséhez:

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
5. Oracle Recovery Manager (RMAN) segítségével készítsen biztonsági másolatot az adatbázisról:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>4. lépés: A Linux virtuális gépek alkalmazáskonzisztens biztonsági mentését

Alkalmazáskonzisztens biztonsági mentést az Azure biztonsági mentés új szolgáltatása. Hozzon létre, és válassza ki a parancsfájlok végrehajtása előtt és után a virtuális gép pillanatkép (pillanatkép előtti és utáni pillanatkép).

1. A JSON-fájl letöltésére.

    Https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig VMSnapshotScriptPluginConfig.json le. A fájl tartalmának keressen a következőhöz hasonló:

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

2. A /etc/azure mappa létrehozása a virtuális Gépen:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. A JSON-fájl másolása.

    VMSnapshotScriptPluginConfig.json másolása a /etc/azure mappába.

4. Szerkessze a JSON-fájlt.

    A VMSnapshotScriptPluginConfig.json-fájl szerkesztése a `PreScriptLocation` és `PostScriptlocation` paraméterek. Példa:

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

5. A pillanatkép előtti és a pillanatkép utáni parancsfájlok létrehozása.

    Íme egy példa a "cold biztonsági mentéshez" pillanatkép előtti és a pillanatkép utáni parancsfájlok (offline biztonsági másolat, leállítása és újraindítása):

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

    Íme egy példa a "gyors biztonsági mentéshez" pillanatkép előtti és a pillanatkép utáni parancsfájlok (online biztonsági mentés):

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

    A /etc/azure/pre_script.sql módosítsa a / a követelmények a fájl tartalmát:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    A /etc/azure/post_script.sql módosítsa a / a követelmények a fájl tartalmát:

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

    Tesztelje a parancsfájlokat, először jelentkezzen be rendszergazdaként. Majd győződjön meg arról, hogy nincsenek-e hibák:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

További információkért lásd: [Linux virtuális gépek alkalmazáskonzisztens biztonsági mentését](https://azure.microsoft.com/en-us/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/).


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>5. lépés: Használata Azure Recovery Services-tárolók biztonsági mentése a virtuális gép

1.  Az Azure-portálon a keresse meg a **Recovery Services-tárolók**.

    ![Helyreállítási szolgáltatások tárolók lap](./media/oracle-backup-recovery/recovery_service_01.png)

2.  Az a **Recovery Services-tárolók** panelt, adja hozzá egy új tárolót, kattintson a **Hozzáadás**.

    ![Helyreállítási szolgáltatások tárolók-weblap hozzáadása](./media/oracle-backup-recovery/recovery_service_02.png)

3.  A folytatáshoz kattintson a **myVault**.

    ![Helyreállítási szolgáltatások tárolók részletességi lap](./media/oracle-backup-recovery/recovery_service_03.png)

4.  Az a **myVault** panelen kattintson a **biztonsági mentés**.

    ![Helyreállítási szolgáltatások tárolók lap biztonsági mentése](./media/oracle-backup-recovery/recovery_service_04.png)

5.  Az a **biztonsági mentési cél** panelen, használja az alapértelmezett értékekkel rendelkező **Azure** és **virtuális gép**. Kattintson az **OK** gombra.

    ![Helyreállítási szolgáltatások tárolók részletességi lap](./media/oracle-backup-recovery/recovery_service_05.png)

6.  A **biztonsági mentési házirend**, használjon **DefaultPolicy**, vagy válasszon **hozzon létre új házirend**. Kattintson az **OK** gombra.

    ![Helyreállítási szolgáltatások tárolók biztonsági mentési házirend Részletek lap](./media/oracle-backup-recovery/recovery_service_06.png)

7.  Az a **válassza ki a virtuális gépek** panelen válassza ki a **myVM1** jelölőnégyzetet, majd kattintson a **OK**. Kattintson a **biztonságimásolat-készítés engedélyezése** gombra.

    ![Helyreállítási szolgáltatások tárolók elemeket a biztonsági mentési Részletek lap](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Miután rákattintott **biztonságimásolat-készítés engedélyezése**, a biztonsági mentés nem indul el, amíg az ütemezett időpontban lejár. Az azonnali biztonsági másolat beállítása, a következő lépés végrehajtása.

8.  A a **myVault - biztonsági mentés elemek** panel alatt **biztonsági MENTÉSI ELEMSZÁMNÁL**, válassza ki a biztonsági mentési elemek száma.

    ![Helyreállítási szolgáltatások tárolók myVault Részletek lap](./media/oracle-backup-recovery/recovery_service_08.png)

9.  Az a **(Azure virtuális gép) biztonsági mentés elemek** panelen, a lap jobb oldalán kattintson a három pont (**...** ) gombra, majd **biztonsági mentés most**.

    ![Helyreállítási szolgáltatások tárolók biztonsági mentés most parancsot.](./media/oracle-backup-recovery/recovery_service_09.png)

10. Kattintson a **biztonsági mentés** gombra. Várja meg a biztonsági mentési folyamat befejezéséhez. Folytassa a [6. lépés: távolítsa el az adatbázisfájlok](#step-6-remove-the-database-files).

    A biztonsági mentési feladat állapotának megtekintéséhez kattintson **feladatok**.

    ![Helyreállítási szolgáltatások tárolók feladat lap](./media/oracle-backup-recovery/recovery_service_10.png)

    A biztonsági mentési feladat állapotát a következő kép jelenik meg:

    ![Helyreállítási szolgáltatások tárolók feladat állapotú lap](./media/oracle-backup-recovery/recovery_service_11.png)

11. Az alkalmazáskonzisztens biztonsági mentését címek a naplófájl-e hibák. A naplófájl /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0 helyezkedik el.

### <a name="step-6-remove-the-database-files"></a>6. lépés: Távolítsa el az adatbázis fájljai 
A cikk későbbi részében megtudhatja, hogyan a helyreállítási folyamat ellenőrzéséhez. A helyreállítási folyamat teszteléséhez előtt el kell távolítania az adatbázisfájlokat.

1.  Távolítsa el a táblaterületen és a biztonsági mentési fájlokat:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Választható) Állítsa le az Oracle-példány:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>A Recovery Services-tárolók a törölt fájlok visszaállítása
A törölt fájlok visszaállításához kövesse az alábbi lépéseket:

1. Az Azure portálon keresse meg a *myVault* Recovery Services-tárolók elemet. A a **áttekintése** panel alatt **elem biztonsági mentését**, válassza ki az elemek száma.

    ![Helyreállítási szolgáltatások tárolók myVault biztonsági másolati elemei](./media/oracle-backup-recovery/recovery_service_12.png)

2. A **biztonsági MENTÉS ELEMSZÁMNÁL**, válassza ki az elemek száma.

    ![Recovery Services-tárolók Azure virtuális gép biztonsági mentési elemek száma](./media/oracle-backup-recovery/recovery_service_13.png)

3. Az a **myvm1** panelen kattintson a **fájlhelyreállítás (előzetes verzió)**.

    ![Képernyőfelvétel a Recovery Services-tárolók helyreállítási lapja](./media/oracle-backup-recovery/recovery_service_14.png)

4. Az a **fájlhelyreállítás (előzetes verzió)** ablaktáblán kattintson a **parancsfájl letöltése**. Az ügyfélszámítógépen, majd menti a letöltési (.sh) fájlt egy mappába.

    ![Töltse le a parancsfájl mentése beállításai](./media/oracle-backup-recovery/recovery_service_15.png)

5. Másolja a .sh fájlt a virtuális Gépet.

    A következő példa bemutatja, hogyan biztonságos másolatot (scp) használatát a fájl áthelyezéséhez a virtuális gép parancsot. Akkor is is tartalmának másolása a vágólapra, majd illessze be a tartalom egy új fájlba, amely a virtuális Gépre van beállítva.

    > [!IMPORTANT]
    > A következő példában győződjön meg arról, hogy frissíti a IP-cím és a mappa értékek. Az értékek a mappába, a fájl menteni kell társítani.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. Módosítsa a fájlt, hogy a legfelső szintű tulajdonában van.

    A következő példában módosítja a fájlt, hogy a legfelső szintű tulajdonában van. Ezután módosítsa az engedélyeket.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    A következő példa bemutatja, lásd az előző parancsfájl futtatása után. Ha van, a folytatás megerősítését kéri, írja be **Y**.

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

    Lépjen ki, írja be a következőt **q**, majd keresse meg a csatlakoztatott kötetek. A felvett kötetek listájának létrehozásához parancsot a parancssorba írja be **df -k**.

    ![A df -k parancs](./media/oracle-backup-recovery/recovery_service_16.png)

8. A következő parancsfájl használata a hiányzó fájlokat másolja vissza a mappák:

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
9. A következő parancsfájlban RMAN használatával állítsa helyre az adatbázist:

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. A lemez leválasztása.

    Az Azure portálon a a **fájlhelyreállítás (előzetes verzió)** panelen kattintson a **lemez leválasztása**.

    ![Válassza le a lemezek parancs](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>Állítsa vissza a teljes méretű VM

Helyett a törölt fájlok visszaállítást végezni a Recovery Services-tárolók, állítsa vissza a teljes virtuális gép.

### <a name="step-1-delete-myvm"></a>1. lépés: Delete myVM

*   Az Azure-portálon lépjen a **myVM1** tároló, és válassza ki **törlése**.

    ![Tároló delete parancs esetében](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>2. lépés: A virtuális gép helyreállítása

1.  Ugrás a **Recovery Services-tárolók**, majd válassza ki **myVault**.

    ![myVault bejegyzés](./media/oracle-backup-recovery/recover_vm_02.png)

2.  A a **áttekintése** panel alatt **elem biztonsági mentését**, válassza ki az elemek száma.

    ![Készítsen biztonsági másolatot elemek myVault](./media/oracle-backup-recovery/recover_vm_03.png)

3.  Az a **(Azure virtuális gép) biztonsági mentés elemek** panelen válassza **myvm1**.

    ![A helyreállítási virtuális gép lap](./media/oracle-backup-recovery/recover_vm_04.png)

4.  Az a **myvm1** panelen kattintson a három pont (**...** ) gombra, majd **visszaállítani a virtuális gép**.

    ![A restore parancs méretű VM](./media/oracle-backup-recovery/recover_vm_05.png)

5.  Az a **visszaállítási pont kiválasztása** panelen, válassza ki a visszaállítani kívánt cikket, és végül **OK**.

    ![Válassza ki a visszaállítási pont](./media/oracle-backup-recovery/recover_vm_06.png)

    Ha engedélyezte az alkalmazáskonzisztens biztonsági mentését, egy függőleges kék színű sáv jelenik meg.

6.  Az a **konfiguráció visszaállítása** panelen válassza ki a virtuális gép nevét, válassza ki az erőforráscsoportot, és kattintson **OK**.

    ![Állítsa vissza a konfigurációs értékek](./media/oracle-backup-recovery/recover_vm_07.png)

7.  Állítsa helyre a virtuális Gépet, kattintson a **visszaállítása** gombra.

8.  A visszaállítási folyamat állapotának megtekintéséhez kattintson **feladatok**, és kattintson a **biztonsági mentési feladatok**.

    ![Biztonsági mentési feladatok állapota parancs](./media/oracle-backup-recovery/recover_vm_08.png)

    Az alábbi ábra a visszaállítási folyamat állapotának megjelenítése:

    ![A visszaállítási folyamat állapota](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>3. lépés: A nyilvános IP-cím beállítása
Miután visszaállította a virtuális gép, állítsa be a nyilvános IP-cím.

1.  A keresési mezőbe, írja be a **nyilvános IP-cím**.

    ![Nyilvános IP-címek listája](./media/oracle-backup-recovery/create_ip_00.png)

2.  Az a **nyilvános IP-címek** panelen kattintson a **Hozzáadás**. Az a **nyilvános IP-cím létrehozása** panelen a **neve**, válassza ki a nyilvános IP-név. Az **Erőforráscsoport** területen válassza a **Meglévő használata** lehetőséget. Ezt követően kattintson a **Create** (Létrehozás) gombra.

    ![IP-cím létrehozása](./media/oracle-backup-recovery/create_ip_01.png)

3.  A nyilvános IP-címet társítani a hálózati illesztő a virtuális gép számára, és adja meg **myVMip**. Kattintson a **társítása**.

    ![IP-cím hozzárendelése](./media/oracle-backup-recovery/create_ip_02.png)

4.  A **erőforrástípus**, jelölje be **hálózati illesztő**. Válassza ki, hogy az myVM példány által használt hálózati kapcsolat, és kattintson a **OK**.

    ![Válassza ki az erőforrás típusa és a hálózati adapter értékek](./media/oracle-backup-recovery/create_ip_03.png)

5.  Keresse meg, és nyissa meg az, hogy van-e legelterjedtebb myVM példányát a portálról. A virtuális Géphez társított IP-cím jelenik meg a myVM **áttekintése** panelen.

    ![IP-cím értéke](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>4. lépés: Csatlakoztassa a virtuális Gépet

*   Csatlakoztassa a virtuális Gépet, használja a következő parancsfájlt:

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>5. lépés: Az adatbázis elérhető-e tesztelése
*   Kisegítő lehetőségek teszteléséhez a következő parancsprogram használata:

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Ha az adatbázis **indítási** parancs hibát generál, állítsa helyre az adatbázist, lásd: [6. lépés: az adatbázis használata RMAN](#step-6-optional-use-rman-to-recover-the-database).

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

A biztonsági mentési és helyreállítási az Azure Linux virtuális gép Oracle-adatbázishoz 12c adatbázis most már befejeződött.

## <a name="delete-the-vm"></a>A virtuális gép törlése

Ha már nincs szüksége a virtuális Gépet, a következő paranccsal távolítsa el az erőforráscsoportot, a virtuális gép és az összes kapcsolódó erőforrások:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: Hozzon létre magas rendelkezésre állású virtuális gépek](../../linux/create-cli-complete.md)

[Virtuális gép telepítése az Azure parancssori felület minták felfedezése](../../linux/cli-samples.md)




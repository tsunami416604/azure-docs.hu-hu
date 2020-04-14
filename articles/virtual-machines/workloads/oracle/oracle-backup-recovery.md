---
title: Oracle Database 12c adatbázis biztonsági mentése és helyreállítása Azure Linux rendszerű virtuális gépen | Microsoft dokumentumok
description: Ismerje meg, hogyan biztonsági másolatot és helyreállítása egy Oracle Database 12c adatbázis az Azure-környezetben.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: mimckitt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: mimckitt
ms.openlocfilehash: 9e2fcc9533c183a356b19c369777e1319ca7f9ae
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263199"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Oracle Database 12c adatbázis biztonsági mentése és helyreállítása Azure Linux-alapú virtuális gépen

Az Azure CLI segítségével azure-erőforrásokat hozhat létre és kezelhet egy parancssorban, vagy parancsfájlokat használhat. Ebben a cikkben az Azure CLI-parancsfájlok segítségével telepítünk egy Oracle Database 12c adatbázist egy Azure Marketplace-katalógus ból.

Mielőtt elkezdené, győződjön meg arról, hogy az Azure CLI telepítve van. További információt az [Azure CLI telepítési útmutatójában talál.](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="prepare-the-environment"></a>A környezet előkészítése

### <a name="step-1-prerequisites"></a>1. lépés: Előfeltételek

*   A biztonsági mentési és helyreállítási folyamat végrehajtásához először létre kell hoznia egy Linux virtuális gép, amely rendelkezik az Oracle Database 12c telepített példányával. A virtuális gép létrehozásához használt Marketplace-lemezkép neve *Oracle:Oracle-Database-Ee:12.1.0.2:latest*.

    Az Oracle-adatbázisok létrehozásáról az [Oracle adatbázis-létrehozási rövid útmutatócímű témakörben olvashat.](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create)


### <a name="step-2-connect-to-the-vm"></a>2. lépés: Csatlakozás a virtuális géphez

*   Biztonságos rendszerhéj (SSH) munkamenet létrehozásához a virtuális gép, használja a következő parancsot. Cserélje le az IP-címet és `publicIpAddress` az állomásnév-kombinációt a virtuális gép értékével.

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>3. lépés: Az adatbázis előkészítése

1.  Ez a lépés feltételezi, hogy van egy Oracle példány (cdb1), amely fut a virtuális gép nevű *myVM*.

    Futtassa az *oracle* superuser root-ot, majd inicializálja a figyelőt:

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

2.  (Nem kötelező) Győződjön meg arról, hogy az adatbázis archív napló módban van:

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

4.  A biztonsági másolat helyének és méretének ellenőrzése vagy módosítása:

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```

5. Az adatbázis biztonsági mentése az Oracle Recovery Manager (RMAN) segítségével:

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>4. lépés: Alkalmazáskonzisztens biztonsági mentés Linuxos virtuális gépekhez

Az alkalmazáskonzisztens biztonsági mentések az Azure Backup új szolgáltatása. Létrehozhat és kijelölhet parancsfájlokat a virtuális gép pillanatképe előtt és után (előzetes pillanatkép és pillanatfelvétel utáni).

1. Töltse le a JSON fájlt.

    Letöltés VMSnapshotScriptPluginConfig.json https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfiga ból. A fájl tartalma a következőhöz hasonlóan néz ki:

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

2. Hozza létre az /etc/azure mappát a virtuális gépen:

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. Másolja a JSON-fájlt.

    Másolja a VMSnapshotScriptPluginConfig.json fájlt az /etc/azure mappába.

4. A JSON-fájl szerkesztése.

    A VMSnapshotScriptPluginConfig.json fájl szerkesztése `PreScriptLocation` `PostScriptlocation` a és a paraméterek hez. Például:

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

5. Hozza létre az előzetes pillanatfelvételt és a pillanatfelvételt követő parancsfájlokat.

    Íme egy példa a "hideg biztonsági mentés" (offline biztonsági mentés, leállítás sal és újraindítással) kapcsolatos pillanatfelvétel előtti és utáni parancsfájlokra:

    Az /etc/azure/pre_script.sh esetében:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    Az /etc/azure/post_script.sh esetében:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    Íme egy példa a pillanatkép előtti és a pillanatkép utáni parancsfájlokra egy "gyors biztonsági mentéshez" (online biztonsági mentés):

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Az /etc/azure/post_script.sh esetében:

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    Az /etc/azure/pre_script.sql esetében módosítsa a fájl tartalmát az Ön igényei szerint:

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    Az /etc/azure/post_script.sql esetében módosítsa a fájl tartalmát az Ön igényei szerint:

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. Fájlengedélyek módosítása:

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. Tesztelje a szkripteket.

    A parancsfájlok teszteléséhez először jelentkezzen be gyökérként. Ezután győződjön meg arról, hogy nincsenek hibák:

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

További információ: [Application-konzisztens biztonsági mentés Linux virtuális gépekhez.](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/)


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>5. lépés: Az Azure Recovery Services-tárolók használata a virtuális gép biztonsági mentéséhez

1.  Az Azure Portalon keressen **a Recovery Services-tárolók**között.

    ![Helyreállítási szolgáltatások tárolóinak lapja](./media/oracle-backup-recovery/recovery_service_01.png)

2.  A **Recovery Services-tárolók** panelen új tároló hozzáadásához kattintson a **Hozzáadás**gombra.

    ![A Recovery Services-tárolók hozzáadási lapja](./media/oracle-backup-recovery/recovery_service_02.png)

3.  A folytatáshoz kattintson a **myVault gombra.**

    ![A Helyreállítási szolgáltatások tárolóinak részletes lapja](./media/oracle-backup-recovery/recovery_service_03.png)

4.  A **myVault** panelen kattintson a **Biztonsági mentés**gombra.

    ![A Helyreállítási szolgáltatások tárolóinak biztonsági másolatlapja](./media/oracle-backup-recovery/recovery_service_04.png)

5.  A **Biztonsági másolat célja** panelen használja az **Azure** és a **Virtuális gép**alapértelmezett értékeit. Kattintson az **OK** gombra.

    ![A Helyreállítási szolgáltatások tárolóinak részletes lapja](./media/oracle-backup-recovery/recovery_service_05.png)

6.  A **Biztonsági másolat házirendjéhez**használja a **DefaultPolicy (Alapértelmezett házirend)** lehetőséget, vagy válassza **az Új házirend létrehozása**lehetőséget. Kattintson az **OK** gombra.

    ![A Helyreállítási szolgáltatások tárolóinak biztonsági mentési házirendjének részleteit tartalmazó lap](./media/oracle-backup-recovery/recovery_service_06.png)

7.  A **Virtuális gépek kiválasztása** panelen jelölje be a **myVM1** jelölőnégyzetet, majd kattintson az **OK**gombra. Kattintson a **Biztonsági másolat engedélyezése** gombra.

    ![A Recovery Services a biztonsági mentés részleteit tartalmazó lapra tárolóelemeket tartalmaz](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > Miután a **Biztonsági mentés engedélyezése**gombra kattintott, a biztonsági mentési folyamat nem indul el, amíg az ütemezett időpont le nem jár. Az azonnali biztonsági mentés beállításához hajtsa végre a következő lépést.

8.  A **myVault – Biztonsági mentési elemek** panelen, a **TARTALÉK CIKKDARABszám**csoportban válassza ki a biztonsági mentési elemek számát.

    ![A Helyreállítási szolgáltatások tárolói a myVault részletes oldalát](./media/oracle-backup-recovery/recovery_service_08.png)

9.  A **Biztonsági másolat elemei (Az Azure virtuális gép)** panelen a lap jobb oldalán kattintson a három pont (**...**) gombra, majd a **Biztonsági mentés gombra.**

    ![Helyreállítási szolgáltatások tárolói – Biztonsági mentés most parancs](./media/oracle-backup-recovery/recovery_service_09.png)

10. Kattintson a **Biztonsági másolat** gombra. Várja meg, amíg a biztonsági mentési folyamat befejeződik. Ezután folytassa [a 6.](#step-6-remove-the-database-files)

    A biztonsági mentési feladat állapotának megtekintéséhez kattintson a **Feladatok**gombra.

    ![Recovery Services-tárolók feladatlap](./media/oracle-backup-recovery/recovery_service_10.png)

    A biztonsági mentési feladat állapota a következő képen jelenik meg:

    ![A Recovery Services-tárolók feladatlapja állapottal](./media/oracle-backup-recovery/recovery_service_11.png)

11. Az alkalmazáskonzisztens biztonsági mentéshez a naplófájlban előforduló hibákat orvosolja. A naplófájl a /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0 helyen található.

### <a name="step-6-remove-the-database-files"></a>6. lépés: Az adatbázisfájlok eltávolítása 
A cikk későbbi részében megtudhatja, hogyan tesztelheti a helyreállítási folyamatot. A helyreállítási folyamat tesztelése előtt el kell távolítania az adatbázisfájlokat.

1.  A táblatér és a biztonsági másolat fájljainak eltávolítása:

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  (Nem kötelező) Állítsa le az Oracle példányt:

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>A törölt fájlok visszaállítása a Helyreállítási szolgáltatások tárolóiból
A törölt fájlok visszaállításához hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon keresse meg a *myVault* Recovery Services-tárolók elemet. Az **Áttekintés** panel **Biztonsági másolat elemek csoportban**adja meg az elemek számát.

    ![A Helyreállítási szolgáltatások tárolók myVault biztonsági mentési elemek](./media/oracle-backup-recovery/recovery_service_12.png)

2. A **BIZTONSÁGI MÁSOLAT DARABSZÁMA csoportban**adja meg az elemek számát.

    ![A helyreállítási szolgáltatások tárolói az Azure virtuális gép biztonsági mentési tételszáma](./media/oracle-backup-recovery/recovery_service_13.png)

3. A **myvm1** panelen kattintson a **Fájlhelyreállítás (előzetes verzió) gombra.**

    ![Képernyőkép a Recovery Services-tárolók fájl-helyreállítási lapjáról](./media/oracle-backup-recovery/recovery_service_14.png)

4. A **Fájl-helyreállítási (előnézeti)** ablaktáblán kattintson a **Parancsfájl letöltése**gombra. Ezután mentse a letöltési (.sh) fájlt az ügyfélszámítógép egyik mappájába.

    ![Parancsfájl mentések letöltése](./media/oracle-backup-recovery/recovery_service_15.png)

5. Másolja az .sh fájlt a virtuális gépre.

    A következő példa bemutatja, hogyan használhatja a biztonságos másolat (scp) parancsot a fájl áthelyezéséhez a virtuális gépre. A tartalmat a vágólapra is másolhatja, majd beillesztheti a tartalmat a virtuális gépen beállított új fájlba.

    > [!IMPORTANT]
    > A következő példában győződjön meg arról, hogy frissíti az IP-cím és a mappa értékeit. Az értékeknek ahhoz a mappához kell leképezniük, ahová a fájlt menti.

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```

6. Módosítsa a fájlt úgy, hogy a gyökér tulajdonában legyen.

    A következő példában módosítsa a fájlt úgy, hogy a gyökér tulajdonában legyen. Ezután módosítsa az engedélyeket.

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```

    A következő példa bemutatja, mit kell látni az előző parancsfájl futtatása után. Amikor a program a folytatást kéri, írja be az **Y**értéket.

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

7. A csatlakoztatott kötetekhez való hozzáférés megerősítést nyer.

    A kilépéshez írja be a **q**értéket, majd keresse meg a csatlakoztatott köteteket. A hozzáadott kötetek listájának létrehozásához a parancssorba írja be a **df -k**parancsot.

    ![A df -k parancs](./media/oracle-backup-recovery/recovery_service_16.png)

8. A hiányzó fájlokat a következő parancsfájlsegítségével másolja vissza a mappákba:

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

9. A következő parancsfájlban az RMAN segítségével állíthatja helyre az adatbázist:

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

    Az Azure Portalon a **Fájl-helyreállítási (előzetes verzió)** panelen kattintson **a Lemezek leválasztása gombra.**

    ![Lemezek leválasztása parancs](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>A teljes virtuális gép visszaállítása

Ahelyett, hogy visszaállítané a törölt fájlokat a Recovery Services-tárolókból, visszaállíthatja a teljes virtuális gép.

### <a name="step-1-delete-myvm"></a>1. lépés: MyVM törlése

*   Az Azure Portalon nyissa meg a **myVM1-tárolót,** és válassza **a Törlés lehetőséget.**

    ![A Tároló törlése parancs](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>2. lépés: A virtuális gép helyreállítása

1.  Nyissa meg a **Recovery Services-tárolókat,** és válassza a **myVault**lehetőséget.

    ![myVault bejegyzés](./media/oracle-backup-recovery/recover_vm_02.png)

2.  Az **Áttekintés** panel **Biztonsági másolat elemek csoportban**adja meg az elemek számát.

    ![myVault biztonsági mentés elemekről](./media/oracle-backup-recovery/recover_vm_03.png)

3.  A **Biztonsági másolat elemek (Az Azure virtuális gép)** panelen válassza a **myvm1**lehetőséget.

    ![Helyreállítási virtuális gép lap](./media/oracle-backup-recovery/recover_vm_04.png)

4.  A **myvm1** panelen kattintson a három pont (**...**) gombra, majd a **Virtuális gép visszaállítása gombra.**

    ![Virtuális gép visszaállítása parancs](./media/oracle-backup-recovery/recover_vm_05.png)

5.  A **Visszaállítási pont panelen** jelölje ki a visszaállítani kívánt elemet, majd kattintson az **OK**gombra.

    ![A visszaállítási pont kijelölése](./media/oracle-backup-recovery/recover_vm_06.png)

    Ha engedélyezte az alkalmazáskonzisztens biztonsági mentést, egy függőleges kék sáv jelenik meg.

6.  A **Konfiguráció visszaállítása** panelen jelölje ki a virtuális gép nevét, jelölje ki az erőforráscsoportot, majd kattintson az **OK**gombra.

    ![Konfigurációs értékek visszaállítása](./media/oracle-backup-recovery/recover_vm_07.png)

7.  A virtuális gép visszaállításához kattintson a **Visszaállítás** gombra.

8.  A visszaállítási folyamat állapotának megtekintéséhez kattintson a **Feladatok**, majd a **Feladatok biztonsági másolata**parancsra.

    ![Biztonsági másolat készítése a feladatok állapotának parancsához](./media/oracle-backup-recovery/recover_vm_08.png)

    Az alábbi ábra a visszaállítási folyamat állapotát mutatja be:

    ![A visszaállítási folyamat állapota](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>3. lépés: A nyilvános IP-cím beállítása
A virtuális gép visszaállítása után állítsa be a nyilvános IP-címet.

1.  A keresőmezőbe írja be a **nyilvános IP-címet.**

    ![Nyilvános IP-címek listája](./media/oracle-backup-recovery/create_ip_00.png)

2.  A **Nyilvános IP-címek** panelen kattintson a **Hozzáadás**gombra. A **Nyilvános IP-cím létrehozása** panelen a **Name (Név)** területen válassza ki a nyilvános IP-nevet. Az **Erőforráscsoport** területen válassza a **Meglévő használata** lehetőséget. Ezt követően kattintson a **Create** (Létrehozás) gombra.

    ![IP-cím létrehozása](./media/oracle-backup-recovery/create_ip_01.png)

3.  Ha a nyilvános IP-címet a virtuális gép hálózati adapteréhez szeretné társítani, keresse meg és válassza a **myVMip parancsot.** Ezután kattintson **a Társítás gombra.**

    ![IP-cím társítása](./media/oracle-backup-recovery/create_ip_02.png)

4.  **Az Erőforrás típusnál**válassza a **Hálózati adapter**lehetőséget. Jelölje ki a myVM-példány által használt hálózati adaptert, majd kattintson az **OK**gombra.

    ![Erőforrástípus és hálózati adapterértékek kiválasztása](./media/oracle-backup-recovery/create_ip_03.png)

5.  Keresse meg és nyissa meg a portálról portolt myVM-példányt. A virtuális géphez társított IP-cím megjelenik a myVM **áttekintése** panelen.

    ![IP-cím értéke](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>4. lépés: Csatlakozás a virtuális géphez

*   A virtuális géphez való csatlakozáshoz használja a következő parancsfájlt:

    ```bash
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>5. lépés: Annak vizsgálata, hogy az adatbázis elérhető-e
*   A kisegítő lehetőségek teszteléséhez használja a következő parancsfájlt:

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > Ha az adatbázis **indítási** parancsa hibát generál, az adatbázis helyreállításához olvassa el a [6.](#step-6-optional-use-rman-to-recover-the-database)

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>6. lépés: (Nem kötelező) RMAN használata az adatbázis helyreállításához
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

Az Oracle Database 12c adatbázis biztonsági mentése és helyreállítása egy Azure Linux virtuális gépen befejeződött.

## <a name="delete-the-vm"></a>A virtuális gép törlése

Ha már nincs szüksége a virtuális gépre, a következő paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gépés az összes kapcsolódó erőforrást:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Magas rendelkezésre állású virtuális gépek létrehozása](../../linux/create-cli-complete.md)

[Fedezze fel a virtuális gép üzembe helyezését az Azure CLI-mintákban](../../linux/cli-samples.md)




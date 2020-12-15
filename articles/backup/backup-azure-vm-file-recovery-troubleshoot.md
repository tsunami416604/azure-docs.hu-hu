---
title: Azure-beli virtuális gép fájljának helyreállítása – problémamegoldás
description: A fájlok és mappák Azure-beli virtuális gép biztonsági mentésből való helyreállításával kapcsolatos problémák elhárítása.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 96876415405cc5099b4d9ca209c36086b793d6e0
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97513339"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Az Azure-beli virtuális gépek biztonsági mentésének fájl-helyreállításával kapcsolatos problémák elhárítása

Ez a cikk olyan hibaelhárítási lépéseket tartalmaz, amelyek segíthetnek a fájlok és mappák Azure-beli virtuális gépekről történő helyreállítására vonatkozó problémákkal kapcsolatos hibák elhárításában Azure Backup. 

## <a name="exception-caught-while-connecting-to-target"></a>Kivétel történt a célhoz való csatlakozáskor

Lehetséges ok: a parancsfájl nem tud hozzáférni a helyreállítási pont javasolt műveletéhez: Győződjön meg arról, hogy a gép megfelel-e az összes [hozzáférési követelménynek](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

## <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>A cél már be van jelentkezve iSCSI-munkameneten keresztül

Lehetséges ok: a parancsfájl már végre lett hajtva ugyanazon a gépen, és a meghajtók csatlakoztatva lettek.
Javasolt művelet: a helyreállítási pont kötetei már csatolva vannak. Előfordulhat, hogy a rendszer nem csatlakoztatja az eredeti virtuális gép ugyanazzal a meghajtóbetűjelével. Tallózással keresse meg a Fájlkezelőben elérhető összes kötetet.

## <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Ez a parancsfájl érvénytelen, mert a lemezek a portálon keresztül lettek leválasztva, vagy túllépte a 12 HR-korlátot. Új szkript letöltése a portálról

Lehetséges ok: a lemezek le lettek választva a portálról, vagy túllépte a 12 órás korlátot.
Javasolt művelet: a parancsfájl a letöltéstől számított 12 óra után érvénytelen, és nem hajtható végre. Látogasson el a portálra, és töltsön le egy új parancsfájlt a fájlok helyreállításának folytatásához.

## <a name="troubleshooting-common-issues"></a>Gyakori problémák megoldása

Ez a szakasz a fájl-helyreállításhoz tartozó parancsfájl letöltése és végrehajtása során felmerülő hibák elhárításának lépéseit ismerteti.

## <a name="cannot-download-the-script"></a>A parancsfájl nem tölthető le

Javasolt művelet:

1. Győződjön meg arról, hogy rendelkezik a [parancsfájl letöltéséhez szükséges engedélyekkel](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
2. Ellenőrizze, hogy van-e kapcsolat az Azure Target IP (s) szolgáltatással.
A kapcsolódás ellenőrzéséhez futtassa a következő parancsot egy rendszergazda jogú parancssorból. 
    - *nslookup download.microsoft.com* vagy
    - *download.microsoft.com pingelése*

## <a name="the-script-downloads-successfully-but-fails-to-run"></a>A parancsfájl letöltése sikeresen megtörtént, de a futtatása sikertelen

### <a name="for-sles-12-sp4-os-linux"></a>SLES 12 SP4 operációs rendszerhez (Linux)

Hiba: a iscsi_tcp_module nem található

Lehetséges ok: a Python-szkript az elemszintű helyreállításhoz a SUSE Linux operációs rendszer 12sp4 való futtatása közben meghiúsul, és a hiba ***iscsi_tcp modul nem tölthető be** _. A ILR modul a (z) "iscsi_tcp" protokollt használja a Backup szolgáltatáshoz való TCP-kapcsolat létesítéséhez. a SUSE a 12SP4-kiadás részeként eltávolítva iscsi_tcp a nyílt iSCSI-csomagból, ezért a ILR művelet meghiúsul.

Javasolt művelet: a fájl-helyreállítási parancsfájlok végrehajtása nem támogatott a SUSE 12SP4 virtuális gépeken, próbálja meg a visszaállítási műveletet a SUSE 12SP4 egy régebbi verzióján.

## <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>A parancsfájl fut, de nem sikerült csatlakozni az iSCSI-tárolóhoz

Hiba: a rendszer a célhoz való csatlakozáskor kifogott kivételt észlelt

1. Ellenőrizze, hogy a parancsfájlt futtató gép megfelel-e az összes [hozzáférési követelménynek](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
2. Keresse meg az Azure cél IP-címéhez való kapcsolódást.
A kapcsolódás ellenőrzéséhez futtassa a következő parancsot egy rendszergazda jogú parancssorból. 
    - _nslookup download.microsoft.com * vagy<br>
    - *download.microsoft.com pingelése*
3. Győződjön meg arról, hogy van hozzáférése a 3260-es iSCSI-kimenő porthoz.
4. Győződjön meg arról, hogy nincs olyan tűzfal-vagy NSG, amely blokkolja az Azure-beli IP-címekre vagy a helyreállítási szolgáltatás URL-címeire irányuló forgalmat.
5. Ellenőrizze, hogy a víruskereső blokkolja-e a parancsfájl végrehajtását.

## <a name="connected-to-recovery-point-but-disks-did-not-get-attached"></a>Csatlakoztatva a helyreállítási ponthoz, de a lemezek nem lettek csatolva

Győződjön meg arról, [hogy rendelkezik a megfelelő géppel a szkript futtatásához](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

### <a name="on-windows-vm"></a>Windows rendszerű virtuális gépen

**A virtuális gépen lévő Storage-készlet csak olvasható módban lesz csatolva** A Windows 2012 R2 és a Windows 2016 esetében (a Storage-készlettel együtt) a parancsfájl első futtatásakor a virtuális géphez csatolt tárolási készlet csak olvasható állapotba kerülhet.

A probléma megoldásához manuálisan be kell állítania a Read-Write hozzáférését a tárolóhoz az első alkalommal, és csatolni kell a virtuális lemezeket, az alábbi lépéseket követve:

1. Read-Write hozzáférés beállítása.
Navigáljon a Kiszolgálókezelő > fájl-és tárolási szolgáltatások > kötetek > Storage-készletek elemre.

   ![Windows Storage](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

2. A **tárolási készlet** ablakban kattintson a jobb gombbal a rendelkezésre álló tárolóra, majd válassza a **Read-Write hozzáférés beállítása** lehetőséget.

   ![Windows Storage – olvasási írás](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

3. A Storage-készlet olvasási/írási hozzáféréssel való hozzárendelése után csatolni kell a virtuális lemezt.
Navigáljon a **Kiszolgálókezelő felhasználói felületéhez** a virtuális lemezek szakaszban > kattintson a jobb gombbal a **virtuális lemez csatolása** lehetőség kiválasztásához.

   ![Kiszolgálókezelő virtuális lemez](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

### <a name="on-linux-vm"></a>Linux rendszerű virtuális gépen

#### <a name="file-recovery-fails-to-auto-mount-because-disk-does-not-contain-volumes"></a>A File Recovery nem tud automatikus csatlakoztatást végrehajtani, mert a lemez nem tartalmaz köteteket

A fájlok helyreállítása közben a Backup szolgáltatás észleli a köteteket és az automatikus csatlakoztatásokat. Ha azonban a biztonsági másolatban szereplő lemezek nyers partíciókkal rendelkeznek, akkor ezek a lemezek nem lesznek automatikusan csatlakoztatva, és nem láthatjuk a helyreállításhoz szükséges adatlemezt.

A probléma megoldásához kövesse az ebben a [cikkben](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)ismertetett lépéseket.
 
#### <a name="os-could-not-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>Az operációs rendszer nem tudja azonosítani a Linux-fájlok helyreállításának sikertelenségét okozó fájlrendszert a csatlakoztatások lemezei közben

A File Recovery parancsfájl adatlemezének futtatása közben nem sikerült csatolni az alábbi hibát: *a következő partíciók nem csatlakoztathatók, mert az operációs rendszer nem tudta azonosítani a fájlrendszert*

- A probléma megoldásához ellenőrizze, hogy a kötet titkosítva van-e egy külső gyártótól származó alkalmazással. Titkosítás esetén a lemez vagy a virtuális gép nem jelenik meg titkosítottként a portálon.
1. Jelentkezzen be a biztonsági másolatban szereplő virtuális gépre, és futtassa a következő parancsot: *lsblk-f*<br>

   ![Kötet nélküli lemez](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Ellenőrizze a fájlrendszert és a titkosítást.
3. Ha a kötet titkosítva van, a fájlok helyreállítása nem támogatott. [További információ](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

## <a name="disks-are-attached-but-unable-to-mount-volumes"></a>A lemezek csatlakoztatva vannak, de nem csatlakoztathatók a kötetek

- Győződjön meg arról, [hogy rendelkezik a megfelelő géppel a szkript futtatásához](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="on-windows-vm"></a>Windows rendszerű virtuális gépen

A Windows rendszerhez készült fájl-helyreállítási parancsfájl futtatásakor a ***0 kötet** a következő üzenettel van csatlakoztatva: _, a lemezeket azonban a Lemezkezelés konzolon lehet felderíteni. A kötetek iSCSI-kapcsolaton keresztüli csatlakoztatása közben egyes észlelt kötetek offline állapotba kerülnek. Amikor az iSCSI-csatorna kommunikál a virtuális gép és a szolgáltatás között, észleli ezeket a köteteket, és azokat online állapotba helyezi, de nincs csatlakoztatva.

   ![A lemez nincs csatlakoztatva](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

A probléma azonosításához és megoldásához hajtsa végre a következő lépéseket:

1. A **diskmgmt** parancs futtatásával nyissa meg az _ *Disk Management** parancsot a cmd ablakban.
2. Győződjön meg arról, hogy a rendszer további lemezeket jelenít meg. Az alábbi példában a 2. lemez egy további lemez.

   ![Lemez management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

3. Kattintson a jobb gombbal a **kötetre** , és válassza a meghajtóbetűjel **és elérési utak módosítása** lehetőséget.

   ![Lemez management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

4. A **meghajtóbetűjel vagy elérési út hozzáadása** ablakban válassza **a következő meghajtóbetűjel** kiosztása elemet, és rendeljen hozzá egy elérhető meghajtót, majd kattintson **az OK** gombra. 

   ![Lemez management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

5. A Fájlkezelőben megtekintheti a meghajtót.

   ![Lemez management3](./media/backup-azure-restore-files-from-vm/disk-management-10.png)

6. Az új köteteket csatolni kell.  

   ![A lemez nincs beépítve](./media/backup-azure-restore-files-from-vm/disk-not-mounting-11.png)

7. A Fájlkezelőben az új kötetek a szkript újrafuttatása után lesznek láthatók.

   ![Nem mounting1 lemez](./media/backup-azure-restore-files-from-vm/disk-not-mounting-12.png)

   ![Nem mounting2 lemez](./media/backup-azure-restore-files-from-vm/disk-not-mounting-13.png)

#### <a name="on-linux-vms"></a>Linux rendszerű virtuális gépeken 

- Győződjön meg arról, [hogy rendelkezik a megfelelő géppel a szkript futtatásához](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Ha a védett linuxos virtuális gép LVM és/vagy RAID tömböket használ, kövesse a jelen [cikkben](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)ismertetett lépéseket.

## <a name="cannot-copy-the-files-from-mounted-volumes"></a>A fájlok nem másolhatók a csatlakoztatott kötetekről

A másolás hibával meghiúsulhat a 0x80070780: a rendszer nem fér hozzá a fájlhoz. Ebben az esetben ellenőrizze, hogy a forráskiszolgálón van-e engedélyezve a lemez deduplikálása. Ezt követően győződjön meg arról, hogy a visszaállítási kiszolgáló a meghajtókon is engedélyezte a deduplikálás szolgáltatást. A deduplikáló szerepkört nem lehet úgy konfigurálni, hogy ne törölje a visszaállítási kiszolgáló meghajtóit.

---
title: Azure-beli virtuális gép fájljának helyreállítása – problémamegoldás
description: A fájlok és mappák Azure-beli virtuális gépekről történő helyreállításakor felmerülő problémák elhárítása.
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: bd369577e320cf6dca510183948f41e6cf91779b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605292"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>Az Azure-beli virtuális gépek biztonsági mentésének fájl-helyreállításával kapcsolatos problémák elhárítása

Ez a cikk a fájlok és mappák Azure-beli virtuális gép biztonsági mentésből való helyreállítása során felmerülő problémákkal kapcsolatos hibák elhárításához nyújt segítséget Azure Backup.

## <a name="common-error-messages"></a>Gyakori hibaüzenetek

### <a name="exception-caught-while-connecting-to-target"></a>Kivétel történt a célhoz való csatlakozáskor

**Lehetséges ok**: a parancsfájl nem fér hozzá a helyreállítási ponthoz.

**Javasolt művelet**: Győződjön meg arról, hogy a gép megfelel-e az összes [hozzáférési követelménynek](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>A cél már be van jelentkezve iSCSI-munkameneten keresztül

**Lehetséges ok**: a parancsfájl már végre lett hajtva ugyanazon a gépen, és a meghajtók csatlakoztatva lettek.

**Javasolt művelet**: a helyreállítási pont kötetei már csatolva vannak. Előfordulhat, hogy a rendszer nem csatlakoztatja az eredeti virtuális gép ugyanazzal a meghajtóbetűjelével. Tallózással keresse meg a Fájlkezelőben elérhető összes kötetet.

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>Ez a parancsfájl érvénytelen, mert a lemezek a portálon keresztül lettek leválasztva, vagy túllépte a 12 HR-korlátot. Új szkript letöltése a portálról

**Lehetséges ok**: a lemezek le lettek választva a portálról, vagy túllépte a 12 órás korlátot.

**Javasolt művelet**: a parancsfájl a letöltéstől számított 12 óra után érvénytelen, és nem hajtható végre. Látogasson el a portálra, és töltsön le egy új parancsfájlt a fájl-helyreállítás folytatásához.

## <a name="troubleshooting-common-scenarios"></a>Gyakori hibaelhárítási forgatókönyvek

Ez a szakasz a fájlok helyreállítására szolgáló parancsfájl letöltése és végrehajtása során felmerülő problémák elhárításának lépéseit ismerteti.

### <a name="cant-download-the-script"></a>Nem lehet letölteni a szkriptet

Javasolt művelet:

1. Győződjön meg arról, hogy rendelkezik a [parancsfájl letöltéséhez szükséges engedélyekkel](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script).
1. Ellenőrizze, hogy van-e kapcsolat az Azure Target IP-címekkel

A kapcsolódás ellenőrzéséhez futtassa a következő parancsok egyikét egy rendszergazda jogú parancssorból.

`nslookup download.microsoft.com`

vagy

`ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>A parancsfájl letöltése sikeresen megtörtént, de a futtatása sikertelen

#### <a name="for-sles-12-sp4-os-linux"></a>SLES 12 SP4 operációs rendszerhez (Linux)

**Hiba**: a iscsi_tcp_module nem található

**Lehetséges ok**: az elemszintű helyreállítás (ILR) Python-parancsfájljának futtatása a SUSE Linux operációs rendszer 12sp4, a hiba **_iscsi_tcp modul nem tölthető be_* _.

Az ILR modul az _ *iscsi_tcp** használatával hoz létre TCP-kapcsolatot a biztonsági mentési szolgáltatáshoz. A 12SP4 kiadás részeként a SUSE eltávolította **iscsi_tcpt** a nyílt iSCSI-csomagból, így a ILR művelet sikertelen lesz.

**Javasolt művelet**: a fájl-helyreállítási parancsfájlok végrehajtása nem támogatott a SUSE 12SP4 virtuális gépeken. Próbálja megismételni a visszaállítási műveletet a SUSE 12SP4 egy régebbi verzióján.

### <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>A parancsfájl fut, de nem sikerült csatlakozni az iSCSI-tárolóhoz

Hiba: a rendszer a célhoz való csatlakozáskor kifogott kivételt **észlelt**

1. Ellenőrizze, hogy a parancsfájlt futtató gép megfelel-e az összes [hozzáférési követelménynek](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script).
1. Keresse meg az Azure Target IP-címekhez való kapcsolódást.
A kapcsolódás ellenőrzéséhez futtassa a következő parancsok egyikét egy rendszergazda jogú parancssorból.

   `nslookup download.microsoft.com`

   vagy

   `ping download.microsoft.com`
1. Győződjön meg arról, hogy van hozzáférése a 3260-es iSCSI kimenő porthoz.
1. Győződjön meg arról, hogy nincs olyan tűzfal-vagy NSG, amely blokkolja az Azure-beli IP-címek vagy a helyreállítási szolgáltatás URL-címeinek forgalmát.
1. Ellenőrizze, hogy a víruskereső blokkolja-e a parancsfájl végrehajtását.

### <a name="connected-to-recovery-point-but-disks-didnt-get-attached"></a>Csatlakoztatva a helyreállítási ponthoz, de a lemezek nem lettek csatolva

Győződjön meg arról, [hogy rendelkezik a megfelelő géppel a szkript futtatásához](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

#### <a name="on-a-windows-vm"></a>Windows rendszerű virtuális gépen

A **virtuális gépen lévő tároló csak olvasható módban van csatolva**: Windows 2012 R2 és Windows 2016 rendszeren (Storage-készletekkel), amikor az első alkalommal futtatja a parancsfájlt, előfordulhat, hogy a virtuális géphez csatolt tárolóeszköz írásvédett állapotba kerül.

A probléma megoldásához manuálisan be kell állítania a Read-Write hozzáférését a Storage-készlethez az első alkalommal, és csatolni kell a virtuális lemezeket. Kövesse az alábbi lépéseket:

1. Read-Write hozzáférés beállítása.

   Navigáljon a **Kiszolgálókezelő**  >  **fájl-és tárolási szolgáltatások**  >  **kötetek**  >  **tárolási készletei** közé.

   ![Windows Storage](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. A **tárolási készlet** ablakban kattintson a jobb gombbal a rendelkezésre álló tárolóra, majd válassza a **Read-Write-hozzáférés beállítása** lehetőséget.

   ![Windows Storage – olvasási írás](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. Ha a tárolót olvasási/írási hozzáféréssel rendeli hozzá, csatlakoztassa a virtuális lemezt.

   Navigáljon a **Kiszolgálókezelő felhasználói felületéhez**. A **virtuális lemezek** szakaszban > kattintson a jobb gombbal a **virtuális lemez csatolása** lehetőség kiválasztásához.

   ![Kiszolgálókezelő virtuális lemez](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="on-a-linux-vm"></a>Linux rendszerű virtuális gépen

##### <a name="file-recovery-fails-to-auto-mount-because-disk-doesnt-contain-volumes"></a>A File Recovery nem tud automatikus csatlakoztatást végrehajtani, mert a lemez nem tartalmaz köteteket

A fájlok helyreállítása során a Backup szolgáltatás a köteteket és az automatikus csatlakoztatásokat is észleli. Ha azonban a biztonsági másolatban szereplő lemezek nyers partíciókkal rendelkeznek, akkor ezek a lemezek nem rendelkeznek automatikus csatlakoztatással, és az adatlemez nem látható a helyreállításhoz.

A probléma megoldásához kövesse az ebben a [cikkben](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)ismertetett lépéseket.

##### <a name="the-os-couldnt-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>Az operációs rendszer nem tudta azonosítani a Linux-fájlok helyreállítását okozó fájlrendszert a csatlakoztatások lemezein.

A fájl-helyreállítási parancsfájl futtatásakor az adatlemez nem csatolható a következő hibával:

"A következő partíciók csatlakoztatása sikertelen volt, mert az operációs rendszer nem tudta azonosítani a fájlrendszert:"

A probléma megoldásához ellenőrizze, hogy a kötet titkosítva van-e egy külső gyártótól származó alkalmazással. Ha titkosítva van, akkor a lemez vagy a virtuális gép nem jelenik meg titkosítottként a portálon.

1. Jelentkezzen be a biztonsági másolatba mentett virtuális gépre, és futtassa a következő parancsot:

   `*lsblk -f*`

   ![Kötet nélküli lemez](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. Ellenőrizze a fájlrendszert és a titkosítást.
3. Ha a kötet titkosítva van, akkor a fájl helyreállítása nem támogatott. [További információ](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore).

### <a name="disks-are-attached-but-unable-to-mount-volumes"></a>A lemezek csatlakoztatva vannak, de nem csatlakoztathatók a kötetek

- Győződjön meg arról, [hogy rendelkezik a megfelelő géppel a szkript futtatásához](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

#### <a name="on-windows-vms"></a>Windows rendszerű virtuális gépeken

A Windows rendszerhez készült fájl-helyreállítási parancsfájl futtatásakor van egy üzenet, amely szerint ***0 kötet van csatlakoztatva** _. A lemezek azonban a Lemezkezelés konzolon vannak felderítve. A kötetek iSCSI-kapcsolaton keresztüli csatlakoztatása közben egyes észlelt kötetek offline állapotba kerülnek. Amikor az iSCSI-csatorna kommunikál a virtuális gép és a szolgáltatás között, észleli ezeket a köteteket, és online állapotba helyezi őket, de nincsenek csatlakoztatva.

   ![A lemez nincs csatlakoztatva](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

A probléma azonosításához és megoldásához hajtsa végre a következő lépéseket:

1. A **diskmgmt** parancs futtatásával nyissa meg az _ *Disk Management** parancsot a cmd ablakban.
1. Győződjön meg arról, hogy a rendszer további lemezeket jelenít meg. A következő példában a 2. lemez egy további lemez.

   ![Lemez management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. Kattintson a jobb gombbal az **új kötetre** , és válassza a meghajtóbetűjel **és elérési utak módosítása** lehetőséget.

   ![Lemez management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. A **meghajtóbetűjel vagy elérési út hozzáadása** ablakban válassza **a következő meghajtóbetűjel kiosztása** lehetőséget, és rendeljen hozzá egy elérhető meghajtót, majd kattintson **az OK gombra**.

   ![Lemez management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. A Fájlkezelőben tekintse meg a kiválasztott meghajtóbetűjelet, és vizsgálja meg a fájlokat.

#### <a name="on-linux-vms"></a>Linux rendszerű virtuális gépeken

- Győződjön meg arról, [hogy rendelkezik a megfelelő géppel a szkript futtatásához](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).
- Ha a védett linuxos virtuális gép LVM vagy RAID tömböket használ, kövesse az ebben a [cikkben](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)ismertetett lépéseket.

### <a name="cant-copy-the-files-from-mounted-volumes"></a>A fájlok nem másolhatók a csatlakoztatott kötetekről

A másolás sikertelen lehet a következő **0x80070780: a fájl nem érhető el a rendszeren.** Ebben az esetben ellenőrizze, hogy a forráskiszolgálón van-e engedélyezve a lemez deduplikálása. Ezt követően győződjön meg arról, hogy a visszaállítási kiszolgáló a meghajtókon is engedélyezte a deduplikálás szolgáltatást. A deduplikáló szerepkört nem lehet úgy beállítani, hogy a visszaállítási kiszolgálón lévő meghajtókat ne törölje.

## <a name="next-steps"></a>További lépések

- [Fájlok és mappák helyreállítása az Azure-beli virtuális gépek biztonsági másolatából](backup-azure-restore-files-from-vm.md)

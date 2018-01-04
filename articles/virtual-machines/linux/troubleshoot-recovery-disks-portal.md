---
title: "Használja a Linux virtuális gép hibaelhárítása az Azure portálon |} Microsoft Docs"
description: "Ismerje meg a Linux virtuális gép kapcsolatos problémák elhárítása az operációs rendszer lemezének csatlakozva egy helyreállítási virtuális gép az Azure portál használatával"
services: virtual-machines-linux
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: iainfou
ms.openlocfilehash: c96ff625c3e83f6fc9057f1163c877e8e0aed5e3
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Linux virtuális gép hibaelhárításáról az operációsrendszer-lemez csatolása a helyreállítási virtuális gép az Azure portál használatával
Ha a Linux virtuális gép (VM) rendszerindító vagy a lemez hibát tapasztal, szükség lehet végezze el a virtuális merevlemez hibaelhárítási lépéseket. Ilyenek például a bejegyzés érvénytelen lenne `/etc/fstab` , amely megakadályozza a virtuális gép rendszerindító sikeresen megtörtént. Ez a cikk részletezi az Azure portál segítségével csatlakozzon a virtuális merevlemez egy másik Linux virtuális gép, javítsa ki a hibákat, majd hozza létre újból az eredeti virtuális gép.

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Törölje a virtuális gép problémák észlelése, a virtuális merevlemezek tartása.
2. Csatolja, és csatlakoztassa a virtuális merevlemez egy másik Linux VM hibaelhárítási célból.
3. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Szerkesztheti a fájlokat, vagy futtassa a problémák megoldásával kapcsolatban az eredeti virtuális merevlemez olyan eszközöket.
4. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
5. Az eredeti virtuális merevlemez virtuális gép létrehozása.


## <a name="determine-boot-issues"></a>Határozza meg a rendszerindítási problémák
Ellenőrizze a rendszerindítási diagnosztika és a virtuális gép képernyőkép határozhatja meg, miért nem végezhetnek rendszerindítást megfelelően a virtuális gép. Ilyenek például a bejegyzés érvénytelen lenne `/etc/fstab`, vagy egy alapul szolgáló virtuális merevlemezek áthelyezése vagy törölhetők.

Válassza ki a virtuális Gépet a portálon, és görgessen le a **támogatási + hibaelhárítás** szakasz. Kattintson a **rendszerindítási diagnosztika** a folyamatos átviteli a virtuális gép konzol üzenetek megtekintéséhez. Tekintse át a konzol megjelenítéséhez, ha meghatározhatja, miért a virtuális gép kapcsolatban felmerült problémát. A következő példa bemutatja, hogy a virtuális gépek elakadt a karbantartási módba manuális beavatkozásra van szükség:

![Megtekintés a virtuális gép rendszerindítási diagnosztika konzol naplók](./media/troubleshoot-recovery-disks-portal/boot-diagnostics-error.png)

Is **képernyőkép** a rendszerindítási diagnosztika naplófájl letölteni egy a virtuális gép képernyőkép rögzítése tetején.


## <a name="view-existing-virtual-hard-disk-details"></a>Meglévő virtuális merevlemez részleteinek megtekintése
A virtuális merevlemez egy másik virtuális gép csatolhat, mielőtt kell nevét, a virtuális merevlemez (VHD). 

Válassza ki az erőforráscsoportot a portálról, majd válassza ki a tárfiók. Kattintson a **Blobok**, az alábbi példa szerint:

![Válassza ki a tárolási BLOB](./media/troubleshoot-recovery-disks-portal/storage-account-overview.png)

Általában akkor nevű tárolót **VHD-k** , amely a virtuális merevlemezeket tárolja. Válassza ki a tárolót, hogy a virtuális merevlemezek listájának megtekintése. Jegyezze fel a VHD-t (az előtag általában a virtuális gép neve):

![A tároló virtuális merevlemez azonosítása](./media/troubleshoot-recovery-disks-portal/storage-container.png)

Válassza ki a meglévő virtuális merevlemez a listából, és másolja az URL-címet használja a következő lépésekben:

![Meglévő virtuális merevlemez URL-Címének másolása](./media/troubleshoot-recovery-disks-portal/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Meglévő virtuális gép törlése
A virtuális merevlemezek és a virtuális gépek az Azure-erőforrások két különböző típusa. A virtuális merevlemez, az operációs rendszert illeti, alkalmazások és konfigurációk tárolására. A virtuális gép magát a csak metaadatokat, amelyek a méretét vagy a hely határozza meg, és hivatkozik arra az erőforrások, például egy virtuális merevlemezt vagy virtuális hálózati kártya (NIC). Minden virtuális merevlemez létrehozásakor kell a virtuális Géphez csatlakozik, a címbérlet rendelkezik. Bár az adatlemezek akkor is csatlakoztathatók és leválaszthatók, amikor a virtuális gép üzemel, az operációs rendszer merevlemeze nem csatlakoztatható le, hacsak nem törli a VM-erőforrást. A bérlet továbbra is fennáll, az operációs rendszer lemezének társítandó egy virtuális Gépet, akkor is, ha ezt a virtuális Gépet felszabadított és leállított állapotban van.

Az első lépés a virtuális gép helyreállításához, hogy törli a virtuális gép erőforrásához magát. A virtuális gép törlésével a virtuális merevlemezek a tárfiókban maradnak. A virtuális gép törlését követően a virtuális merevlemez csatlakoztatása egy másik virtuális géphez, és javítsa ki a hibákat.

A virtuális Gépet a portálon, majd kattintson **törlése**:

![Virtuális gép rendszerindítási diagnosztika képernyőfelvétel rendszerindítási hiba](./media/troubleshoot-recovery-disks-portal/stop-delete-vm.png)

Várjon, amíg a virtuális gép törlése a virtuális merevlemez egy másik virtuális géphez csatolása előtt befejeződött. A virtuális merevlemezen, amely a virtuális Gépet társít a címbérlet kell helyezni, előtt a virtuális merevlemez egy másik virtuális géphez.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Meglévő virtuális merevlemez egy másik virtuális géphez csatolása
A következő néhány lépést, a másik virtuális gép a hibaelhárításhoz használja. A meglévő virtuális merevlemez csatlakoztatása a hibaelhárítási virtuális Gépet megtekintheti és szerkesztheti a lemez tartalma. Ez a folyamat teszi javíthatja az esetleges konfigurációs hibákat, vagy tekintse át például további alkalmazás vagy a rendszer naplófájljait. Válassza ki vagy hozzon létre egy másik virtuális Gépet a hibaelhárításhoz használja.

1. Válassza ki az erőforráscsoportot a portálról, majd válassza ki a hibaelhárítási virtuális Gépet. Válassza ki **lemezek** majd **Csatolás meglévő**:

    ![A portál meglévő lemez csatolása](./media/troubleshoot-recovery-disks-portal/attach-existing-disk.png)

2. A meglévő virtuális merevlemez kiválasztásához kattintson a **VHD-fájl** lehetőségre:

    ![Meglévő VHD keresése](./media/troubleshoot-recovery-disks-portal/select-vhd-location.png)

3. A tárfiók és tároló, majd kattintson a meglévő virtuális Merevlemezt. Kattintson a **válasszon** gombra kattintva erősítse meg választását:

    ![Meglévő VHD kiválasztása](./media/troubleshoot-recovery-disks-portal/select-vhd.png)

4. A most kijelölt virtuális merevlemez, és kattintson **OK** csatolni a meglévő virtuális merevlemez:

    ![Ellenőrizze a meglévő virtuális merevlemez csatlakoztatása](./media/troubleshoot-recovery-disks-portal/attach-disk-confirm.png)

5. Néhány másodpercen belül a **lemezek** a virtuális gép ablaktábla listázza a meglévő virtuális merevlemez csatlakoztatva adatlemezt számára:

    ![Adatlemezként csatlakoztatott meglévő virtuális merevlemez](./media/troubleshoot-recovery-disks-portal/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>A csatolt adatlemez csatlakoztatása

> [!NOTE]
> A következő példák részletesen az Ubuntu virtuális gép indításához szükséges lépéseket. Red Hat Enterprise Linux és SUSE, például a különböző Linux distro használata a naplófájl helyét és `mount` parancsok kissé eltérő lehet. Tekintse meg az adott distro a parancsok a megfelelő változásokat a dokumentációját.

1. SSH-kapcsolatot a hibaelhárítási virtuális Gépet a megfelelő hitelesítő adatokkal. Ha ezt a lemezt a a hibaelhárítási virtuális Géphez csatlakozik, első adatlemez, az valószínűleg kapcsolódik `/dev/sdc`. Használjon `dmseg` csatlakoztatott lemezek listázásához:

    ```bash
    dmesg | grep SCSI
    ```
    A kimenet a következő példához hasonló:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    A fenti példában az operációsrendszer-lemezképet jelenleg `/dev/sda` és az egyes virtuális gépek a megadott ideiglenes lemez `/dev/sdb`. Ha több adatlemezek, hogy legyen `/dev/sdd`, `/dev/sde`, és így tovább.

2. Hozzon létre egy könyvtárat, a meglévő virtuális merevlemez csatlakoztatása. Az alábbi példa létrehoz egy könyvtárat nevű `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Ha több partíciót a meglévő virtuális merevlemezen, csatlakoztassa a szükséges partíciót. Az alábbi példa csatlakoztatja, az első elsődleges partíció `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Ajánlott eljárás az adatlemezek csatlakoztatása az Azure-ban univerzálisan egyedi azonosítóját (UUID) a virtuális merevlemez virtuális gépeken. A jelen rövid hibaelhárítási esetben csatlakoztatni a virtuális merevlemez használatával UUID nincs szükség. Azonban a normál használja, a Szerkesztés `/etc/fstab` csatlakoztatni a virtuális merevlemezek UUID helyett eszköznév okozhat a virtuális gépek a rendszerindítás.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Az eredeti virtuális merevlemez kapcsolatos problémák megoldása
A meglévő virtuális merevlemezzel csatlakoztatva is képes lemezvizsgálatok elvégzésére bármely karbantartási és hibaelhárítási lépéseket, igény szerint. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Válassza le a lemezképet, és válassza le az eredeti virtuális merevlemez
Ha a hibák feloldása, válassza le a hibaelhárítási virtuális gépről a meglévő virtuális merevlemez. A virtuális merevlemez nem használható a többi virtuális Géphez, amíg a címbérlet, a virtuális merevlemez csatolását a hibaelhárítási VM.

1. Az SSH-munkamenetet a hibaelhárítási virtuális gépre, a leválasztani a meglévő virtuális merevlemez. Először módosítsa a csatlakoztatási pont szülőkönyvtárában kívül:

    ```bash
    cd /
    ```

    Most leválasztani a meglévő virtuális merevlemez. Az alábbi példa leválasztja az eszköz `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Most válassza le a virtuális merevlemezt a virtuális gépről. Válassza ki a virtuális Gépet a portálon, majd kattintson a **lemezek**. Válasszon a meglévő virtuális merevlemezt, majd kattintson **leválasztási**:

    ![Válassza le a meglévő virtuális merevlemez](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Várjon, amíg a virtuális gép rendelkezik az folytatása előtt a adatlemez leválasztása sikeresen megtörtént.

## <a name="create-vm-from-original-hard-disk"></a>Virtuális gép eredeti merevlemez létrehozása
Egy virtuális Gépet hozhat létre az eredeti virtuális merevlemez [Azure Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). A sablont egy virtuális Gépet létrehozni meglévő virtuális hálózatban, a virtuális merevlemez URL-CÍMÉT a korábbi parancs használatával központilag telepíti. Kattintson a **az Azure telepítéséhez** gombra kattint, az alábbiak szerint:

![A sablont a Githubból a virtuális gép üzembe helyezése](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

A sablon betöltése be az Azure portálon központi telepítéshez. Adja meg a nevet az új virtuális gép és a meglévő Azure-erőforrások, és illessze be a meglévő virtuális merevlemez URL-CÍMÉT. A telepítés megkezdéséhez kattintson **beszerzési**:

![Telepítse a virtuális Gépet sablonból](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Engedélyezze újra a rendszerindítási diagnosztika
Amikor a virtuális Gépet hoz létre a meglévő virtuális merevlemez, rendszerindítási diagnosztika automatikusan nem lehet engedélyezni. Tekintse meg a rendszerindítási diagnosztika, és kapcsolja be, ha szükséges, jelölje be a virtuális Gépet a portálon. A **figyelés**, kattintson a **diagnosztikai beállítások**. Győződjön meg arról, az állapot **a**, és a pipa jelre a **rendszerindítási diagnosztika** van kiválasztva. Ha bármilyen módosításhoz kattintson **mentése**:

![Rendszerindítási diagnosztika beállításainak frissítése](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>További lépések
Ha a virtuális Géphez való kapcsolódás problémát tapasztal, tekintse meg [hibaelhárítása SSH kapcsolatok egy Azure virtuális gépre](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A virtuális gépen futó alkalmazások elérésével problémákkal kapcsolatban lásd: [alkalmazás csatlakozási problémák a Linux virtuális gép](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Erőforrás-kezelő használatával kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

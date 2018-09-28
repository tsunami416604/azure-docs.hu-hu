---
title: Használja a hibaelhárító virtuális Géphez az Azure Portalon Linux |} A Microsoft Docs
description: Ismerje meg az operációsrendszer-lemez egy helyreállítási virtuális Géphez az Azure portal használatával történő csatlakoztatásával Linux rendszerű virtuális gép hibáinak elhárítása
services: virtual-machines-linux
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: genli
ms.openlocfilehash: 817a3465c3e9f5d43bb49e20a443b8cf88293abd
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47413845"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Az operációsrendszer-lemez egy helyreállítási virtuális Géphez az Azure portal használatával történő csatlakoztatásával Linuxos virtuális gépek hibaelhárítása
Ha a Linux rendszerű virtuális gép (VM) indítási vagy hiba fordul, szükség lehet a hibaelhárítási lépések végrehajtásához a virtuális merevlemezen magát. Ilyenek például a bejegyzés érvénytelen lenne `/etc/fstab` , amely megakadályozza, hogy a virtuális gép képes arra, hogy sikeresen. Ez a cikk részletesen bemutatja a virtuális merevlemez csatlakoztatása egy másik Linux rendszerű virtuális géphez, javítsa ki a hibákat, majd hozza létre újból az eredeti virtuális gép az Azure portal használatával.

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Törölje a virtuális gép problémái, így a virtuális merevlemezek.
2. Csatolja, és a egy másik Linux rendszerű virtuális géphez a virtuális merevlemez csatlakoztatása hibaelhárítás céljából.
3. Kapcsolódjon a hibaelhárítást végző virtuális gépre. A fájlok szerkesztésével, vagy minden olyan eszközök futtatásával javítsa problémák az eredeti virtuális merevlemezen.
4. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
5. Hozzon létre egy virtuális Gépet az eredeti virtuális merevlemez használatával.

A virtuális gép felügyelt lemez, amely használja, lásd: [egy felügyelt lemez virtuális Gépet egy új operációsrendszer-lemez csatlakoztatásával végzett hibaelhárítását](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

## <a name="determine-boot-issues"></a>Rendszerindítási problémák meghatározása
Ellenőrizze a rendszerindítási diagnosztika és a virtuális gép képernyőfelvételén meghatározni, miért érdemes a virtuális gép nem áll lehetségesnek megfelelően. Ilyenek például a bejegyzés érvénytelen lenne `/etc/fstab`, vagy az alapjául szolgáló virtuális merevlemez folyamatban törölték vagy áthelyezték.

Válassza ki a virtuális Gépet a portálon, és görgessen lefelé a **támogatás + hibaelhárítás** szakaszban. Kattintson a **rendszerindítási diagnosztika** a konzol üzeneteket, a virtuális gépről streamelt adatok megtekintéséhez. Tekintse át a konzolnaplófájlokban megtekintheti, ha megadhatja, hogy miért érdemes a virtuális gép van hajt végre, amikor egy probléma. Az alábbi példa bemutatja egy virtuális gép elakadt karbantartási módba manuális beavatkozásra van szükség:

![Virtuális gépek megtekintése a rendszerindítási diagnosztika konzol naplójának](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Is **képernyőkép** felső részén a rendszerindítási diagnosztikai napló, töltse le a virtuális gép képernyőfelvételén a rögzítést.


## <a name="view-existing-virtual-hard-disk-details"></a>Meglévő virtuális merevlemez részleteinek megtekintése
A virtuális merevlemez csatlakoztathat egy másik virtuális Géphez, mielőtt a virtuális merevlemez (VHD) nevére azonosítania kell. 

Jelölje ki az erőforráscsoportot a portálon, majd válassza ki a tárfiókját. Kattintson a **Blobok**, ahogy az alábbi példában:

![Válassza ki a storage-blobok](./media/troubleshoot-recovery-disks-portal-linux/storage-account-overview.png)

Általában nevű tárolóban van **VHD-k** , amely a virtuális merevlemezeken tárolja. Válassza ki a tárolót a virtuális merevlemezeket listájának megtekintéséhez. Jegyezze fel a VHD-t (az előtag, általában a virtuális gép neve):

![Virtuális merevlemez, a storage-tároló azonosítása](./media/troubleshoot-recovery-disks-portal-linux/storage-container.png)

Válassza ki a meglévő virtuális merevlemezt a listából, és másolja az URL-cím használható a következő lépésekben:

![Meglévő virtuális merevlemez URL-Címének másolása](./media/troubleshoot-recovery-disks-portal-linux/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Meglévő virtuális gép törlése
A virtuális merevlemezek és a virtuális gépek az Azure-erőforrások két különböző típusa. Virtuális merevlemez, az operációs rendszer magát, alkalmazások és konfigurációk tárolására. A virtuális gépre a csak metaadatokat, amelyek a méretét vagy a hely határozza meg, és az erőforrások, például egy virtuális merevlemezt vagy virtuális hálózati kártya (NIC) hivatkozik. Minden egyes virtuális merevlemezhez egy bérletet, amikor egy virtuális Géphez csatolva van. Bár az adatlemezek akkor is csatlakoztathatók és leválaszthatók, amikor a virtuális gép üzemel, az operációs rendszer merevlemeze nem csatlakoztatható le, hacsak nem törli a VM-erőforrást. A bérlet továbbra is az operációsrendszer-lemez társíthat egy virtuális Gépet, akkor is, ha a virtuális gép leállított vagy felszabadított állapotban van.

Az első lépés a virtuális gép helyreállításához, ami törli magát a VM-erőforrás. A virtuális gép törlésével a virtuális merevlemezek a tárfiókban maradnak. A virtuális gép törlését követően a virtuális merevlemez csatlakoztatása egy másik virtuális géphez a hibák elhárítására.

Válassza ki a virtuális Gépet a portálon, majd kattintson a **törlése**:

![Virtuális gép rendszerindítási diagnosztika képernyőképe a rendszer indítási hibát megjelenítése](./media/troubleshoot-recovery-disks-portal-linux/stop-delete-vm.png)

Várjon, amíg a virtuális gép törlése a virtuális merevlemezt egy másik virtuális géphez csatolása előtt befejeződött. A bérlet társítja azt a virtuális gép a virtuális merevlemezen kell megjelenése előtt a virtuális merevlemezt egy másik virtuális géphez.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Meglévő virtuális merevlemez csatlakoztatása egy másik virtuális géphez
A következő néhány lépést, egy másik virtuális Géphez hibaelhárítás céljából használja. A meglévő virtuális merevlemez csatlakoztatása a hibaelhárító virtuális Géphez, és szerkessze a lemez tartalma tudják. Ez a folyamat lehetővé teszi, hogy javítsa az esetleges konfigurációs hibákat, vagy további alkalmazás vagy a rendszer naplófájljait, például tekintse át. Válassza ki, vagy hozzon létre egy másik virtuális Géphez hibaelhárítás céljából használja.

1. Jelölje ki az erőforráscsoportot a portálon, majd válassza ki a hibaelhárító virtuális Géphez. Válassza ki **lemezek** majd **csatolása meglévő**:

    ![A portál a meglévő lemez csatolása](./media/troubleshoot-recovery-disks-portal-linux/attach-existing-disk.png)

2. A meglévő virtuális merevlemez kiválasztásához kattintson a **VHD-fájl** lehetőségre:

    ![Meglévő VHD keresése](./media/troubleshoot-recovery-disks-portal-linux/select-vhd-location.png)

3. Válassza ki a tárfiókot és tárolót, majd kattintson a meglévő VHD-t. Kattintson a **kiválasztása** gombra kattintva erősítse meg:

    ![Meglévő VHD kiválasztása](./media/troubleshoot-recovery-disks-portal-linux/select-vhd.png)

4. Kattintson a VHD-t bejelölt, **OK** csatolni a meglévő virtuális merevlemezt:

    ![Erősítse meg a meglévő virtuális merevlemez csatlakoztatása](./media/troubleshoot-recovery-disks-portal-linux/attach-disk-confirm.png)

5. Néhány másodperc elteltével a **lemezek** a virtuális gép panel felsorolja a meglévő virtuális merevlemez adatlemez csatlakoztatott:

    ![Adatlemezként csatlakoztatott meglévő virtuális merevlemez](./media/troubleshoot-recovery-disks-portal-linux/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>A csatlakoztatott lemez csatlakoztatása

> [!NOTE]
> Az alábbi példák bemutatják, milyen lépések szükségesek egy Ubuntu virtuális gépen. Ha a különböző Linux-disztribúció, például a Red Hat Enterprise Linux vagy a SUSE, használja a napló fájlhelyekhez és `mount` parancsok kissé eltérő lehet. Tekintse meg a megfelelő parancsokat változásairól az adott disztribúció dokumentációjában.

1. Ssh-KAPCSOLATOT a hibaelhárító virtuális Géphez a megfelelő hitelesítő adatokkal. Ha ezt a lemezt a hibaelhárító virtuális Géphez csatolt első adatlemez, valószínűleg csatlakozik `/dev/sdc`. Használat `dmseg` a csatlakoztatott lemezek listázása:

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

    Az előző példában az operációsrendszer-lemez jelenleg `/dev/sda` és az ideiglenes lemez jelenleg minden virtuális Géphez megadott `/dev/sdb`. Ha több adatlemez, akkor meg kell `/dev/sdd`, `/dev/sde`, és így tovább.

2. Hozzon létre egy könyvtárat a meglévő virtuális merevlemez csatlakoztatása. A következő példában létrehozunk egy nevű könyvtárat `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Ha több partíciót a meglévő virtuális merevlemezen, csatlakoztassa a szükséges partíciót. Az alábbi példa csatlakoztatja, az első elsődleges partíció `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Ajánlott eljárás az adatlemezek csatlakoztatása az Azure-ban a virtuális merevlemez univerzálisan egyedi azonosítót (UUID) használó virtuális gépeken. Ebben a rövid hibaelhárítási forgatókönyvben csatlakoztatja a virtuális merevlemez használatával UUID már nem szükséges. Azonban a normál használat Szerkesztés `/etc/fstab` csatlakoztatni a virtuális merevlemezek az UUID helyett az eszköznevet okozhat a virtuális gép rendszerindítás.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Hárítsa el a problémákat az eredeti virtuális merevlemez
A meglévő virtuális merevlemezzel csatlakoztatva van most már elvégezheti karbantartási és hibaelhárítási lépések, igény szerint. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Válassza le, és az eredeti virtuális merevlemez leválasztása
Miután a hibák megoldódnak, válassza le a hibaelhárító virtuális gépről a meglévő virtuális merevlemezt. Nem használhat a virtuális merevlemez más virtuális gép mindaddig, amíg a virtuális merevlemez csatlakoztatása a hibaelhárító virtuális géphez bérlet.

1. Az SSH-munkamenetből a hibaelhárító virtuális géphez válassza le a meglévő virtuális merevlemezt. Először ki a csatlakoztatási pont szülőkönyvtárában módosítása:

    ```bash
    cd /
    ```

    Most válassza le a meglévő virtuális merevlemezt. Az alábbi példa leválasztja az eszközön lévő `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Most már le a virtuális merevlemezt a virtuális gépről. Válassza ki a virtuális Gépet a portálon, majd kattintson a **lemezek**. Válassza ki a meglévő virtuális merevlemezt, és kattintson a **leválasztási**:

    ![Válassza le a meglévő virtuális merevlemezről](./media/troubleshoot-recovery-disks-portal-linux/detach-disk.png)

    Várjon, amíg a virtuális gép rendelkezik az a folytatás előtt az adatlemez leválasztása sikeresen megtörtént.

## <a name="create-vm-from-original-hard-disk"></a>Virtuális gép létrehozása az eredeti merevlemezről
Egy virtuális Gépet hozhat létre az eredeti virtuális merevlemez [ezen Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). A sablon egy meglévő virtuális hálózatban, a korábbi paranccsal a virtuális merevlemez URL-cím használatával helyez üzembe egy virtuális Gépet. Kattintson a **üzembe helyezés az Azure** gombra az alábbiak szerint:

![Virtuális gép üzembe helyezése a Githubról sablonból](./media/troubleshoot-recovery-disks-portal-linux/deploy-template-from-github.png)

A sablon tölti be az üzembe helyezés az Azure Portalra. Adja meg az új virtuális gép és a meglévő Azure-erőforrások nevét, és illessze be a meglévő virtuális merevlemez URL-CÍMÉT. A telepítés megkezdéséhez kattintson a **beszerzési**:

![Virtuális gép üzembe helyezése sablonból](./media/troubleshoot-recovery-disks-portal-linux/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Engedélyezze újra a rendszerindítási diagnosztika
Amikor a meglévő virtuális merevlemezről hoz létre a virtuális gép, a rendszerindítási diagnosztika lehetséges, hogy nem automatikusan engedélyezni. Ellenőrizze a rendszerindítási diagnosztika állapotát, és kapcsolja be, ha szükséges, válassza ki a virtuális Gépet a portálon. A **figyelés**, kattintson a **diagnosztikai beállítások**. Győződjön meg, hogy a **a**, és a pipa jelre a **rendszerindítási diagnosztika** van kiválasztva. Ha bármilyen módosításhoz kattintson **mentése**:

![Rendszerindítási diagnosztikai beállításainak frissítése](./media/troubleshoot-recovery-disks-portal-linux/reenable-boot-diagnostics.png)

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Egy felügyelt lemez virtuális Gépet egy új operációsrendszer-lemez csatlakoztatásával végzett hibaelhárítását
1. Állítsa az érintett virtuális Gépeket.
2. [Hozzon létre egy felügyelt lemez pillanatképének](../windows/snapshot-copy-managed-disk.md) felügyelt lemezt a virtuális gép operációsrendszer-lemezről.
3. [Felügyelt lemez létrehozása pillanatképből a](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [A felügyelt lemez csatolása a virtuális gép adatlemezként](../windows/attach-disk-ps.md).
5. [Módosítsa az adatlemezt a 4. lépéssel operációsrendszer-lemez](../windows/os-disk-swap.md).

## <a name="next-steps"></a>További lépések
Ha a virtuális Géphez való csatlakozással kapcsolatos problémákat tapasztal, tekintse meg [hibaelhárítása SSH csatlakozhat egy Azure virtuális gép](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A virtuális Gépen futó alkalmazások elérésével kapcsolatos problémák, lásd: [egy Linux rendszerű virtuális gépen alkalmazások csatlakozási hibáinak elhárítása](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Erőforrás-kezelő használatával kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

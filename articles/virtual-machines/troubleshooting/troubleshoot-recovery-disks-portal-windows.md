---
title: Használja a hibaelhárító virtuális Géphez az Azure Portalon Windows |} A Microsoft Docs
description: Ismerje meg, az Azure-beli Windows virtuális gép kapcsolatos problémák elhárítása az operációsrendszer-lemez egy helyreállítási virtuális Géphez az Azure portal használatával történő csatlakoztatásával
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/013/2018
ms.author: genli
ms.openlocfilehash: 2c5fac377dfab4b4c85991dcb8f4e15f4e3cb61a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225929"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Hibaelhárítás Windows virtuális gépek az operációsrendszer-lemez egy helyreállítási virtuális Géphez az Azure portal használatával történő csatlakoztatásával
Ha a Windows virtuális gép (VM) az Azure-ban egy indítási vagy hibát észlel, szükség lehet a hibaelhárítási lépések végrehajtásához a virtuális merevlemezen magát. Ilyenek például egy sikertelen alkalmazásfrissítés megakadályozza, hogy a virtuális gép képes arra, hogy sikeresen lenne. Ez a cikk részletesen bemutatja a virtuális merevlemez csatlakoztatása egy másik Windows virtuális géphez, javítsa ki a hibákat, majd hozza létre újból az eredeti virtuális gép az Azure portal használatával.

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Törölje a virtuális gép problémái, így a virtuális merevlemezek.
2. Csatolja, és a egy másik Windows virtuális géphez a virtuális merevlemez csatlakoztatása hibaelhárítás céljából.
3. Kapcsolódjon a hibaelhárítást végző virtuális gépre. A fájlok szerkesztésével, vagy minden olyan eszközök futtatásával javítsa problémák az eredeti virtuális merevlemezen.
4. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
5. Hozzon létre egy virtuális Gépet az eredeti virtuális merevlemez használatával.

A virtuális gép számára, hogy használja a felügyelt lemez, azt most már használhatja az Azure PowerShell egy virtuális Gépet az operációsrendszer-lemez módosítása. Már nincs szükségünk, törölje és hozza létre újra a virtuális Gépet. További információkért lásd: [hibáinak elhárítása egy Windows virtuális Gépet az operációsrendszer-lemez egy helyreállítási virtuális Géphez az Azure PowerShell használatával történő csatlakoztatásával](troubleshoot-recovery-disks-windows.md).

## <a name="determine-boot-issues"></a>Rendszerindítási problémák meghatározása
Annak megállapításához, hogy miért érdemes a virtuális gép nem áll lehetségesnek megfelelően, vizsgálja meg a rendszerindítási diagnosztika képernyőképe a virtuális gép. Ilyenek például lenne egy sikertelen alkalmazásfrissítés, vagy az alapjául szolgáló virtuális merevlemez folyamatban törölték vagy áthelyezték.

Válassza ki a virtuális Gépet a portálon, és görgessen lefelé a **támogatás + hibaelhárítás** szakaszban. Kattintson a **rendszerindítási diagnosztika** képernyőképen megtekintéséhez. Megjegyzés: minden olyan konkrét hibaüzeneteket vagy hibakódok annak meghatározásához, hogy miért érdemes a virtuális gép van hajt végre, amikor egy probléma. 

![Virtuális gépek megtekintése a rendszerindítási diagnosztika konzol naplójának](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

Is **letölteni a képernyőképet** töltheti le a virtuális gép képernyőfelvételén a rögzítését.

## <a name="view-existing-virtual-hard-disk-details"></a>Meglévő virtuális merevlemez részleteinek megtekintése
A virtuális merevlemez csatlakoztathat egy másik virtuális Géphez, mielőtt a virtuális merevlemez (VHD) nevére azonosítania kell. 

Jelölje ki az erőforráscsoportot a portálon, majd válassza ki a tárfiókját. Kattintson a **Blobok**, ahogy az alábbi példában:

![Válassza ki a storage-blobok](./media/troubleshoot-recovery-disks-portal-windows/storage-account-overview.png)

Általában nevű tárolóban van **VHD-k** , amely a virtuális merevlemezeken tárolja. Válassza ki a tárolót a virtuális merevlemezeket listájának megtekintéséhez. Jegyezze fel a VHD-t (az előtag, általában a virtuális gép neve):

![Virtuális merevlemez, a storage-tároló azonosítása](./media/troubleshoot-recovery-disks-portal-windows/storage-container.png)

Válassza ki a meglévő virtuális merevlemezt a listából, és másolja az URL-cím használható a következő lépésekben:

![Meglévő virtuális merevlemez URL-Címének másolása](./media/troubleshoot-recovery-disks-portal-windows/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Meglévő virtuális gép törlése
A virtuális merevlemezek és a virtuális gépek az Azure-erőforrások két különböző típusa. Virtuális merevlemez, az operációs rendszer magát, alkalmazások és konfigurációk tárolására. A virtuális gépre a csak metaadatokat, amelyek a méretét vagy a hely határozza meg, és az erőforrások, például egy virtuális merevlemezt vagy virtuális hálózati kártya (NIC) hivatkozik. Minden egyes virtuális merevlemezhez egy bérletet, amikor egy virtuális Géphez csatolva van. Bár az adatlemezek akkor is csatlakoztathatók és leválaszthatók, amikor a virtuális gép üzemel, az operációs rendszer merevlemeze nem csatlakoztatható le, hacsak nem törli a VM-erőforrást. A bérlet továbbra is az operációsrendszer-lemez társíthat egy virtuális Gépet, akkor is, ha a virtuális gép leállított vagy felszabadított állapotban van.

Az első lépés a virtuális gép helyreállításához, ami törli magát a VM-erőforrás. A virtuális gép törlésével a virtuális merevlemezek a tárfiókban maradnak. A virtuális gép törlését követően a virtuális merevlemez csatlakoztatása egy másik virtuális géphez a hibák elhárítására.

Válassza ki a virtuális Gépet a portálon, majd kattintson a **törlése**:

![Virtuális gép rendszerindítási diagnosztika képernyőképe a rendszer indítási hibát megjelenítése](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

Várjon, amíg a virtuális gép törlése a virtuális merevlemezt egy másik virtuális géphez csatolása előtt befejeződött. A bérlet társítja azt a virtuális gép a virtuális merevlemezen kell megjelenése előtt a virtuális merevlemezt egy másik virtuális géphez.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Meglévő virtuális merevlemez csatlakoztatása egy másik virtuális géphez
A következő néhány lépést, egy másik virtuális Géphez hibaelhárítás céljából használja. A meglévő virtuális merevlemez csatlakoztatása a hibaelhárító virtuális Géphez, és szerkessze a lemez tartalma tudják. Ez a folyamat lehetővé teszi, hogy javítsa az esetleges konfigurációs hibákat, vagy további alkalmazás vagy a rendszer naplófájljait, például tekintse át. Válassza ki, vagy hozzon létre egy másik virtuális Géphez hibaelhárítás céljából használja.

1. Jelölje ki az erőforráscsoportot a portálon, majd válassza ki a hibaelhárító virtuális Géphez. Válassza ki **lemezek** majd **csatolása meglévő**:

    ![A portál a meglévő lemez csatolása](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. A meglévő virtuális merevlemez kiválasztásához kattintson a **VHD-fájl** lehetőségre:

    ![Meglévő VHD keresése](./media/troubleshoot-recovery-disks-portal-windows/select-vhd-location.png)

3. Válassza ki a tárfiókot és tárolót, majd kattintson a meglévő VHD-t. Kattintson a **kiválasztása** gombra kattintva erősítse meg:

    ![Meglévő VHD kiválasztása](./media/troubleshoot-recovery-disks-portal-windows/select-vhd.png)

4. Kattintson a VHD-t bejelölt, **OK** csatolni a meglévő virtuális merevlemezt:

    ![Erősítse meg a meglévő virtuális merevlemez csatlakoztatása](./media/troubleshoot-recovery-disks-portal-windows/attach-disk-confirm.png)

5. Néhány másodperc elteltével a **lemezek** a virtuális gép panel felsorolja a meglévő virtuális merevlemez adatlemez csatlakoztatott:

    ![Adatlemezként csatlakoztatott meglévő virtuális merevlemez](./media/troubleshoot-recovery-disks-portal-windows/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>A csatlakoztatott lemez csatlakoztatása

1. Nyissa meg a virtuális géphez távoli asztali kapcsolatot. Válassza ki a virtuális Gépet a portálon, majd kattintson a **Connect**. Töltse le és nyissa meg az RDP-kapcsolat fájlt. Adja meg a bejelentkezési adatait, jelentkezzen be a virtuális gépre a következő:

    ![Jelentkezzen be a virtuális gép távoli asztali kapcsolattal](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. Nyissa meg **Kiszolgálókezelő**, majd **fájl- és tárolási szolgáltatások**. 

    ![Válassza ki a fájl- és tárolási szolgáltatások Kiszolgálókezelőben](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Az adatlemez automatikusan észlelte és csatolva. A csatlakoztatott lemezek listájának megtekintéséhez válasszon **lemezek**. Kiválaszthatja az adatlemezt mennyiségi információk, beleértve a meghajtó betűjeléhez megtekintéséhez. Az alábbi példa bemutatja az adatlemezt csatolni, és használatával **F:**:

    ![Csatlakoztatott lemez és a rendszerkötet-információkat a Kiszolgálókezelőben](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Hárítsa el a problémákat az eredeti virtuális merevlemez
A meglévő virtuális merevlemezzel csatlakoztatva van most már elvégezheti karbantartási és hibaelhárítási lépések, igény szerint. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Válassza le, és az eredeti virtuális merevlemez leválasztása
Miután a hibák megoldódnak, válassza le a hibaelhárító virtuális gépről a meglévő virtuális merevlemezt. Nem használhat a virtuális merevlemez más virtuális gép mindaddig, amíg a virtuális merevlemez csatlakoztatása a hibaelhárító virtuális géphez bérlet.

1. A virtuális géphez RDP-munkamenetet, nyissa meg a **Kiszolgálókezelő**, majd **fájl- és tárolási szolgáltatások**:

    ![Válassza a Kiszolgálókezelő fájl- és tárolási szolgáltatások](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Válassza ki **lemezek** , és válassza ki az adatlemezt. Kattintson a jobb gombbal az adatokat lemezen, és válassza ki **offline állapotba helyezés**:

    ![Az adatlemez állítja be a Kiszolgálókezelő offline](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Most már le a virtuális merevlemezt a virtuális gépről. Válassza ki a virtuális gép az Azure Portalon, és kattintson a **lemezek**. Válassza ki a meglévő virtuális merevlemezt, és kattintson a **leválasztási**:

    ![Válassza le a meglévő virtuális merevlemezről](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Várjon, amíg a virtuális gép rendelkezik az a folytatás előtt az adatlemez leválasztása sikeresen megtörtént.

## <a name="create-vm-from-original-hard-disk"></a>Virtuális gép létrehozása az eredeti merevlemezről
Egy virtuális Gépet hozhat létre az eredeti virtuális merevlemez [ezen Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet). A sablon virtuális hálózatban a meglévő vagy új, a korábbi paranccsal a virtuális merevlemez URL-cím használatával helyez üzembe egy virtuális Gépet. Kattintson a **üzembe helyezés az Azure** gombra az alábbiak szerint:

![Virtuális gép üzembe helyezése a Githubról sablonból](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

A sablon tölti be az üzembe helyezés az Azure Portalra. Adja meg az új virtuális gép és a meglévő Azure-erőforrások nevét, és illessze be a meglévő virtuális merevlemez URL-CÍMÉT. A telepítés megkezdéséhez kattintson a **beszerzési**:

![Virtuális gép üzembe helyezése sablonból](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Engedélyezze újra a rendszerindítási diagnosztika
Amikor a meglévő virtuális merevlemezről hoz létre a virtuális gép, a rendszerindítási diagnosztika lehetséges, hogy nem automatikusan engedélyezni. Ellenőrizze a rendszerindítási diagnosztika állapotát, és kapcsolja be, ha szükséges, válassza ki a virtuális Gépet a portálon. A **figyelés**, kattintson a **diagnosztikai beállítások**. Győződjön meg, hogy a **a**, és a pipa jelre a **rendszerindítási diagnosztika** van kiválasztva. Ha bármilyen módosításhoz kattintson **mentése**:

![Rendszerindítási diagnosztikai beállításainak frissítése](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>További lépések
Ha a virtuális Géphez való csatlakozással kapcsolatos problémákat tapasztal, tekintse meg [hibaelhárítása RDP-kapcsolatai egy Azure virtuális gép](troubleshoot-rdp-connection.md). A virtuális Gépen futó alkalmazások elérésével kapcsolatos problémák, lásd: [a Windows virtuális gép alkalmazások csatlakozási hibáinak elhárítása](troubleshoot-app-connection.md).

Erőforrás-kezelő használatával kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md).


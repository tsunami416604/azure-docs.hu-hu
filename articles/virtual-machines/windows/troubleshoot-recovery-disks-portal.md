---
title: "Használjon egy Windows virtuális gép hibaelhárítása az Azure portálon |} Microsoft Docs"
description: "Az operációs rendszer lemezének csatlakozva egy helyreállítási virtuális gépet az Azure-portálon hibaelhárítása a Windows virtuális gépekkel kapcsolatos problémákat, az Azure-ban"
services: virtual-machines-windows
documentationCenter: 
authors: genlin
manager: timlt
editor: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: c1ccd165f9652a8e5e96652937667add6825db8c
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>A Windows virtuális gépek hibaelhárításáról az operációsrendszer-lemez csatolása a helyreállítási virtuális gép az Azure portál használatával
Ha a Windows rendszerű virtuális gép (VM) az Azure-ban rendszerindító vagy a lemez hibát tapasztal, szükség lehet végezze el a virtuális merevlemez hibaelhárítási lépéseket. Ilyenek például a sikertelen frissítés, amely megakadályozza a virtuális gép sikeresen rendszerindító lenne. Ez a cikk részletezi az Azure portál segítségével csatlakozzon a virtuális merevlemez egy másik Windows virtuális gépre, javítsa ki a hibákat, majd hozza létre újból az eredeti virtuális gép.

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Törölje a virtuális gép problémák észlelése, a virtuális merevlemezek tartása.
2. Csatolja, és csatlakoztassa a virtuális merevlemez egy másik Windows VM hibaelhárítási célból.
3. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Szerkesztheti a fájlokat, vagy futtassa a problémák megoldásával kapcsolatban az eredeti virtuális merevlemez olyan eszközöket.
4. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
5. Az eredeti virtuális merevlemez virtuális gép létrehozása.


## <a name="determine-boot-issues"></a>Határozza meg a rendszerindítási problémák
Annak megállapítása, miért a virtuális gép nem végezhetnek rendszerindítást megfelelően, vizsgálja meg a rendszerindítási diagnosztika Képernyőkép a virtuális gép. Ilyenek például a sikertelen frissítés, vagy egy alapul szolgáló virtuális merevlemezek áthelyezése vagy törölhetők lenne.

Válassza ki a virtuális Gépet a portálon, és görgessen le a **támogatási + hibaelhárítás** szakasz. Kattintson a **rendszerindítási diagnosztika** a képernyőkép megtekintéséhez. Megjegyzés: a vonatkozó hibaüzeneteket vagy hibakódok annak meghatározásához, hogy a virtuális gép miért kapcsolatban felmerült problémát. A következő példa bemutatja a virtuális gépek vár a szolgáltatások leállítása:

![Megtekintés a virtuális gép rendszerindítási diagnosztika konzol naplók](./media/troubleshoot-recovery-disks-portal/screenshot-error.png)

Is **képernyőkép** töltheti le a virtuális gép képernyőfelvétel egy rögzítését.


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

1. Nyissa meg a távoli asztali kapcsolatot a virtuális Gépet. Válassza ki a virtuális Gépet a portálon, majd kattintson a **Connect**. Töltse le és nyissa meg az RDP-kapcsolat fájlt. Adja meg a hitelesítő adatokat jelentkezzen be a virtuális géphez az alábbiak szerint:

    ![Jelentkezzen be a virtuális gép távoli asztali kapcsolattal](./media/troubleshoot-recovery-disks-portal/open-remote-desktop.png)

2. Nyissa meg **Kiszolgálókezelő**, majd jelölje be **fájl- és tárolási szolgáltatások**. 

    ![Válassza ki a fájl- és tárolási szolgáltatások Kiszolgálókezelőben](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

3. Az adatlemez automatikusan észlelt és csatolva. A csatlakoztatott lemezek listájának megtekintéséhez válasszon **lemezek**. Kiválaszthatja a adatlemez kötet információk, beleértve a meghajtóbetűjelet. A következő példa bemutatja a adatlemezt csatolni, és segítségével **F:**:

    ![Csatlakoztatott lemez és kötet információkat a Kiszolgálókezelőben](./media/troubleshoot-recovery-disks-portal/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Az eredeti virtuális merevlemez kapcsolatos problémák megoldása
A meglévő virtuális merevlemezzel csatlakoztatva is képes lemezvizsgálatok elvégzésére bármely karbantartási és hibaelhárítási lépéseket, igény szerint. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Válassza le a lemezképet, és válassza le az eredeti virtuális merevlemez
Ha a hibák feloldása, válassza le a hibaelhárítási virtuális gépről a meglévő virtuális merevlemez. A virtuális merevlemez nem használható a többi virtuális Géphez, amíg a címbérlet, a virtuális merevlemez csatolását a hibaelhárítási VM.

1. Az a virtuális gép RDP-munkamenetet, nyissa meg a **Kiszolgálókezelő**, majd jelölje be **fájl- és tárolási szolgáltatások**:

    ![Válassza a Kiszolgálókezelő fájl- és tárolási szolgáltatások](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

2. Válassza ki **lemezek** , és válassza ki az adatlemezt. Kattintson a jobb gombbal a adatok lemezen, és válassza ki **kapcsolat nélküli üzemmódra állítás**:

    ![Az adatlemez állítja be a Kiszolgálókezelő offline állapotú](./media/troubleshoot-recovery-disks-portal/server-manager-set-disk-offline.png)

3. Most válassza le a virtuális merevlemezt a virtuális gépről. Válassza ki a virtuális Gépet az Azure portálon, és kattintson a **lemezek**. Válasszon a meglévő virtuális merevlemezt, majd kattintson **leválasztási**:

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

## <a name="next-steps"></a>Következő lépések
Ha a virtuális Géphez való kapcsolódás problémát tapasztal, tekintse meg [hibáinak elhárítása az RDP-kapcsolatok egy Azure virtuális gépre](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A virtuális gépen futó alkalmazások elérésével problémákkal kapcsolatban lásd: [alkalmazás csatlakozási problémák a Windows virtuális gép](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Erőforrás-kezelő használatával kapcsolatos további információkért lásd: [Azure Resource Manager áttekintése](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

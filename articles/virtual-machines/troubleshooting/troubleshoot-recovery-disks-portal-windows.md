---
title: Windows hibaelhárítást használó virtuális gép használata a Azure Portalban | Microsoft Docs
description: A Windows rendszerű virtuális gépekkel kapcsolatos hibák elhárítása az Azure-ban az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatlakoztatásával a Azure Portal használatával
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/13/2018
ms.author: genli
ms.openlocfilehash: 8ab6fc75475cd99e3d803450476880175f12d2b6
ms.sourcegitcommit: 1b7b0e1c915f586a906c33d7315a5dc7050a2f34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67881137"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Windows rendszerű virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez csatolásával a Azure Portal használatával
Ha az Azure-beli Windows rendszerű virtuális gép rendszerindítási vagy lemezhiba-hibát észlel, előfordulhat, hogy hibaelhárítási lépéseket kell végrehajtania a virtuális merevlemezen. Gyakori példa egy sikertelen alkalmazás frissítése, amely megakadályozza, hogy a virtuális gép sikeresen elinduljon. Ez a cikk részletesen ismerteti, hogyan lehet a Azure Portal használatával összekapcsolni a virtuális merevlemezt egy másik Windowsos virtuális géppel a hibák kijavítása érdekében, majd újból létre kell hoznia az eredeti virtuális gépet. 

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Törölje a problémát észlelő virtuális GÉPET, és őrizze meg a virtuális merevlemezeket.
2. Hibaelhárítási célból csatlakoztassa és csatlakoztassa a virtuális merevlemezt egy másik Windows rendszerű virtuális géphez.
3. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Szerkessze a fájlokat, vagy futtasson eszközöket az eredeti virtuális merevlemezen található problémák megoldásához.
4. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
5. Hozzon létre egy virtuális GÉPET az eredeti virtuális merevlemez használatával.

A felügyelt lemezt használó virtuális gépek esetében mostantól a Azure PowerShell segítségével módosíthatja egy virtuális gép operációsrendszer-lemezét. Már nem kell törölnie és újra létrehozni a virtuális gépet. További információ: Windows rendszerű [virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatolásával Azure PowerShell használatával](troubleshoot-recovery-disks-windows.md).

> [!NOTE]
> Ez a cikk nem vonatkozik a nem felügyelt lemezzel rendelkező virtuális gépre.

## <a name="determine-boot-issues"></a>Rendszerindítási problémák meghatározása
Annak megállapításához, hogy a virtuális gép miért nem indítható el megfelelően, vizsgálja meg a rendszerindítási diagnosztikai virtuális gép képernyőképét. Gyakori példa az alkalmazás sikertelen frissítése vagy egy mögöttes virtuális merevlemez törlése vagy áthelyezése.

Válassza ki a virtuális gépet a portálon, majd görgessen le a **támogatás + hibaelhárítás** szakaszhoz. A képernyőkép megtekintéséhez kattintson a rendszerindítási **diagnosztika** elemre. Jegyezze fel az adott hibaüzeneteket vagy hibakódokat, hogy megtudja, miért jelentkezik a virtuális gép egy problémával.

![VIRTUÁLIS gépek rendszerindítási diagnosztikai konzoljának naplófájljainak megtekintése](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

A képernyőfelvétel **letöltése** lehetőségre kattintva letöltheti a virtuális gép képernyőképét.

## <a name="view-existing-virtual-hard-disk-details"></a>A virtuális merevlemez meglévő adatainak megtekintése
Ahhoz, hogy a virtuális merevlemezt egy másik virtuális GÉPHEZ csatolja, azonosítania kell a virtuális merevlemez (VHD) nevét.

Válassza ki a problémát okozó virtuális gépet, majd válassza a **lemezek**lehetőséget. Jegyezze fel az operációsrendszer-lemez nevét az alábbi példában látható módon:

![Storage-Blobok kiválasztása](./media/troubleshoot-recovery-disks-portal-windows/view-disk.png)

## <a name="delete-existing-vm"></a>Meglévő virtuális gép törlése
A virtuális merevlemezek és a virtuális gépek az Azure-erőforrások két különböző típusa. A virtuális merevlemez az a hely, ahol maga az operációs rendszer, az alkalmazások és a konfigurációk tárolódnak. Maga a virtuális gép csak olyan metaadatokat tartalmaz, amelyek meghatározzák a méretet vagy a helyet, és olyan erőforrásokra hivatkoznak, mint például a virtuális merevlemez vagy a virtuális hálózati kártya (NIC). Minden virtuális merevlemezhez hozzá van rendelve egy, a virtuális GÉPHEZ csatolt bérlet. Bár az adatlemezek akkor is csatlakoztathatók és leválaszthatók, amikor a virtuális gép üzemel, az operációs rendszer merevlemeze nem csatlakoztatható le, hacsak nem törli a VM-erőforrást. A bérlet továbbra is társítja az operációsrendszer-lemezt egy virtuális géppel, még akkor is, ha a virtuális gép leállított és felszabadítási állapotban van.

A virtuális gép helyreállításának első lépése a virtuálisgép-erőforrás törlése. A virtuális gép törlésével a virtuális merevlemezek a tárfiókban maradnak. A virtuális gép törlése után csatolja a virtuális merevlemezt egy másik virtuális géphez a hibák elhárítása és megoldása érdekében.

Válassza ki a virtuális gépet a portálon, majd kattintson a **Törlés**gombra:

![Rendszerindítási hibát mutató VM rendszerindítási diagnosztika képernyőképe](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

Várjon, amíg a virtuális gép el nem végezte a törlést, mielőtt csatlakoztatja a virtuális merevlemezt egy másik virtuális GÉPHEZ. Ahhoz, hogy a virtuális merevlemezt egy másik virtuális GÉPHEZ csatolja, a virtuális merevlemezhez társítani kívánt bérletet fel kell szabadítani.

## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Meglévő virtuális merevlemez csatolása egy másik virtuális GÉPHEZ
A következő néhány lépésben hibaelhárítási célból egy másik virtuális gépet használ. A meglévő virtuális merevlemezt csatolja ehhez a hibaelhárítási virtuális géphez a lemez tartalmának tallózásához és szerkesztéséhez. Ez a folyamat lehetővé teszi a konfigurációs hibák kijavítását, vagy a további alkalmazás-vagy rendszernapló-fájlok áttekintését, például:. Válasszon ki vagy hozzon létre egy másik virtuális gépet, amelyet hibaelhárítási célokra használhat.

1. Válassza ki az erőforráscsoportot a portálon, majd válassza ki a hibaelhárítási virtuális gépet. Válassza a **lemezek**lehetőséget, válassza a **Szerkesztés**lehetőséget, majd kattintson **az adatlemez hozzáadása**elemre:

    ![Meglévő lemez csatolása a portálon](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Az adatlemezek listán válassza ki az Ön által azonosított virtuális gép operációsrendszer-lemezét. Ha nem látja az operációsrendszer-lemezt, ellenőrizze, hogy a virtuális gép és az operációs rendszer lemeze ugyanabban a régióban van-e (hely).
3. A módosítások alkalmazásához válassza a **Mentés** lehetőséget.

## <a name="mount-the-attached-data-disk"></a>A csatlakoztatott adatlemez csatlakoztatása

1. Nyisson meg egy Távoli asztal-kapcsolódást a virtuális géphez. Válassza ki a virtuális gépet a portálon, és kattintson a **kapcsolat**elemre. Töltse le és nyissa meg az RDP-kapcsolat fájlját. Adja meg a hitelesítő adatait a virtuális gépre való bejelentkezéshez a következőképpen:

    ![Jelentkezzen be a virtuális gépre a Távoli asztal használatával](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. Nyissa meg a **Kiszolgálókezelő alkalmazást**, majd válassza a **fájl-és tárolási szolgáltatások**lehetőséget. 

    ![Fájl-és tárolási szolgáltatások kiválasztása a Kiszolgálókezelő programban](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. A rendszer automatikusan észleli és csatolja az adatlemezt. A csatlakoztatott lemezek listájának megtekintéséhez válassza a **lemezek**lehetőséget. Az adatlemez kiválasztásával megtekintheti a kötetek adatait, beleértve a meghajtóbetűjelet is. Az alábbi példa az **F:** csatolt adatlemezt mutatja be és használja:

    ![Lemez csatolt és kötetének adatai a Kiszolgálókezelőben](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Hibák elhárítása az eredeti virtuális merevlemezen
A meglévő virtuális merevlemez csatlakoztatása után a szükséges karbantartási és hibaelhárítási lépéseket is elvégezheti. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Eredeti virtuális merevlemez leválasztása és leválasztása
A hibák megoldása után válassza le a meglévő virtuális merevlemezt a hibaelhárítási virtuális gépről. A virtuális merevlemezt nem használhatja más virtuális géppel, amíg a virtuális merevlemezt a hibaelhárítási virtuális géphez csatlakoztató címbérlet megjelent.

1. Az RDP-munkamenetből a virtuális gépre nyissa meg a **Kiszolgálókezelő alkalmazást**, majd válassza a **fájl-és tárolási szolgáltatások**elemet:

    ![Fájl-és tárolási szolgáltatások kiválasztása a Kiszolgálókezelőben](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Válassza a **lemezek** lehetőséget, majd válassza ki az adatlemezt. Kattintson a jobb gombbal az adatlemezre, és válassza az **offline állapotba**állítás lehetőséget:

    ![Az adatlemez offline állapotba állítása a Kiszolgálókezelőben](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Most válassza le a virtuális merevlemezt a virtuális gépről. Válassza ki a virtuális gépet a Azure Portalon, majd kattintson a **lemezek**elemre. 
4. Válassza a **Szerkesztés**lehetőséget, válassza ki a csatlakoztatott operációsrendszer-lemezt, majd kattintson a Leválasztás elemre:

    ![Meglévő virtuális merevlemez leválasztása](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Várjon, amíg a virtuális gép sikeresen leválasztott egy adatlemezt a folytatás előtt.

## <a name="create-vm-from-original-hard-disk"></a>Virtuális gép létrehozása az eredeti merevlemezből

### <a name="method-1-use-azure-resource-manager-template"></a>1\. módszer Azure Resource Manager sablon használata
Ha az eredeti virtuális merevlemezről szeretne virtuális GÉPET létrehozni, használja [ezt a Azure Resource Manager sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet). A sablon egy meglévő vagy új virtuális hálózatba helyez üzembe egy virtuális gépet, a korábbi parancs VHD URL-címének használatával. Kattintson az **üzembe helyezés az Azure-** ban gombra a következőképpen:

![Virtuális gép üzembe helyezése sablonból a GitHubról](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

A sablon betöltődik a Azure Portalba a telepítéshez. Adja meg az új virtuális gép és a meglévő Azure-erőforrások nevét, majd illessze be az URL-címet a meglévő virtuális merevlemezre. A telepítés megkezdéséhez kattintson a **vásárlás**gombra:

![Virtuális gép üzembe helyezése sablonból](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)

### <a name="method-2-create-a-vm-from-the-disk"></a>2\. módszer: virtuális gép létrehozása a lemezről

1. A Azure Portal válassza ki az erőforráscsoportot a portálon, majd keresse meg az operációsrendszer-lemezt. A lemezt a lemez neve alapján is megkeresheti:

    ![Lemez keresése Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/search-disk.png)
1. Válassza az **Áttekintés**lehetőséget, majd válassza a **virtuális gép létrehozása**lehetőséget.
    ![Virtuális gép létrehozása lemezről Azure Portalból](./media/troubleshoot-recovery-disks-portal-windows/create-vm-from-disk.png)
1. A virtuális gép létrehozásához kövesse a varázslót.

## <a name="re-enable-boot-diagnostics"></a>Rendszerindítási diagnosztika újbóli engedélyezése
Ha a virtuális GÉPET a meglévő virtuális merevlemezről hozza létre, előfordulhat, hogy a rendszerindítási diagnosztika nem lesz automatikusan engedélyezve. A rendszerindítási diagnosztika állapotának vizsgálatához és szükség esetén bekapcsolásához válassza ki a virtuális gépet a portálon. A **figyelés**területen kattintson a **diagnosztikai beállítások**elemre. Győződjön meg arról, hogy az állapot **be van kapcsolva**, és a rendszerindítási **diagnosztika** melletti pipa van kiválasztva. Ha bármilyen változást hajt végre, kattintson a **Mentés**gombra:

![Rendszerindítási diagnosztika beállításainak frissítése](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>További lépések
Ha problémába ütközik a virtuális géphez való csatlakozással kapcsolatban, tekintse meg [az RDP-kapcsolatok hibaelhárítása Azure-beli virtuális géppel](troubleshoot-rdp-connection.md)című témakört. A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákért lásd: az [alkalmazások kapcsolódási problémáinak elhárítása Windows rendszerű virtuális gépen](troubleshoot-app-connection.md).

További információ a Resource Manager használatáról: [Azure Resource Manager Overview (áttekintés](../../azure-resource-manager/resource-group-overview.md)).


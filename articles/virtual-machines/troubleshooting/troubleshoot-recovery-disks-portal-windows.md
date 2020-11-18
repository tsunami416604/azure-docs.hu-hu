---
title: Windows rendszerű virtuális gép hibáinak megoldása a Azure Portalban | Microsoft Docs
description: Ismerje meg, hogyan oldhatja meg a Windows rendszerű virtuális gépekkel kapcsolatos problémákat az Azure-ban az operációsrendszer-lemez egy helyreállítási virtuális géphez való összekapcsolásával a Azure Portal.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: 04adf3903cd925f4507e94347251c762a576ff93
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94735228"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-through-the-azure-portal"></a>Windows rendszerű virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatolásával a Azure Portal
Ha az Azure-beli Windows rendszerű virtuális gép indítási vagy lemezhiba-hibát észlel, lehetséges, hogy hibaelhárítási lépéseket kell végrehajtania a virtuális merevlemezen (VHD). Gyakori példa egy sikertelen alkalmazás frissítése, amely megakadályozza, hogy a virtuális gép sikeresen elinduljon. Ez a cikk részletesen ismerteti, hogyan lehet a Azure Portal használatával összekapcsolni a virtuális merevlemezt egy másik Windowsos virtuális géppel a hibák kijavítása érdekében, majd újból létre kell hoznia az eredeti virtuális gépet. 

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Állítsa le az érintett virtuális gépet.
1. Hozzon létre egy pillanatképet a virtuális gép operációsrendszer-lemezéhez.
1. Hozzon létre egy virtuális merevlemezt a pillanatképből.
1. Hibaelhárítási célból csatlakoztassa és csatlakoztassa a virtuális merevlemezt egy másik Windows rendszerű virtuális géphez.
1. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Szerkessze a fájlokat, vagy futtasson eszközöket az eredeti virtuális merevlemezen felmerülő problémák elhárításához.
1. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
1. Cserélje le a virtuális gép operációsrendszer-lemezét.

> [!NOTE]
> Ez a cikk nem vonatkozik a nem felügyelt lemezekkel rendelkező virtuális gépekre.

## <a name="take-a-snapshot-of-the-os-disk"></a>Pillanatkép készítése az operációsrendszer-lemezről
A pillanatkép egy virtuális merevlemez teljes, írásvédett másolata. Javasoljuk, hogy a pillanatkép elkészítése előtt törölje a virtuális gépet a folyamatban lévő folyamatok törléséhez. Az operációsrendszer-lemez pillanatképének elvégzéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Válassza a **virtuális gépek** lehetőséget az oldalsávon, majd válassza ki azt a virtuális gépet, amelyen a probléma van.
1. A bal oldali ablaktáblán válassza a **lemezek** lehetőséget, majd válassza ki az operációsrendszer-lemez nevét.
    ![Képernyőkép, amely megjeleníti az operációsrendszer-lemez nevét a lemez beállításaiban.](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Az operációsrendszer-lemez **Áttekintés** lapján válassza a **pillanatkép létrehozása** lehetőséget.
1. Hozzon létre egy pillanatképet az operációsrendszer-lemezzel megegyező helyen.

## <a name="create-a-disk-from-the-snapshot"></a>Lemez létrehozása a pillanatképből
A pillanatképből származó lemez létrehozásához kövesse az alábbi lépéseket:

1. Válassza ki **Cloud Shell** a Azure Portal.

    ![A Azure Cloud Shell megnyitására szolgáló gombot megjelenítő képernyőkép.](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. A következő PowerShell-parancsok futtatásával hozzon létre egy felügyelt lemezt a pillanatképből. Cserélje le a példában szereplő neveket a megfelelő nevekre.

    ```powershell
    #Provide the name of your resource group.
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create managed disks.
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of the managed disk.
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in gigabytes. It should be greater than the VHD file size. In this example, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for the managed disk: Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (for example, westus) where the managed disks will be located.
    #This location should be the same as the snapshot location.
    #Get all the Azure locations by using this command:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Ha a parancsok futtatása sikeresen megtörtént, a megadott erőforráscsoport új lemezét fogja látni.

## <a name="attach-the-disk-to-another-vm"></a>Lemez csatolása egy másik virtuális géphez
A következő néhány lépésben hibaelhárítási célból egy másik virtuális gépet használ. Miután csatlakoztatta a lemezt a hibaelhárítási virtuális géphez, megkeresheti és szerkesztheti a lemez tartalmát. Ez a folyamat lehetővé teszi a konfigurációs hibák kijavítását, vagy a további alkalmazás-vagy rendszernapló-fájlok áttekintését. Ha a lemezt egy másik virtuális géphez szeretné csatolni, kövesse az alábbi lépéseket:

1. Válassza ki az erőforráscsoportot a portálon, majd válassza ki a hibaelhárítási virtuális gépet. Válassza a **lemezek**  >  **Szerkesztés**  >  **adatlemez hozzáadása** elemet.

    ![Képernyőkép, amely egy meglévő lemez csatolásához szükséges beállításokat jeleníti meg a portálon.](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Az **adatlemezek** listán válassza ki az Ön által azonosított virtuális gép operációsrendszer-lemezét. Ha nem látja az operációsrendszer-lemezt, győződjön meg arról, hogy a hibaelhárítási virtuális gép és az operációsrendszer-lemez ugyanabban a régióban található (hely). 
3. A módosítások alkalmazásához válassza a **Mentés** lehetőséget.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Csatlakoztassa a csatlakoztatott adatlemezt a virtuális géphez.

1. Nyisson meg egy Távoli asztal-kapcsolódást a hibaelhárítási virtuális géphez. 
2. A hibaelhárítási virtuális gépen nyissa meg a **Kiszolgálókezelő eszközt**, majd válassza a **fájl-és tárolási szolgáltatások** lehetőséget. 

    ![Képernyőkép a fájl-és tárolási szolgáltatások kiválasztásáról a Kiszolgálókezelőben.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. A rendszer automatikusan észleli és csatolja az adatlemezt. A csatlakoztatott lemezek listájának megtekintéséhez válassza a **lemezek** lehetőséget. Az adatlemez kiválasztásával megtekintheti a kötetek adatait, beleértve a meghajtóbetűjelet is. A következő példa az **F** meghajtóval csatolt adatlemezt mutatja be.

    ![Képernyőkép, amely a Kiszolgálókezelő csatolt lemez-és kötet-információit jeleníti meg.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-problems-on-the-original-virtual-hard-disk"></a>Problémák elhárítása az eredeti virtuális merevlemezen
A meglévő virtuális merevlemez csatlakoztatása után a szükséges karbantartási és hibaelhárítási lépéseket is elvégezheti. Az összes hiba megoldása után folytassa a következő lépésekkel.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Az eredeti virtuális merevlemez leválasztása
Válassza le a meglévő virtuális merevlemezt a hibaelhárító virtuális gépről. A virtuális merevlemezt nem használhatja más virtuális géppel, amíg a virtuális merevlemezt a hibaelhárítási virtuális GÉPHEZ csatlakoztató bérlet megjelent.

1. A Távoli asztal-munkamenetből a virtuális gépre, nyissa meg a **Kiszolgálókezelő alkalmazást**, majd válassza a **fájl-és tárolási szolgáltatások** lehetőséget.

    ![Képernyőkép a fájl-és tárolási szolgáltatások kiválasztásáról a Kiszolgálókezelőben.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Válassza a **lemezek** lehetőséget, kattintson a jobb gombbal az adatlemezre, majd válassza az **offline állapotba** állítás lehetőséget.

    ![Képernyőfelvétel: az adatlemez beállítása kapcsolat nélküli üzemmódban a Kiszolgálókezelőben.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Válassza le a virtuális merevlemezt a virtuális gépről. Válassza ki a virtuális gépet a Azure Portalban, majd válassza a **lemezek** lehetőséget. 
4. Válassza a **Szerkesztés** lehetőséget, válassza ki a csatlakoztatott operációsrendszer-lemezt, majd válassza a **Törlés** lehetőséget.

    ![A meglévő virtuális merevlemez leválasztására szolgáló beállításokat bemutató képernyőkép.](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    A folytatás előtt várjon, amíg az adatlemez sikeresen törölve lett a virtuális gépen.

## <a name="swap-the-os-disk-for-the-vm"></a>A virtuális gép operációsrendszer-lemezének cseréje

Azure Portal mostantól támogatja a virtuális gép operációsrendszer-lemezének módosítását. Ehhez kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Válassza a **virtuális gépek** lehetőséget az oldalsávon, majd válassza ki azt a virtuális gépet, amelyen a probléma van.
1. A bal oldali ablaktáblán válassza a **lemezek** lehetőséget, majd válassza az **operációsrendszer-lemez cseréje** lehetőséget.
   
    ![Képernyőkép a Azure Portal operációsrendszer-lemezének cseréjére szolgáló gombról.](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Válassza ki a javított új lemezt, majd írja be a virtuális gép nevét, hogy erősítse meg a változást. Ha nem látja a lemezt a listában, várjon 10 – 15 percet, miután leválasztja a lemezt a hibaelhárítási virtuális gépről. Győződjön meg arról is, hogy a lemez a virtuális géppel megegyező helyen található.
1. Kattintson az **OK** gombra.

## <a name="next-steps"></a>Következő lépések
Ha problémába ütközne a virtuális géphez való csatlakozással kapcsolatban, tekintse meg [Az Azure-beli virtuális gép távoli asztal kapcsolatainak hibaelhárítását](troubleshoot-rdp-connection.md)ismertető témakört. A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákért lásd: az [alkalmazások kapcsolódási problémáinak elhárítása Windows rendszerű virtuális gépen](troubleshoot-app-connection.md).

További információ a Azure Resource Manager használatáról: [Azure Resource Manager áttekintése](../../azure-resource-manager/management/overview.md).



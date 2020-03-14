---
title: Windows hibaelhárítást használó virtuális gép használata a Azure Portalban | Microsoft Docs
description: A Windows rendszerű virtuális gépekkel kapcsolatos hibák elhárítása az Azure-ban az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatlakoztatásával a Azure Portal használatával
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
ms.openlocfilehash: e76fc2da8da2325a8bb0cda47c4405c9eb03c8f4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249996"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Windows rendszerű virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez csatolásával a Azure Portal használatával
Ha az Azure-beli Windows rendszerű virtuális gép rendszerindítási vagy lemezhiba-hibát észlel, előfordulhat, hogy hibaelhárítási lépéseket kell végrehajtania a virtuális merevlemezen. Gyakori példa egy sikertelen alkalmazás frissítése, amely megakadályozza, hogy a virtuális gép sikeresen elinduljon. Ez a cikk részletesen ismerteti, hogyan lehet a Azure Portal használatával összekapcsolni a virtuális merevlemezt egy másik Windowsos virtuális géppel a hibák kijavítása érdekében, majd újból létre kell hoznia az eredeti virtuális gépet. 

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Állítsa le az érintett virtuális gépet.
1. Hozzon létre egy pillanatképet a virtuális gép operációsrendszer-lemezéhez.
1. Hozzon létre egy virtuális merevlemezt a pillanatképből.
1. Hibaelhárítási célból csatlakoztassa és csatlakoztassa a virtuális merevlemezt egy másik Windows rendszerű virtuális géphez.
1. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Szerkessze a fájlokat, vagy futtasson eszközöket az eredeti virtuális merevlemezen található problémák megoldásához.
1. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
1. Cserélje le a virtuális gép operációsrendszer-lemezét.

> [!NOTE]
> Ez a cikk nem vonatkozik a nem felügyelt lemezzel rendelkező virtuális gépre.

## <a name="take-a-snapshot-of-the-os-disk"></a>Pillanatkép készítése az operációsrendszer-lemezről
A pillanatkép egy virtuális merevlemez (VHD) teljes, csak olvasható másolata. Javasoljuk, hogy a pillanatkép elkészítése előtt törölje a virtuális gépet a folyamatban lévő folyamatok törléséhez. Az operációsrendszer-lemez pillanatképének elvégzéséhez kövesse az alábbi lépéseket:

1. Lépjen [Azure Portal](https://portal.azure.com). Válassza a **virtuális gépek** lehetőséget az oldalsávon, majd válassza ki a problémát okozó virtuális gépet.
1. A bal oldali ablaktáblán válassza a **lemezek**lehetőséget, majd válassza ki az operációsrendszer-lemez nevét.
    ![lemezkép az operációsrendszer-lemez nevével](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Az operációsrendszer-lemez **Áttekintés** lapján válassza a **pillanatkép létrehozása**lehetőséget.
1. Hozzon létre egy pillanatképet az operációsrendszer-lemezzel megegyező helyen.

## <a name="create-a-disk-from-the-snapshot"></a>Lemez létrehozása a pillanatképből
A pillanatképből származó lemez létrehozásához kövesse az alábbi lépéseket:

1. Válassza ki **Cloud Shell** a Azure Portal.

    ![Az Open Cloud Shell képe](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. A következő PowerShell-parancsok futtatásával hozzon létre egy felügyelt lemezt a pillanatképből. Ezeket a neveket a megfelelő nevekkel kell helyettesíteni.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Ha a parancsok futtatása sikeresen megtörtént, az új lemezt a megadott erőforráscsoporthoz fogja látni.

## <a name="attach-the-disk-to-another-vm"></a>Lemez csatolása egy másik virtuális géphez
A következő néhány lépésben hibaelhárítási célból egy másik virtuális gépet használ. Miután csatlakoztatta a lemezt a hibaelhárítási virtuális géphez, megkeresheti és szerkesztheti a lemez tartalmát. Ez a folyamat lehetővé teszi a konfigurációs hibák kijavítását, vagy a további alkalmazás-vagy rendszernapló-fájlok áttekintését. Ha a lemezt egy másik virtuális géphez szeretné csatolni, kövesse az alábbi lépéseket:

1. Válassza ki az erőforráscsoportot a portálon, majd válassza ki a hibaelhárítási virtuális gépet. Válassza a **lemezek**lehetőséget, válassza a **Szerkesztés**lehetőséget, majd kattintson **az adatlemez hozzáadása**elemre:

    ![Meglévő lemez csatolása a portálon](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Az **adatlemezek** listán válassza ki az Ön által azonosított virtuális gép operációsrendszer-lemezét. Ha nem látja az operációsrendszer-lemezt, ellenőrizze, hogy a virtuális gép és az operációs rendszer lemeze ugyanabban a régióban van-e (hely). 
3. A módosítások alkalmazásához válassza a **Mentés** lehetőséget.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Csatlakoztassa a csatlakoztatott adatlemezt a virtuális géphez.

1. Nyisson meg egy Távoli asztal-kapcsolódást a hibaelhárítási virtuális géphez. 
2. A virtuális gép hibakeresése lapon nyissa meg a **Kiszolgálókezelő alkalmazást**, majd válassza a **fájl-és tárolási szolgáltatások**lehetőséget. 

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
4. Válassza a **Szerkesztés**lehetőséget, válassza ki a csatlakoztatott operációsrendszer-lemezt, majd kattintson a **Leválasztás**elemre:

    ![Meglévő virtuális merevlemez leválasztása](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Várjon, amíg a virtuális gép sikeresen leválasztott egy adatlemezt a folytatás előtt.

## <a name="swap-the-os-disk-for-the-vm"></a>A virtuális gép operációsrendszer-lemezének cseréje

Azure Portal mostantól támogatja a virtuális gép operációsrendszer-lemezének módosítását. Ehhez kövesse az alábbi lépéseket:

1. Lépjen [Azure Portal](https://portal.azure.com). Válassza a **virtuális gépek** lehetőséget az oldalsávon, majd válassza ki a problémát okozó virtuális gépet.
1. A bal oldali ablaktáblán válassza a **lemezek**lehetőséget, majd válassza az **operációsrendszer-lemez cseréje**lehetőséget.
        ![az operációsrendszer-lemez kicserélése Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Válassza ki a javított új lemezt, majd írja be a virtuális gép nevét, hogy erősítse meg a változást. Ha nem látja a lemezt a listában, várjon 10 ~ 15 percet a lemez leválasztása után a hibaelhárítási virtuális gépről. Győződjön meg arról is, hogy a lemez a virtuális géppel megegyező helyen található.
1. Kattintson az OK gombra.

## <a name="next-steps"></a>Következő lépések
Ha problémába ütközik a virtuális géphez való csatlakozással kapcsolatban, tekintse meg [az RDP-kapcsolatok hibaelhárítása Azure-beli virtuális géppel](troubleshoot-rdp-connection.md)című témakört. A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákért lásd: az [alkalmazások kapcsolódási problémáinak elhárítása Windows rendszerű virtuális gépen](troubleshoot-app-connection.md).

További információ a Resource Manager használatáról: [Azure Resource Manager Overview (áttekintés](../../azure-resource-manager/management/overview.md)).



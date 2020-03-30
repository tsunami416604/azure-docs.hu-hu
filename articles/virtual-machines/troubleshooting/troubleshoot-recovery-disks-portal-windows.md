---
title: Windows hibaelhárítási virtuális gép használata az Azure Portalon | Microsoft dokumentumok
description: Megtudhatja, hogy miként háríthatja el a Windows virtuálisgépekkel kapcsolatos problémáit az Azure-ban az operációs rendszer lemezének az Azure Portalon keresztüli helyreállítási virtuális géphez való csatlakoztatásával
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249996"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Windows virtuális gép hibaelhárítása az operációs rendszer lemezének helyreállítási virtuális géphez való csatlakoztatásával az Azure Portal használatával
Ha a Windows virtuális gép (VM) az Azure-ban észlel egy rendszerindító vagy lemez hiba, előfordulhat, hogy hibaelhárítási lépéseket kell végrehajtania a virtuális merevlemezen. Egy gyakori példa lenne egy sikertelen alkalmazásfrissítés, amely megakadályozza, hogy a virtuális gép sikeresen eltudja indítani. Ez a cikk részletezi, hogyan használhatja az Azure Portalon a virtuális merevlemez csatlakoztatása egy másik Windows virtuális gép a hibák kijavítása, majd újra létrehozza az eredeti virtuális gép. 

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Állítsa le az érintett virtuális gép.
1. Hozzon létre egy pillanatképet a virtuális gép operációs rendszer lemezéhez.
1. Hozzon létre egy virtuális merevlemezt a pillanatképből.
1. Hibaelhárítási célból csatolja és csatlakoztatja a virtuális merevlemezt egy másik Windows virtuális géphez.
1. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Fájlok szerkesztése vagy bármilyen eszköz futtatása az eredeti virtuális merevlemezen felmerülő problémák megoldásához.
1. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
1. Az operációs rendszer lemezének felcserélése a virtuális gépre.

> [!NOTE]
> Ez a cikk nem vonatkozik a nem felügyelt lemezzel rendelkező virtuális gépre.

## <a name="take-a-snapshot-of-the-os-disk"></a>Pillanatkép készítése az operációs rendszer lemezéről
A pillanatkép egy virtuális merevlemez (VHD) teljes, csak olvasható másolata. Azt javasoljuk, hogy a pillanatkép készítése előtt állítsa le a virtuális gép, törölje a folyamatban lévő folyamatok törlése. Az operációs rendszer lemezének pillanatképének készítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com) Válassza ki a **virtuális gépek** az oldalsávon, majd válassza ki a virtuális gépet, amely problémát okoz.
1. A bal oldali ablaktáblában válassza a **Lemezek**lehetőséget, majd jelölje ki az operációs rendszer lemezének nevét.
    ![Az operációs rendszer lemezének nevéről szóló kép](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Az operációs rendszer lemezének **Áttekintés lapján** válassza a **Pillanatkép létrehozása lehetőséget.**
1. Hozzon létre egy pillanatképet ugyanazon a helyen, mint az operációs rendszer lemeze.

## <a name="create-a-disk-from-the-snapshot"></a>Lemez létrehozása a pillanatképből
Ha lemezt szeretne létrehozni a pillanatképből, kövesse az alábbi lépéseket:

1. Válassza ki a **Cloud Shell** az Azure Portalon.

    ![Kép az Open Cloud Shell-ről](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Futtassa a következő PowerShell-parancsokat felügyelt lemez létrehozásához a pillanatképből. Ezeket a mintaneveket a megfelelő nevekre kell cserélni.

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
3. Ha a parancsok sikeresen futnak, az új lemez megjelenik a megadott erőforráscsoportban.

## <a name="attach-the-disk-to-another-vm"></a>A lemez csatolása másik virtuális géphez
A következő néhány lépésben egy másik virtuális gép hibaelhárítási célokra. Miután csatlakoztatta a lemezt a hibaelhárítási virtuális géphez, tallózhat és szerkesztheti a lemez tartalmát. Ez a folyamat lehetővé teszi a konfigurációs hibák kijavítását vagy további alkalmazás- vagy rendszernaplófájlok áttekintését. Ha a lemezt egy másik virtuális géphez szeretné csatolni, kövesse az alábbi lépéseket:

1. Válassza ki az erőforráscsoportot a portálon, majd válassza ki a hibaelhárítási virtuális gép. Válassza a **Lemezek**lehetőséget, válassza **a Szerkesztés**lehetőséget, majd kattintson **az Adatlemez hozzáadása**gombra :

    ![Meglévő lemez csatolása a portálon](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Az **adatlemezek** listájában válassza ki az azonosított virtuális gép operációsrendszer-lemezét. Ha nem látja az operációs rendszer lemezét, győződjön meg arról, hogy a virtuális gép és az operációs rendszer lemezének hibaelhárítása ugyanabban a régióban (helyen) található. 
3. A módosítások alkalmazásához válassza a **Mentés** gombot.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>A csatolt adatlemez csatlakoztatása a virtuális géphez

1. Nyisson meg egy távoli asztali kapcsolatot a hibaelhárítási virtuális géphez. 
2. A hibaelhárítási modulban nyissa meg a **Kiszolgálókezelőt,** majd válassza a **Fájl- és tárolási szolgáltatások lehetőséget.** 

    ![Fájl- és tárolási szolgáltatások kiválasztása a Kiszolgálókezelőben](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. A rendszer automatikusan felismeri és csatolja az adatlemezt. A csatlakoztatott lemezek listájának megtekintéséhez válassza a **Lemezek**lehetőséget. Az adatlemez kiválasztásával megtekintheti a kötetadatokat, beleértve a meghajtóbetűjelet is. A következő példa a csatolt és az F használatával csatlakoztatott és használt adatlemezt mutatja **be:**:

    ![Lemezcsatlakoztatás és kötetadatok a Kiszolgálókezelőben](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>Az eredeti virtuális merevlemezekkel kapcsolatos problémák megoldása
A meglévő virtuális merevlemez csatlakoztatásával mostantól szükség szerint elvégezheti a karbantartási és hibaelhárítási lépéseket. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Eredeti virtuális merevlemez leválasztása és leválasztása
A hibák megoldása után válassza le a meglévő virtuális merevlemezt a hibaelhárítási virtuális gépről. A virtuális merevlemez nem használható más virtuális géptel, amíg a virtuális merevlemezt a hibaelhárítási virtuális géphez csatlakoztató bérlet fel nem szabadul.

1. Az RDP-munkamenetből a virtuális gépre nyissa meg a **Kiszolgálókezelőt,** majd válassza a **Fájl- és tárolási szolgáltatások lehetőséget:**

    ![Fájl- és tárolási szolgáltatások kiválasztása a Kiszolgálókezelőben](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Válassza **a Lemezek** lehetőséget, majd jelölje ki az adatlemezt. Kattintson a jobb gombbal az adatlemezre, és válassza **az Offline mód kiválasztását:**

    ![Az adatlemez beállítása kapcsolat nélküli módként a Kiszolgálókezelőben](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Most válassza le a virtuális merevlemezt a virtuális gépről. Válassza ki a virtuális gép az Azure Portalon, és kattintson **a Lemezek gombra.** 
4. Válassza **a Szerkesztés lehetőséget,** jelölje ki a csatlakoztatott operációsrendszer-lemezt, majd kattintson a **Leválasztás**gombra:

    ![Meglévő virtuális merevlemez leválasztása](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    A folytatás előtt várja meg, amíg a virtuális gép sikeresen leválasztotta az adatlemezt.

## <a name="swap-the-os-disk-for-the-vm"></a>Az operációs rendszer lemezének felcserélése a virtuális gépre

Az Azure Portal mostantól támogatja a virtuális gép operációsrendszer-lemezének módosítását. Ehhez kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com) Válassza ki a **virtuális gépek** az oldalsávon, majd válassza ki a virtuális gépet, amely problémát okoz.
1. A bal oldali ablaktáblában válassza a **Lemezek**lehetőséget, majd válassza **az Operációsrendszer-lemez cseréje**lehetőséget .
        ![Az operációsrendszer-lemez cseréjéről az Azure Portalon](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Válassza ki a javított új lemezt, majd írja be a virtuális gép nevét a módosítás megerősítéséhez. Ha nem látja a lemezt a listában, várjon 10 ~ 15 percet, miután leválasztja a lemezt a hibaelhárítási virtuális gépről. Győződjön meg arról is, hogy a lemez ugyanazon a helyen található, mint a virtuális gép.
1. Kattintson az OK gombra.

## <a name="next-steps"></a>További lépések
Ha problémái vannak a virtuális géphez való csatlakozással, olvassa [el az RDP-kapcsolatok hibaelhárítása azure-beli virtuális géphez című témakört.](troubleshoot-rdp-connection.md) A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákról a [Windows virtuális gépeken az alkalmazáscsatlakozási problémák elhárítása című](troubleshoot-app-connection.md)témakörben nyújt fel problémát.

Az Erőforrás-kezelő használatáról az Azure Resource Manager – áttekintés című témakörben olvashat [bővebben.](../../azure-resource-manager/management/overview.md)



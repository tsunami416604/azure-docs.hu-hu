---
title: Linuxos hibaelhárítási virtuális gép használata az Azure Portalon | Microsoft dokumentumok
description: Ismerje meg, hogyan háríthatja el a Linux-virtuálisgéppel kapcsolatos problémákat az operációs rendszer lemezének az Azure Portal on-val történő helyreállítási virtuális géphez való csatlakoztatásával
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: e45de5c12f0d93645a0b1253acf8300527cafdbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374641"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Linuxos virtuális gép hibaelhárítása az operációs rendszer lemezének az Azure Portalon keresztül idomuló helyreállítási virtuális géphez való csatlakoztatásával
Ha a Linux virtuális gép (VM) rendszerindítási vagy lemezhibába ütközik, előfordulhat, hogy hibaelhárítási lépéseket kell végrehajtania magán a virtuális merevlemezen. Egy gyakori példa lenne egy `/etc/fstab` érvénytelen bejegyzés, amely megakadályozza, hogy a virtuális gép sikeresen eltudja indítani. Ez a cikk ismerteti, hogyan használhatja az Azure Portalon a virtuális merevlemez csatlakoztatása egy másik Linux virtuális gép a hibák kijavítása, majd újra létrehozza az eredeti virtuális gépet.

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Állítsa le az érintett virtuális gép.
1. Pillanatkép készítése a virtuális gép operációs rendszer lemezéről.
1. Hozzon létre egy virtuális merevlemezt a pillanatképből.
1. Hibaelhárítási célból csatolja és csatlakoztatja a virtuális merevlemezt egy másik Windows virtuális géphez.
1. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Fájlok szerkesztése vagy bármilyen eszköz futtatása az eredeti virtuális merevlemezen felmerülő problémák megoldásához.
1. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
1. Az operációs rendszer lemezének felcserélése a virtuális gépre.

> [!NOTE]
> Ez a cikk nem vonatkozik a nem felügyelt lemezzel rendelkező virtuális gépre.

## <a name="determine-boot-issues"></a>Rendszerindítási problémák meghatározása
Vizsgálja meg a rendszerindítási diagnosztika és a virtuális gép képernyőkép, hogy miért a virtuális gép nem tudja megfelelően elindítani. Gyakori példa egy érvénytelen bejegyzés `/etc/fstab`a alkalmazásban, vagy egy mögöttes virtuális merevlemez törlése vagy áthelyezése.

Válassza ki a virtuális gép a portálon, majd görgessen le a **Támogatás + Hibaelhárítás** szakasz. Kattintson **a Rendszerindításdia gombra** a virtuális gépről streamelt konzolüzenetek megtekintéséhez. Tekintse át a konzol naplók, hogy ha meg tudja állapítani, hogy miért a virtuális gép hibát tapasztal. A következő példa egy karbantartási módban ragadt virtuális géplátható, amely manuális beavatkozást igényel:

![A virtuális gép rendszerindítási diagnosztikai konzolnaplóinak megtekintése](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

A rendszerindító diagnosztikai napló tetején lévő **Képernyőkép** elemre kattintva letöltheti a virtuális gép képernyőképének rögzítését.

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
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
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

## <a name="attach-disk-to-another-vm"></a>Lemez csatolása másik virtuális géphez
A következő néhány lépésben egy másik virtuális gép hibaelhárítási célokra. Miután csatlakoztatta a lemezt a hibaelhárítási virtuális géphez, tallózhat és szerkesztheti a lemez tartalmát. Ez a folyamat lehetővé teszi a konfigurációs hibák kijavítását vagy további alkalmazás- vagy rendszernaplófájlok áttekintését. Ha a lemezt egy másik virtuális géphez szeretné csatolni, kövesse az alábbi lépéseket:

1. Válassza ki az erőforráscsoportot a portálon, majd válassza ki a hibaelhárítási virtuális gép. Válassza a **Lemezek**lehetőséget, válassza **a Szerkesztés**lehetőséget, majd kattintson **az Adatlemez hozzáadása**gombra :

    ![Meglévő lemez csatolása a portálon](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Az **adatlemezek** listájában válassza ki az azonosított virtuális gép operációsrendszer-lemezét. Ha nem látja az operációs rendszer lemezét, győződjön meg arról, hogy a virtuális gép és az operációs rendszer lemezének hibaelhárítása ugyanabban a régióban (helyen) található. 
3. A módosítások alkalmazásához válassza a **Mentés** gombot.

## <a name="mount-the-attached-data-disk"></a>A csatolt adatlemez csatlakoztatása

> [!NOTE]
> Az alábbi példák ismertetik az Ubuntu virtuális gép szükséges lépéseket. Ha egy másik Linux disztribúciót használ, például a Red Hat `mount` Enterprise Linuxot vagy a SUSE-t, a naplófájl helye és parancsai egy kicsit eltérőek lehetnek. A parancsok megfelelő módosításait az adott distro dokumentációjában találja.

1. SSH a hibaelhárítás iM a megfelelő hitelesítő adatokhasználatával. Ha ez a lemez az első adatlemez, amely a hibaelhárítási virtuális géphez van csatlakoztatva, akkor valószínűleg a hoz `/dev/sdc`csatlakozik. Csatolt `dmseg` lemezek listázására használható:

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

    Az előző példában az operációs `/dev/sda` rendszer lemeze van, és `/dev/sdb`az ideiglenes lemez minden virtuális gép a. Ha több adatlemeze volt, akkor `/dev/sdd` `/dev/sde`azoknak a és a, és így tovább kell lenniük.

2. Hozzon létre egy könyvtárat a meglévő virtuális merevlemez csatlakoztatásához. A következő példa létrehoz `troubleshootingdisk`egy könyvtár at named :

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Ha több partíció van a meglévő virtuális merevlemezen, csatlakoztassa a szükséges partíciót. A következő példa az első `/dev/sdc1`elsődleges partíciót csatlakoztatja:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Ajánlott eljárás ként érdemes adatlemezeket csatlakoztatni az Azure-beli virtuális gépekhez a virtuális merevlemez univerzálisan egyedi azonosítójának (UUID) használatával. Ebben a rövid hibaelhárítási forgatókönyvben nincs szükség a virtuális merevlemez UUID használatával történő csatlakoztatására. Normál használat esetén azonban `/etc/fstab` a virtuális merevlemezek uuid helyett az eszköz nevének használatával történő csatlakoztatása a virtuális gép indításának sikertelensítéséhez vezethet.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Az eredeti virtuális merevlemezekkel kapcsolatos problémák megoldása
A meglévő virtuális merevlemez csatlakoztatásával mostantól szükség szerint elvégezheti a karbantartási és hibaelhárítási lépéseket. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Eredeti virtuális merevlemez leválasztása és leválasztása
A hibák megoldása után válassza le a meglévő virtuális merevlemezt a hibaelhárítási virtuális gépről. A virtuális merevlemez nem használható más virtuális géptel, amíg a virtuális merevlemezt a hibaelhárítási virtuális géphez csatlakoztató bérlet fel nem szabadul.

1. Az SSH-munkamenettől a hibaelhárítási virtuális gépig válassza le a meglévő virtuális merevlemezt. Először váltson ki a csatlakoztatási pont szülőkönyvtárából:

    ```bash
    cd /
    ```

    Most leválasztani a meglévő virtuális merevlemezt. A következő példa leszereli `/dev/sdc1`az eszközt a következő helyen:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Most válassza le a virtuális merevlemezt a virtuális gépről. Válassza ki a virtuális gép a portálon, és kattintson **a Lemezek gombra**. Válassza ki a meglévő virtuális merevlemezt, majd kattintson **a Leválasztás gombra:**

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
Ha problémái vannak a virtuális géphez való csatlakozással, olvassa [el az SSH-kapcsolatok hibaelhárítása azure-beli virtuális géphez című témakört.](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) A virtuális számítógépen futó alkalmazások elérésével kapcsolatos problémákról a [Linux-alapú virtuális gépeken az alkalmazáscsatlakozási problémák elhárítása című](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)témakörben nyújt fel problémát.

Az Erőforrás-kezelő használatáról az Azure Resource Manager – áttekintés című témakörben olvashat [bővebben.](../../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

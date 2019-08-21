---
title: Linux hibaelhárítási virtuális gép használata a Azure Portalban | Microsoft Docs
description: A Linux rendszerű virtuális gépekkel kapcsolatos hibák elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez való csatlakoztatásával a Azure Portal használatával
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: 21122847c1b417b00cfe8c69b8324a2f73bf31ea
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69641133"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Linux rendszerű virtuális gép hibáinak elhárítása az operációsrendszer-lemez egy helyreállítási virtuális géphez csatolásával a Azure Portal használatával
Ha a linuxos virtuális gép (VM) rendszerindítási vagy lemezhiba miatti hibát észlel, lehetséges, hogy a virtuális merevlemezen hibaelhárítási lépéseket kell végrehajtania. Egy gyakori példa lehet egy olyan bejegyzés, `/etc/fstab` amely megakadályozza, hogy a virtuális gép sikeresen elinduljon. Ez a cikk részletesen ismerteti, hogyan lehet a Azure Portal használatával összekapcsolni a virtuális merevlemezt egy másik linuxos virtuális géppel a hibák elhárítása érdekében, majd újból létre kell hoznia az eredeti virtuális gépet.

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Állítsa le az érintett virtuális gépet.
1. Készítsen pillanatképet a virtuális gép operációsrendszer-lemezéről.
1. Hozzon létre egy virtuális merevlemezt a pillanatképből.
1. Hibaelhárítási célból csatlakoztassa és csatlakoztassa a virtuális merevlemezt egy másik Windows rendszerű virtuális géphez.
1. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Szerkessze a fájlokat, vagy futtasson eszközöket az eredeti virtuális merevlemezen található problémák megoldásához.
1. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
1. Cserélje le a virtuális gép operációsrendszer-lemezét.

> [!NOTE]
> Ez a cikk nem vonatkozik a nem felügyelt lemezzel rendelkező virtuális gépre.

## <a name="determine-boot-issues"></a>Rendszerindítási problémák meghatározása
Vizsgálja meg a rendszerindítási diagnosztika és a virtuális gép képernyőképét annak meghatározásához, hogy a virtuális gép miért nem tud megfelelően elindulni. Gyakori például `/etc/fstab`, hogy a bejegyzés érvénytelen, vagy egy mögöttes virtuális merevlemez törölve vagy áthelyezve lett.

Válassza ki a virtuális gépet a portálon, majd görgessen le a **támogatás + hibaelhárítás** szakaszhoz. Kattintson a rendszerindítási **diagnosztika** elemre a virtuális gépről továbbított konzol üzeneteinek megtekintéséhez. Tekintse át a konzol naplóit, és ellenőrizze, hogy a virtuális gép miért nem fog problémát észlelni. Az alábbi példa egy olyan virtuális gépet mutat be, amely kézi interakciót igénylő karbantartási módban ragadt:

![VIRTUÁLIS gépek rendszerindítási diagnosztikai konzoljának naplófájljainak megtekintése](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

A rendszerindítási diagnosztikai napló tetején található **képernyőkép** lehetőségre kattintva letöltheti a virtuális gép képernyőképének rögzítését.

## <a name="take-a-snapshot-of-the-os-disk"></a>Pillanatkép készítése az operációsrendszer-lemezről
A pillanatkép egy virtuális merevlemez (VHD) teljes, csak olvasható másolata. Javasoljuk, hogy a pillanatkép elkészítése előtt törölje a virtuális gépet a folyamatban lévő folyamatok törléséhez. Az operációsrendszer-lemez pillanatképének elvégzéséhez kövesse az alábbi lépéseket:

1. Lépjen [Azure Portal](https://portal.azure.com). Válassza a **virtuális gépek** lehetőséget az oldalsávon, majd válassza ki a problémát okozó virtuális gépet.
1. A bal oldali ablaktáblán válassza a **lemezek**lehetőséget, majd válassza ki az operációsrendszer-lemez nevét.
    ![Az operációsrendszer-lemez nevével kapcsolatos rendszerkép](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
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
    
    #Provide the storage type for Managed Disk. PremiumLRS or StandardLRS.
    $storageType = 'StandardLRS'
    
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

## <a name="attach-disk-to-another-vm"></a>Lemez csatolása egy másik virtuális géphez
A következő néhány lépésben hibaelhárítási célból egy másik virtuális gépet használ. Miután csatlakoztatta a lemezt a hibaelhárítási virtuális géphez, megkeresheti és szerkesztheti a lemez tartalmát. Ez a folyamat lehetővé teszi a konfigurációs hibák kijavítását, vagy a további alkalmazás-vagy rendszernapló-fájlok áttekintését. Ha a lemezt egy másik virtuális géphez szeretné csatolni, kövesse az alábbi lépéseket:

1. Válassza ki az erőforráscsoportot a portálon, majd válassza ki a hibaelhárítási virtuális gépet. Válassza a **lemezek**lehetőséget, válassza a **Szerkesztés**lehetőséget, majd kattintson **az adatlemez hozzáadása**elemre:

    ![Meglévő lemez csatolása a portálon](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Az adatlemezek listán válassza ki az Ön által azonosított virtuális gép operációsrendszer-lemezét. Ha nem látja az operációsrendszer-lemezt, ellenőrizze, hogy a virtuális gép és az operációs rendszer lemeze ugyanabban a régióban van-e (hely). 
3. A módosítások alkalmazásához válassza a **Mentés** lehetőséget.

## <a name="mount-the-attached-data-disk"></a>A csatlakoztatott adatlemez csatlakoztatása

> [!NOTE]
> Az alábbi példák egy Ubuntu virtuális gépen szükséges lépéseket részletezik. Ha más Linux-disztribúciót használ, például Red Hat Enterprise Linux vagy SUSE, a naplófájl helyei és `mount` parancsai némileg eltérőek lehetnek. Tekintse át az adott disztribúció dokumentációját a parancsok megfelelő módosításaihoz.

1. A megfelelő hitelesítő adatok használatával SSH-t a hibaelhárítási virtuális géphez. Ha ez a lemez az első, a hibaelhárítási virtuális géphez csatolt adatlemez, valószínűleg csatlakozik `/dev/sdc`a szolgáltatáshoz. A `dmseg` következő paranccsal listázhatja a csatolt lemezeket:

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

    Az előző példában az operációsrendszer-lemez a (z `/dev/sda` ) helyen található, és az egyes virtuális gépekhez `/dev/sdb`megadott ideiglenes lemez a következő helyen található:. Ha több adatlemezzel is rendelkezett, akkor a következő `/dev/sdd`helyen `/dev/sde`kell lennie:, és így tovább.

2. Hozzon létre egy könyvtárat a meglévő virtuális merevlemez csatlakoztatásához. A következő példa egy nevű `troubleshootingdisk`könyvtárat hoz létre:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Ha több partícióval rendelkezik a meglévő virtuális merevlemezen, csatlakoztassa a szükséges partíciót. Az alábbi példa az első elsődleges partíciót csatlakoztatja a `/dev/sdc1`következő helyen:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Ajánlott eljárás az adatlemezek csatlakoztatása az Azure-beli virtuális gépekhez a virtuális merevlemez univerzálisan egyedi azonosítója (UUID) használatával. Ebben a rövid hibaelhárítási forgatókönyvben nem szükséges a virtuális merevlemezt az UUID használatával csatlakoztatni. A normál használat `/etc/fstab` alatt azonban az UUID helyett a virtuális merevlemezek az eszköz nevével való csatlakoztatásakor előfordulhat, hogy a virtuális gép nem tud elindulni.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Hibák elhárítása az eredeti virtuális merevlemezen
A meglévő virtuális merevlemez csatlakoztatása után a szükséges karbantartási és hibaelhárítási lépéseket is elvégezheti. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Eredeti virtuális merevlemez leválasztása és leválasztása
A hibák megoldása után válassza le a meglévő virtuális merevlemezt a hibaelhárítási virtuális gépről. A virtuális merevlemezt nem használhatja más virtuális géppel, amíg a virtuális merevlemezt a hibaelhárítási virtuális géphez csatlakoztató címbérlet megjelent.

1. Az SSH-munkamenetből a hibaelhárító virtuális GÉPHEZ válassza le a meglévő virtuális merevlemezt. Először a csatlakoztatási ponthoz tartozó szülő könyvtáron változtassa meg a következőt:

    ```bash
    cd /
    ```

    Most válassza le a meglévő virtuális merevlemezt. Az alábbi példa leválasztja az eszközt a `/dev/sdc1`következő helyen:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Most válassza le a virtuális merevlemezt a virtuális gépről. Válassza ki a virtuális gépet a portálon, és kattintson a **lemezek**elemre. Válassza ki a meglévő virtuális merevlemezt, majdkattintson a Leválasztás elemre:

    ![Meglévő virtuális merevlemez leválasztása](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Várjon, amíg a virtuális gép sikeresen leválasztott egy adatlemezt a folytatás előtt.

## <a name="swap-the-os-disk-for-the-vm"></a>A virtuális gép operációsrendszer-lemezének cseréje

Azure Portal mostantól támogatja a virtuális gép operációsrendszer-lemezének módosítását. Ehhez kövesse az alábbi lépéseket:

1. Lépjen [Azure Portal](https://portal.azure.com). Válassza a **virtuális gépek** lehetőséget az oldalsávon, majd válassza ki a problémát okozó virtuális gépet.
1. A bal oldali ablaktáblán válassza a **lemezek**lehetőséget, majd válassza az **operációsrendszer-lemez cseréje**lehetőséget.
        ![Az operációsrendszer-lemez lecserélése Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Válassza ki a javított új lemezt, majd írja be a virtuális gép nevét, hogy erősítse meg a változást. Ha nem látja a lemezt a listában, várjon 10 ~ 15 percet a lemez leválasztása után a hibaelhárítási virtuális gépről. Győződjön meg arról is, hogy a lemez a virtuális géppel megegyező helyen található.
1. Kattintson az OK gombra.

## <a name="next-steps"></a>További lépések
Ha problémába ütközik a virtuális géphez való csatlakozással kapcsolatban, olvassa el az [SSH-kapcsolatok Azure-beli virtuális géphez való hibaelhárítását](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)ismertető témakört. A virtuális gépen futó alkalmazások elérésével kapcsolatos problémákért lásd: az [alkalmazások kapcsolódási problémáinak elhárítása Linux rendszerű virtuális gépen](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

További információ a Resource Manager használatáról: [Azure Resource Manager Overview (áttekintés](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

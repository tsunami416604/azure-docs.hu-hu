---
title: Linuxos hibaelhárítási virtuális gép használata az Azure CLI-vel | Microsoft dokumentumok
description: Ismerje meg, hogyan háríthatja el a Linux virtuális gépekkel kapcsolatos problémákat az operációs rendszer lemezének az Azure CLI használatával egy helyreállítási virtuális géphez való csatlakoztatásával
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 1b91a39e1297d8952da67a4f8d3b8568cefe04ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73620565"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Linuxos virtuális gép hibaelhárítása az operációs rendszer lemezének az Azure CLI-vel egy helyreállítási virtuális géphez való csatlakoztatásával
Ha a Linux virtuális gép (VM) rendszerindítási vagy lemezhibába ütközik, előfordulhat, hogy hibaelhárítási lépéseket kell végrehajtania magán a virtuális merevlemezen. Egy gyakori példa lenne egy `/etc/fstab` érvénytelen bejegyzés, amely megakadályozza, hogy a virtuális gép sikeresen eltudja indítani. Ez a cikk részletezi, hogyan használhatja az Azure CLI-t a virtuális merevlemez csatlakoztatásához egy másik Linux virtuális géphez a hibák kijavításához, majd hozza létre újra az eredeti virtuális gépet. 

## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Állítsa le az érintett virtuális gép.
1. Pillanatkép készítése a virtuális gép operációs rendszer lemezéről.
1. Hozzon létre egy lemezt az operációs rendszer lemezének pillanatképéből.
1. Az új operációsrendszer-lemez csatlakoztatása és csatlakoztatása egy másik Linux virtuális géphez hibaelhárítási célokra.
1. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Fájlok szerkesztése vagy bármilyen eszköz futtatása az új operációsrendszer-lemezen felmerülő problémák megoldásához.
1. Válassza le és válassza le az új operációsrendszer-lemezt a hibaelhárítási virtuális gépről.
1. Módosítsa az érintett virtuális gép operációsrendszer-lemezét.

A hibaelhárítási lépések végrehajtásához a legújabb [Azure CLI-t](/cli/azure/install-az-cli2) kell telepítenie, és be kell jelentkeznie egy Azure-fiókba [az a bejelentkezéssel.](/cli/azure/reference-index)

> [!Important]
> A cikkben szereplő parancsfájlok csak a [felügyelt lemezt](../linux/managed-disks-overview.md)használó virtuális gépekre vonatkoznak. 

A következő példákban cserélje le a paraméterneveket a saját értékeire, például `myResourceGroup` a és `myVM`a.

## <a name="determine-boot-issues"></a>Rendszerindítási problémák meghatározása
Vizsgálja meg a soros kimenetet annak megállapításához, hogy a virtuális gép miért nem tudja megfelelően elindítani. Gyakori példa erre a program `/etc/fstab`érvénytelen bejegyzése, vagy az alapul szolgáló virtuális merevlemez törlése vagy áthelyezése.

A rendszerindító naplók beszerezni [az az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics). A következő példa leveszi a soros `myVM` kimenetet a `myResourceGroup`virtuális gép neve a nevű erőforráscsoportban:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Tekintse át a soros kimenetet annak meghatározásához, hogy a virtuális gép miért nem indul el. Ha a soros kimenet nem nyújt semmilyen jelzést, előfordulhat, hogy át kell tekintenie a `/var/log` naplófájlokat, miután a virtuális merevlemez csatlakoztatva van egy hibaelhárítási virtuális géphez.

## <a name="stop-the-vm"></a>A virtuális gép leállítása

A következő példa leállítja a névvel ellátott erőforráscsoportból megnevezett `myVM` virtuális gépnevét: `myResourceGroup`

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>Pillanatkép készítése az érintett virtuális gép operációs rendszerlemezéről

A pillanatkép a virtuális merevlemez teljes, csak olvasható példánya. Nem csatolható virtuális géphez. A következő lépésben létrehozunk egy lemezt ebből a pillanatképből. A következő példa létrehoz `mySnapshot` egy pillanatképet a "myVM" nevű virtuális gép operációsrendszer-lemezének nevével. 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>Lemez létrehozása a pillanatképből

Ez a parancsfájl egy `myOSDisk` felügyelt lemezt `mySnapshot`hoz létre névvel a snapshot nevű .  

```azurecli
#Provide the name of your resource group
$resourceGroup=myResourceGroup

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot=mySnapshot

#Provide the name of the Managed Disk
$osDisk=myNewOSDisk

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType=Premium_LRS

#Provide the OS type
$osType=linux

#Provide the name of the virtual machine
$virtualMachine=myVM

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query [id] -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

Ha az erőforráscsoport és a forráspillanatkép nem ugyanabban a régióban található, a `az disk create`futtatáskor az "Erőforrás nem található" hibaüzenet jelenik meg. Ebben az esetben meg `--location <region>` kell adnia, hogy a lemezt ugyanabba a régióba hozza létre, mint a forrás pillanatképet.

Most már van egy másolatot az eredeti operációs rendszer lemez. Az új lemezt hibaelhárítási célból csatlakoztathatja egy másik Windows virtuális géphez.

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>Az új virtuális merevlemez csatolása másik virtuális géphez
A következő néhány lépésben egy másik virtuális gép hibaelhárítási célokra. A lemezt ehhez a hibaelhárítási virtuális géphez csatlakoztatja a lemez tartalmának tallózásához és szerkesztéséhez. Ez a folyamat lehetővé teszi a konfigurációs hibák kijavítását vagy további alkalmazás- vagy rendszernaplófájlok áttekintését.

Ez a parancsfájl `myNewOSDisk` csatolja `MyTroubleshootVM`a lemezt a virtuális géphez:

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $diskId --resource-group MyResourceGroup --size-gb 128 --sku Standard_LRS --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>A csatolt adatlemez csatlakoztatása

> [!NOTE]
> Az alábbi példák ismertetik az Ubuntu virtuális gép szükséges lépéseket. Ha egy másik Linux disztribúciót használ, például a Red Hat `mount` Enterprise Linuxot vagy a SUSE-t, a naplófájl helye és parancsai egy kicsit eltérőek lehetnek. A parancsok megfelelő módosításait az adott distro dokumentációjában találja.

1. SSH a hibaelhárítás iM a megfelelő hitelesítő adatokhasználatával. Ha ez a lemez az első adatlemez, amely a hibaelhárítási virtuális géphez van csatlakoztatva, akkor a lemez valószínűleg a hoz `/dev/sdc`csatlakozik. Csatolt `dmesg` lemezek megtekintésére használható:

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


## <a name="fix-issues-on-the-new-os-disk"></a>Az új operációsrendszer-lemezen felmerülő problémák megoldása
A meglévő virtuális merevlemez csatlakoztatásával mostantól szükség szerint elvégezheti a karbantartási és hibaelhárítási lépéseket. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.


## <a name="unmount-and-detach-the-new-os-disk"></a>Az új operációsrendszer-lemez leválasztása és leválasztása
Miután a hibák at oldották meg, leválasztja és leválasztja a meglévő virtuális merevlemezt a hibaelhárítási virtuális gépről. A virtuális merevlemez nem használható más virtuális géptel, amíg a virtuális merevlemezt a hibaelhárítási virtuális géphez csatlakoztató bérlet fel nem szabadul.

1. Az SSH-munkamenettől a hibaelhárítási virtuális gépig válassza le a meglévő virtuális merevlemezt. Először váltson ki a csatlakoztatási pont szülőkönyvtárából:

    ```bash
    cd /
    ```

    Most leválasztani a meglévő virtuális merevlemezt. A következő példa leszereli `/dev/sdc1`az eszközt a következő helyen:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Most válassza le a virtuális merevlemezt a virtuális gépről. Lépjen ki az SSH-munkamenetből a hibaelhárítási virtuális géphez:

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>Az érintett virtuális gép operációsrendszer-lemezének módosítása

Az Azure CLI segítségével cserélheti fel az operációs rendszer lemezeit. Nem kell törölnie, és újra létre kell hoznia a virtuális gép.

Ez a példa leállítja a megnevezett virtuális gép, `myVM` és hozzárendeli a lemezt, `myNewOSDisk` mint az új operációsrendszer-lemez.

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>További lépések
Ha problémái vannak a virtuális géphez való csatlakozással, olvassa [el az SSH-kapcsolatok hibaelhárítása azure-beli virtuális géphez című témakört.](troubleshoot-ssh-connection.md) A virtuális számítógépen futó alkalmazások elérésével kapcsolatos problémákról a [Linux-alapú virtuális gépeken az alkalmazáscsatlakozási problémák elhárítása című](troubleshoot-app-connection.md)témakörben nyújt fel problémát.


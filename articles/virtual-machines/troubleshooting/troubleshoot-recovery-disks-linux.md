---
title: Használja a hibaelhárító virtuális Géphez az Azure CLI-vel Linux |} A Microsoft Docs
description: Ismerje meg az operációsrendszer-lemez egy helyreállítási virtuális Géphez az Azure CLI-vel való csatlakoztatásával a Linux rendszerű virtuális gép hibáinak elhárítása
services: virtual-machines-linux
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: e6d6c47726b21a241b379366bd1fde6c6b90e223
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462012"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Az operációsrendszer-lemez egy helyreállítási virtuális Géphez az Azure CLI-vel való csatlakoztatásával Linuxos virtuális gépek hibaelhárítása
Ha a Linux rendszerű virtuális gép (VM) indítási vagy hiba fordul, szükség lehet a hibaelhárítási lépések végrehajtásához a virtuális merevlemezen magát. Ilyenek például a bejegyzés érvénytelen lenne `/etc/fstab` , amely megakadályozza, hogy a virtuális gép képes arra, hogy sikeresen. Ez a cikk részletesen bemutatja a virtuális merevlemez egy másik Linux rendszerű virtuális géphez, javítsa ki a hibákat, majd hozza létre újból az eredeti virtuális gép csatlakoztatása az Azure CLI használatával. 


## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Törölje a virtuális gép problémái, így a virtuális merevlemezek.
2. Csatolja, és a egy másik Linux rendszerű virtuális géphez a virtuális merevlemez csatlakoztatása hibaelhárítás céljából.
3. Kapcsolódjon a hibaelhárítást végző virtuális gépre. A fájlok szerkesztésével, vagy minden olyan eszközök futtatásával javítsa problémák az eredeti virtuális merevlemezen.
4. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
5. Hozzon létre egy virtuális Gépet az eredeti virtuális merevlemez használatával.

A virtuális gép felügyelt lemez, amely használja, lásd: [egy felügyelt lemez virtuális Gépet egy új operációsrendszer-lemez csatlakoztatásával végzett hibaelhárítását](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

Ezeket a hibaelhárítási lépések végrehajtásához szüksége a legújabb [Azure CLI-vel](/cli/azure/install-az-cli2) telepítve, és bejelentkezett egy Azure-fiókba az [az bejelentkezési](/cli/azure/reference-index).

A következő példákban cserélje le a saját értékeire paraméterek nevei. Példa a paraméter nevek a következők `myResourceGroup`, `mystorageaccount`, és `myVM`.


## <a name="determine-boot-issues"></a>Rendszerindítási problémák meghatározása
Vizsgálja meg a soros kimenetet határozza meg, miért érdemes a virtuális gép nem áll lehetségesnek megfelelően. Ilyenek például egy érvénytelen bejegyzés `/etc/fstab`, vagy az alapjául szolgáló virtuális merevlemez folyamatban törölték vagy áthelyezték.

A rendszerindítási naplók lekérése [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log). Az alábbi példa lekéri a soros kimeneti nevű virtuális gépről `myVM` az erőforráscsoport neve `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Tekintse át a soros kimenetet határozza meg, miért a virtuális gép rendszerindító sikertelen. Ha a soros kimeneti a végrehajtás nem biztosít, szükség lehet tekintse át a naplófájlokat `/var/log` után a virtuális merevlemezt a hibaelhárító virtuális Géphez csatlakozik.


## <a name="view-existing-virtual-hard-disk-details"></a>Meglévő virtuális merevlemez részleteinek megtekintése
A virtuális merevlemez (VHD) csatlakoztathat egy másik virtuális Géphez, mielőtt az URI-ját az operációsrendszer-lemez azonosítania kell. 

A virtuális gép adatainak megtekintése [az vm show](/cli/azure/vm#az_vm_show). Használja a `--query` bontsa ki az URI-t az operációsrendszer-lemez jelzőt. Az alábbi példa lekéri a virtuális gép nevű lemezadatai `myVM` az erőforráscsoport neve `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

Az URI-ja hasonló **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd**.

## <a name="delete-existing-vm"></a>Meglévő virtuális gép törlése
A virtuális merevlemezek és a virtuális gépek az Azure-erőforrások két különböző típusa. Virtuális merevlemez, az operációs rendszer magát, alkalmazások és konfigurációk tárolására. A virtuális gépre a csak metaadatokat, amelyek a méretét vagy a hely határozza meg, és az erőforrások, például egy virtuális merevlemezt vagy virtuális hálózati kártya (NIC) hivatkozik. Minden egyes virtuális merevlemezhez egy bérletet, amikor egy virtuális Géphez csatolva van. Bár az adatlemezek akkor is csatlakoztathatók és leválaszthatók, amikor a virtuális gép üzemel, az operációs rendszer merevlemeze nem csatlakoztatható le, hacsak nem törli a VM-erőforrást. A bérlet továbbra is az operációsrendszer-lemez társíthat egy virtuális Gépet, akkor is, ha a virtuális gép leállított vagy felszabadított állapotban van.

Az első lépés a virtuális gép helyreállításához, ami törli magát a VM-erőforrás. A virtuális gép törlésével a virtuális merevlemezek a tárfiókban maradnak. A virtuális gép törlését követően a virtuális merevlemez csatlakoztatása egy másik virtuális géphez a hibák elhárítására.

Törölje a virtuális Gépet a [az virtuális gép törlése](/cli/azure/vm). Az alábbi példával törölhet nevű virtuális Gépet `myVM` az erőforráscsoportból nevű `myResourceGroup`:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Várjon, amíg a virtuális gép törlése a virtuális merevlemezt egy másik virtuális géphez csatolása előtt befejeződött. A bérlet társítja azt a virtuális gép a virtuális merevlemezen kell megjelenése előtt a virtuális merevlemezt egy másik virtuális géphez.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Meglévő virtuális merevlemez csatlakoztatása egy másik virtuális géphez
A következő néhány lépést, egy másik virtuális Géphez hibaelhárítás céljából használja. A meglévő virtuális merevlemez csatlakoztatása a hibaelhárító virtuális Géphez, és szerkessze a lemez tartalma. Ez a folyamat lehetővé teszi, hogy javítsa az esetleges konfigurációs hibákat, vagy további alkalmazás vagy a rendszer naplófájljait, például tekintse át. Válassza ki, vagy hozzon létre egy másik virtuális Géphez hibaelhárítás céljából használja.

A meglévő virtuális merevlemez csatlakoztatása [az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_attach). Ha a meglévő virtuális merevlemezt csatlakoztat, adja meg az URI-t a lemezt az előző kapott `az vm show` parancsot. Az alábbi példa csatlakoztat egy meglévő virtuális merevlemezt a hibaelhárító virtuális géphez nevű `myVMRecovery` az erőforráscsoport neve `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>A csatlakoztatott lemez csatlakoztatása

> [!NOTE]
> Az alábbi példák bemutatják, milyen lépések szükségesek egy Ubuntu virtuális gépen. Ha a különböző Linux-disztribúció, például a Red Hat Enterprise Linux vagy a SUSE, használja a napló fájlhelyekhez és `mount` parancsok kissé eltérő lehet. Tekintse meg a megfelelő parancsokat változásairól az adott disztribúció dokumentációjában.

1. Ssh-KAPCSOLATOT a hibaelhárító virtuális Géphez a megfelelő hitelesítő adatokkal. Ha ezt a lemezt a hibaelhárító virtuális Géphez csatolt első adatlemez, a lemez valószínűleg csatlakozik `/dev/sdc`. Használat `dmseg` csatlakoztatott lemezek megtekintéséhez:

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
Miután a hibák megoldódnak, válassza le, és le a meglévő virtuális merevlemezt a hibaelhárító virtuális gépről. Nem használhat a virtuális merevlemez más virtuális gép mindaddig, amíg a virtuális merevlemez csatlakoztatása a hibaelhárító virtuális géphez bérlet.

1. Az SSH-munkamenetből a hibaelhárító virtuális géphez válassza le a meglévő virtuális merevlemezt. Először ki a csatlakoztatási pont szülőkönyvtárában módosítása:

    ```bash
    cd /
    ```

    Most válassza le a meglévő virtuális merevlemezt. Az alábbi példa leválasztja az eszközön lévő `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Most már le a virtuális merevlemezt a virtuális gépről. Lépjen ki az SSH-munkamenetből a hibaelhárító virtuális géphez. A csatlakoztatott adatlemezekkel, a hibaelhárító virtuális géphez a listában [az vm unmanaged-disk list](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_list). Az alábbi példa felsorolja a nevű virtuális géphez csatolt adatlemezek `myVMRecovery` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Jegyezze fel a meglévő virtuális merevlemez nevét. Például egy lemez neve és URI-ját **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** van **myVHD**. 

    A virtuális gépről az adatlemez leválasztása [az vm unmanaged-disk detach](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_detach). Az alábbi példa leválasztja a lemezt nevű `myVHD` nevű virtuális gépről `myVMRecovery` a a `myResourceGroup` erőforráscsoportot:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Virtuális gép létrehozása az eredeti merevlemezről
Egy virtuális Gépet hozhat létre az eredeti virtuális merevlemez [ezen Azure Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). A tényleges JSON-sablon jelenleg a következő hivatkozásra:

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

A sablon üzembe helyezi a virtuális gép korábbi parancsot a virtuális merevlemez URI használatával. Helyezze üzembe a sablont [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az_group_deployment_create). Adja meg az URI-t az eredeti virtuális merevlemezen, majd adja meg az operációs rendszer típusa, a virtuális gép mérete és a virtuális gép neve a következő:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Engedélyezze újra a rendszerindítási diagnosztika
Amikor a meglévő virtuális merevlemezről hoz létre a virtuális gép, a rendszerindítási diagnosztika lehetséges, hogy nem automatikusan engedélyezni. A rendszerindítási diagnosztika engedélyezése [az vm boot-diagnostics engedélyezése](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). A következő példában engedélyezzük a diagnosztikai bővítmény nevű virtuális gépre `myDeployedVM` az erőforráscsoport neve `myResourceGroup`:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Egy felügyelt lemez virtuális Gépet egy új operációsrendszer-lemez csatlakoztatásával végzett hibaelhárítását
1. Állítsa az érintett virtuális Gépeket.
2. [Hozzon létre egy felügyelt lemez pillanatképének](../linux/snapshot-copy-managed-disk.md) felügyelt lemezt a virtuális gép operációsrendszer-lemezről.
3. [Felügyelt lemez létrehozása pillanatképből a](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [A felügyelt lemez csatolása a virtuális gép adatlemezként](../windows/attach-disk-ps.md).
5. [Módosítsa az adatlemezt a 4. lépéssel operációsrendszer-lemez](../windows/os-disk-swap.md).

## <a name="next-steps"></a>További lépések
Ha a virtuális Géphez való csatlakozással kapcsolatos problémákat tapasztal, tekintse meg [hibaelhárítása SSH csatlakozhat egy Azure virtuális gép](troubleshoot-ssh-connection.md). A virtuális Gépen futó alkalmazások elérésével kapcsolatos problémák, lásd: [egy Linux rendszerű virtuális gépen alkalmazások csatlakozási hibáinak elhárítása](troubleshoot-app-connection.md).


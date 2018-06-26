---
title: A Linux virtuális gép és az Azure CLI 2.0 hibaelhárítási használata |} Microsoft Docs
description: Ismerje meg, az operációs rendszer lemezének csatlakozva egy helyreállítási virtuális gépet az Azure CLI 2.0 Linux virtuális gép kapcsolatos problémák elhárítása
services: virtual-machines-linux
documentationCenter: ''
authors: iainfoulds
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: iainfou
ms.openlocfilehash: 1b7c887be67d5d1a209f1647b567f5659f99fb44
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938229"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli-20"></a>Linux virtuális gép által a operációsrendszer-lemez csatolása a helyreállítási virtuális Gépet az Azure CLI 2.0 hibaelhárítása
Ha a Linux virtuális gép (VM) rendszerindító vagy a lemez hibát tapasztal, szükség lehet végezze el a virtuális merevlemez hibaelhárítási lépéseket. Ilyenek például a bejegyzés érvénytelen lenne `/etc/fstab` , amely megakadályozza a virtuális gép rendszerindító sikeresen megtörtént. Ez a cikk részletezi az Azure CLI 2.0 másik Linux virtuális gép, javítsa ki a hibákat, majd hozza létre újból az eredeti virtuális gép csatlakozni a virtuális merevlemez használata. 


## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Törölje a virtuális gép problémák észlelése, a virtuális merevlemezek tartása.
2. Csatolja, és csatlakoztassa a virtuális merevlemez egy másik Linux VM hibaelhárítási célból.
3. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Szerkesztheti a fájlokat, vagy futtassa a problémák megoldásával kapcsolatban az eredeti virtuális merevlemez olyan eszközöket.
4. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
5. Az eredeti virtuális merevlemez virtuális gép létrehozása.

A virtuális gép számára, amely felügyelt lemezt használ, tekintse meg [hibaelhárításáról egy kezelt lemez virtuális Gépet egy új operációsrendszer-lemez csatolása](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

Hajtsa végre az alábbi lépéseket, a legújabb kell [Azure CLI 2.0](/cli/azure/install-az-cli2) telepítve, és bejelentkezett az Azure-fiók használatával [az bejelentkezési](/cli/azure/reference-index#az_login).

A következő példákban cserélje le a saját értékeit paraméterek nevei. Példa paraméter nevek a következők `myResourceGroup`, `mystorageaccount`, és `myVM`.


## <a name="determine-boot-issues"></a>Határozza meg a rendszerindítási problémák
Vizsgálja meg a soros kimenete annak megállapításához, miért a virtuális gép nem végezhetnek rendszerindítást megfelelően. Ilyenek például a bejegyzés érvénytelen `/etc/fstab`, vagy az alapul szolgáló virtuális merevlemezek áthelyezése vagy törölhetők.

A rendszerindító naplófájlok rendelkező [az virtuális gép rendszerindítási-diagnosztika get-rendszerindítási-naplófájl](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log). Az alábbi példa lekérdezi a soros kimeneti nevű virtuális gép `myVM` az erőforráscsoport neve `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Tekintse át a soros kimenetet meghatározni, miért a virtuális gép rendszerindító sikertelenek lesznek. A soros kimeneti bármely arra utal, hogy nem ad meg, ha szeretne nézze meg a naplófájlokat `/var/log` után a virtuális merevlemez, egy hibaelhárítási Virtuálisgép kapcsolódik.


## <a name="view-existing-virtual-hard-disk-details"></a>Meglévő virtuális merevlemez részleteinek megtekintése
A virtuális merevlemez (VHD) egy másik virtuális gép csatolhat, mielőtt kell azonosítani az URI-azonosítója az operációsrendszer-lemezképet. 

Tekintse meg a virtuális Gépet a [az vm megjelenítése](/cli/azure/vm#az_vm_show). Használja a `--query` jelzőjét, hogy az URI-t az operációs rendszer lemezének kibontásához. Az alábbi példa lekérdezi a nevű virtuális gép lemezeinek adatai `myVM` az erőforráscsoport neve `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

Az URI hasonlít **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd**.

## <a name="delete-existing-vm"></a>Meglévő virtuális gép törlése
A virtuális merevlemezek és a virtuális gépek az Azure-erőforrások két különböző típusa. A virtuális merevlemez, az operációs rendszert illeti, alkalmazások és konfigurációk tárolására. A virtuális gép magát a csak metaadatokat, amelyek a méretét vagy a hely határozza meg, és hivatkozik arra az erőforrások, például egy virtuális merevlemezt vagy virtuális hálózati kártya (NIC). Minden virtuális merevlemez létrehozásakor kell a virtuális Géphez csatlakozik, a címbérlet rendelkezik. Bár az adatlemezek akkor is csatlakoztathatók és leválaszthatók, amikor a virtuális gép üzemel, az operációs rendszer merevlemeze nem csatlakoztatható le, hacsak nem törli a VM-erőforrást. A bérlet továbbra is fennáll, az operációs rendszer lemezének társítandó egy virtuális Gépet, akkor is, ha ezt a virtuális Gépet felszabadított és leállított állapotban van.

Az első lépés a virtuális gép helyreállításához, hogy törli a virtuális gép erőforrásához magát. A virtuális gép törlésével a virtuális merevlemezek a tárfiókban maradnak. A virtuális gép törlését követően a virtuális merevlemez csatlakoztatása egy másik virtuális géphez, és javítsa ki a hibákat.

Törölje a virtuális Géphez a [az vm törlése](/cli/azure/vm#az_vm_delete). A következő példa törli a virtuális gép nevű `myVM` az erőforráscsoportból nevű `myResourceGroup`:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Várjon, amíg a virtuális gép törlése a virtuális merevlemez egy másik virtuális géphez csatolása előtt befejeződött. A virtuális merevlemezen, amely a virtuális Gépet társít a címbérlet kell helyezni, előtt a virtuális merevlemez egy másik virtuális géphez.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Meglévő virtuális merevlemez egy másik virtuális géphez csatolása
A következő néhány lépést, a másik virtuális gép a hibaelhárításhoz használja. A meglévő virtuális merevlemez csatlakoztatása a hibaelhárítási virtuális géppel, keresse meg és a lemez tartalmának szerkesztéséhez. Ez a folyamat teszi javíthatja az esetleges konfigurációs hibákat, vagy tekintse át például további alkalmazás vagy a rendszer naplófájljait. Válassza ki vagy hozzon létre egy másik virtuális Gépet a hibaelhárításhoz használja.

A meglévő virtuális merevlemez csatlakoztatása [nem felügyelt az virtuálisgép-lemez csatolása](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_attach). A meglévő virtuális merevlemez csatolása, adja meg az URI-t a lemezt az előző kapott `az vm show` parancsot. Az alábbi példa csatolja a létező virtuális merevlemezt a hibaelhárítási nevű virtuális gép `myVMRecovery` az erőforráscsoport neve `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>A csatolt adatlemez csatlakoztatása

> [!NOTE]
> A következő példák részletesen az Ubuntu virtuális gép indításához szükséges lépéseket. Red Hat Enterprise Linux és SUSE, például a különböző Linux distro használata a naplófájl helyét és `mount` parancsok kissé eltérő lehet. Tekintse meg az adott distro a parancsok a megfelelő változásokat a dokumentációját.

1. SSH-kapcsolatot a hibaelhárítási virtuális Gépet a megfelelő hitelesítő adatokkal. Ha ezt a lemezt az első adatok lemezt, a hibaelhárítási Virtuálisgép kapcsolódik, a lemez valószínűleg csatlakozik-e `/dev/sdc`. Használjon `dmseg` csatlakoztatott lemezek megtekintéséhez:

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

    A fenti példában az operációsrendszer-lemezképet jelenleg `/dev/sda` és az egyes virtuális gépek a megadott ideiglenes lemez `/dev/sdb`. Ha több adatlemezek, hogy legyen `/dev/sdd`, `/dev/sde`, és így tovább.

2. Hozzon létre egy könyvtárat, a meglévő virtuális merevlemez csatlakoztatása. Az alábbi példa létrehoz egy könyvtárat nevű `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Ha több partíciót a meglévő virtuális merevlemezen, csatlakoztassa a szükséges partíciót. Az alábbi példa csatlakoztatja, az első elsődleges partíció `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Ajánlott eljárás az adatlemezek csatlakoztatása az Azure-ban univerzálisan egyedi azonosítóját (UUID) a virtuális merevlemez virtuális gépeken. A jelen rövid hibaelhárítási esetben csatlakoztatni a virtuális merevlemez használatával UUID nincs szükség. Azonban a normál használja, a Szerkesztés `/etc/fstab` csatlakoztatni a virtuális merevlemezek UUID helyett eszköznév okozhat a virtuális gépek a rendszerindítás.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Az eredeti virtuális merevlemez kapcsolatos problémák megoldása
A meglévő virtuális merevlemezzel csatlakoztatva is képes lemezvizsgálatok elvégzésére bármely karbantartási és hibaelhárítási lépéseket, igény szerint. Miután végzett a hibák javításával, folytassa az alábbi lépésekkel.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Válassza le a lemezképet, és válassza le az eredeti virtuális merevlemez
Ha a hibák fakadó problémák megoldásával válassza le, és a meglévő virtuális merevlemez válassza le a hibaelhárítási virtuális Gépet. A virtuális merevlemez nem használható a többi virtuális Géphez, amíg a címbérlet, a virtuális merevlemez csatolását a hibaelhárítási VM.

1. Az SSH-munkamenetet a hibaelhárítási virtuális gépre, a leválasztani a meglévő virtuális merevlemez. Először módosítsa a csatlakoztatási pont szülőkönyvtárában kívül:

    ```bash
    cd /
    ```

    Most leválasztani a meglévő virtuális merevlemez. Az alábbi példa leválasztja az eszköz `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Most válassza le a virtuális merevlemezt a virtuális gépről. Kilépés az SSH-munkamenetet a hibaelhárítási virtuális Gépet. A mellékelt adatok a hibaelhárítási tulajdonsággal rendelkező virtuális lemezein listában [az virtuálisgép-lemez nem felügyelt lista](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_list). Az alábbi példa felsorolja a adatok nevű virtuális gép csatlakoztatott lemezei `myVMRecovery` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Vegye figyelembe a meglévő virtuális merevlemez nevét. Például az egy lemez neve az URI-azonosítójú **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** van **myVHD**. 

    Válassza le a lemezt a virtuális gép adatok [nem felügyelt az virtuálisgép-lemez leválasztása](/cli/azure/vm/unmanaged-disk#az_vm_unmanaged_disk_detach). Az alábbi példa leválasztja a lemezt nevű `myVHD` nevű virtuális gép `myVMRecovery` a a `myResourceGroup` erőforráscsoport:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Virtuális gép eredeti merevlemez létrehozása
Egy virtuális Gépet hozhat létre az eredeti virtuális merevlemez [Azure Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). A tényleges JSON-sablon jelenleg a következő hivatkozásra:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json

A sablont egy virtuális Gépet a virtuális merevlemez URI a korábbi parancs használatával telepíti. A sablon telepítéséhez [az csoport központi telepítésének létrehozása](/cli/azure/group/deployment#az_group_deployment_create). Adja meg az URI az eredeti virtuális Merevlemezt, majd adja meg az operációsrendszer-típus, a Virtuálisgép-méretet és a virtuális gép nevét az alábbiak szerint:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Engedélyezze újra a rendszerindítási diagnosztika
Amikor a virtuális Gépet hoz létre a meglévő virtuális merevlemez, rendszerindítási diagnosztika automatikusan nem lehet engedélyezni. A rendszerindítási diagnosztika engedélyezése [az virtuális gép rendszerindítási-diagnosztika engedélyezése](/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable). A következő példában engedélyezzük a diagnosztikai kiterjesztés nevű virtuális gép `myDeployedVM` az erőforráscsoport neve `myResourceGroup`:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Egy kezelt lemez virtuális Gépet egy új operációsrendszer-lemez csatolásával hibaelhárítása
1. Állítsa le az érintett kezelt lemez Windows virtuális Gépet.
2. [Hozzon létre egy felügyelt lemezes pillanatképet](../windows/snapshot-copy-managed-disk.md) , az operációsrendszer-lemezképet, a felügyelt lemezt a virtuális gépet.
3. [Hozzon létre egy felügyelt lemezes a pillanatképből](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [A felügyelt lemezt csatolni a virtuális gép adatok lemezként](../windows/attach-disk-ps.md).
5. [4. lépés a adatlemez váltson az operációsrendszer-lemez](../windows/os-disk-swap.md).

## <a name="next-steps"></a>További lépések
Ha a virtuális Géphez való kapcsolódás problémát tapasztal, tekintse meg [hibaelhárítása SSH kapcsolatok egy Azure virtuális gépre](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A virtuális gépen futó alkalmazások elérésével problémákkal kapcsolatban lásd: [alkalmazás csatlakozási problémák a Linux virtuális gép](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
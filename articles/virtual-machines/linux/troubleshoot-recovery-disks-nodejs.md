---
title: Használja a Linux virtuális gép és az Azure CLI 1.0 hibaelhárítási |} Microsoft Docs
description: Ismerje meg, az operációs rendszer lemezének csatlakozva egy helyreállítási virtuális gépet az Azure CLI 1.0 Linux virtuális gép kapcsolatos problémák elhárítása
services: virtual-machines-linux
documentationCenter: ''
authors: iainfoulds
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 047d0041fa89fa480de0744e594b8ac4f974973a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915201"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-cli-10"></a>Linux virtuális gép által a operációsrendszer-lemez csatolása a helyreállítási virtuális gépet az Azure CLI 1.0 hibaelhárítása
Ha a Linux virtuális gép (VM) rendszerindító vagy a lemez hibát tapasztal, szükség lehet végezze el a virtuális merevlemez hibaelhárítási lépéseket. Ilyenek például a bejegyzés érvénytelen lenne `/etc/fstab` , amely megakadályozza a virtuális gép rendszerindító sikeresen megtörtént. Ez a cikk részletezi az Azure CLI 1.0 másik Linux virtuális gép, javítsa ki a hibákat, majd hozza létre újból az eredeti virtuális gép csatlakozni a virtuális merevlemez használata.


## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A következő CLI-verziók egyikével elvégezheti a feladatot:

- [Az Azure CLI 1.0](#recovery-process-overview) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](../windows/troubleshoot-recovery-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.


## <a name="recovery-process-overview"></a>Helyreállítási folyamat áttekintése
A hibaelhárítási folyamat a következő:

1. Törölje a virtuális gép problémák észlelése, a virtuális merevlemezek tartása.
2. Csatolja, és csatlakoztassa a virtuális merevlemez egy másik Linux VM hibaelhárítási célból.
3. Kapcsolódjon a hibaelhárítást végző virtuális gépre. Szerkesztheti a fájlokat, vagy futtassa a problémák megoldásával kapcsolatban az eredeti virtuális merevlemez olyan eszközöket.
4. Válassza le a virtuális merevlemezt a hibaelhárító virtuális gépről.
5. Az eredeti virtuális merevlemez virtuális gép létrehozása.

Győződjön meg arról, hogy rendelkezik [a legújabb Azure CLI 1.0](../../cli-install-nodejs.md) telepítve és bejelentkezett és erőforrás-kezelő módban:

```azurecli
azure config mode arm
```

A következő példákban cserélje le a saját értékeit paraméterek nevei. Példa paraméter nevek a következők `myResourceGroup`, `mystorageaccount`, és `myVM`.


## <a name="determine-boot-issues"></a>Határozza meg a rendszerindítási problémák
Vizsgálja meg a soros kimenete annak megállapításához, miért a virtuális gép nem végezhetnek rendszerindítást megfelelően. Ilyenek például a bejegyzés érvénytelen `/etc/fstab`, vagy az alapul szolgáló virtuális merevlemezek áthelyezése vagy törölhetők.

Az alábbi példa lekérdezi a soros kimeneti nevű virtuális gép `myVM` az erőforráscsoport neve `myResourceGroup`:

```azurecli
azure vm get-serial-output --resource-group myResourceGroup --name myVM
```

Tekintse át a soros kimenetet meghatározni, miért a virtuális gép rendszerindító sikertelenek lesznek. A soros kimeneti bármely arra utal, hogy nem ad meg, ha szeretne nézze meg a naplófájlokat `/var/log` után a virtuális merevlemez, egy hibaelhárítási Virtuálisgép kapcsolódik.


## <a name="view-existing-virtual-hard-disk-details"></a>Meglévő virtuális merevlemez részleteinek megtekintése
A virtuális merevlemez egy másik virtuális gép csatolhat, mielőtt kell nevét, a virtuális merevlemez (VHD). 

Az alábbi példa nevű virtuális gép adatainak beolvasása `myVM` az erőforráscsoport neve `myResourceGroup`:

```azurecli
azure vm show --resource-group myResourceGroup --name myVM
```

Keressen `Vhd URI` az előző parancs kimenetét. A következő példa kimenet látható csonkolja a `Vhd URI` utolsó sorában:

```azurecli
info:    Executing command vm show
+ Looking up the VM "myVM"
+ Looking up the NIC "myNic"
+ Looking up the public ip "myPublicIP"
...
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :myVM
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/myVM201610292712.vhd
```


## <a name="delete-existing-vm"></a>Meglévő virtuális gép törlése
A virtuális merevlemezek és a virtuális gépek az Azure-erőforrások két különböző típusa. A virtuális merevlemez, az operációs rendszert illeti, alkalmazások és konfigurációk tárolására. A virtuális gép magát a csak metaadatokat, amelyek a méretét vagy a hely határozza meg, és hivatkozik arra az erőforrások, például egy virtuális merevlemezt vagy virtuális hálózati kártya (NIC). Minden virtuális merevlemez létrehozásakor kell a virtuális Géphez csatlakozik, a címbérlet rendelkezik. Bár az adatlemezek akkor is csatlakoztathatók és leválaszthatók, amikor a virtuális gép üzemel, az operációs rendszer merevlemeze nem csatlakoztatható le, hacsak nem törli a VM-erőforrást. A bérlet továbbra is fennáll, az operációs rendszer lemezének társítandó egy virtuális Gépet, akkor is, ha ezt a virtuális Gépet felszabadított és leállított állapotban van.

Az első lépés a virtuális gép helyreállításához, hogy törli a virtuális gép erőforrásához magát. A virtuális gép törlésével a virtuális merevlemezek a tárfiókban maradnak. A virtuális gép törlését követően a virtuális merevlemez csatlakoztatása egy másik virtuális géphez, és javítsa ki a hibákat.

A következő példa törli a virtuális gép nevű `myVM` az erőforráscsoportból nevű `myResourceGroup`:

```azurecli
azure vm delete --resource-group myResourceGroup --name myVM 
```

Várjon, amíg a virtuális gép törlése a virtuális merevlemez egy másik virtuális géphez csatolása előtt befejeződött. A virtuális merevlemezen, amely a virtuális Gépet társít a címbérlet kell helyezni, előtt a virtuális merevlemez egy másik virtuális géphez.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Meglévő virtuális merevlemez egy másik virtuális géphez csatolása
A következő néhány lépést, a másik virtuális gép a hibaelhárításhoz használja. A meglévő virtuális merevlemez csatlakoztatása a hibaelhárítási virtuális géppel, keresse meg és a lemez tartalmának szerkesztéséhez. Ez a folyamat teszi javíthatja az esetleges konfigurációs hibákat, vagy tekintse át például további alkalmazás vagy a rendszer naplófájljait. Válassza ki vagy hozzon létre egy másik virtuális Gépet a hibaelhárításhoz használja.

A meglévő virtuális merevlemez csatolása, adja meg a lemezt az előző beszerzett URL-címe `azure vm show` parancsot. Az alábbi példa csatolja a létező virtuális merevlemezt a hibaelhárítási nevű virtuális gép `myVMRecovery` az erőforráscsoport neve `myResourceGroup`:

```azurecli
azure vm disk attach --resource-group myResourceGroup --name myVMRecovery \
    --vhd-url https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
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

2. Most válassza le a virtuális merevlemezt a virtuális gépről. Kilépés az SSH-munkamenetet a hibaelhárítási virtuális Gépet. Az Azure parancssori felület először felsorolja a mellékelt adatok lemezeket a hibaelhárítási virtuális géphez. Az alábbi példa felsorolja a adatok nevű virtuális gép csatlakoztatott lemezei `myVMRecovery` az erőforráscsoport neve `myResourceGroup`:

    ```azurecli
    azure vm disk list --resource-group myResourceGroup --vm-name myVMRecovery
    ```

    Megjegyzés: a `Lun` értékét a meglévő virtuális merevlemez. A következő példa parancs kimenete LUN azonosítójú 0 csatolni a meglévő virtuális lemez jeleníti meg:

    ```azurecli
    info:    Executing command vm disk list
    + Looking up the VM "myVMRecovery"
    data:    Name              Lun  DiskSizeGB  Caching  URI
    data:    ------            ---  ----------  -------  ------------------------------------------------------------------------
    data:    myVM              0                None     https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    info:    vm disk list command OK
    ```

    Válassza le a lemezt a virtuális gépet a megfelelő adatok `Lun` érték:

    ```azurecli
    azure vm disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --lun 0
    ```


## <a name="create-vm-from-original-hard-disk"></a>Virtuális gép eredeti merevlemez létrehozása
Egy virtuális Gépet hozhat létre az eredeti virtuális merevlemez [Azure Resource Manager sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). A tényleges JSON-sablon jelenleg a következő hivatkozásra:

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json

A sablont egy virtuális Gépet létrehozni meglévő virtuális hálózatban, a virtuális merevlemez URL-CÍMÉT a korábbi parancs használatával központilag telepíti. A következő példa telepíti a sablon nevű erőforráscsoport `myResourceGroup`:

```azurecli
azure group deployment create --resource-group myResourceGroup --name myDeployment \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

Válaszolja meg az utasításokat a sablonhoz, például a virtuális gép nevét (`myDeployedVM` az alábbi példában), az operációs rendszer típusa (`Linux`), és a Virtuálisgép-méretet (`Standard_DS1_v2`). A `osDiskVhdUri` van azonos az előzőleg használt csatolni a meglévő virtuális merevlemez a hibaelhárítási virtuális géphez. A parancs kimenetében és felszólítás például a következőképpen történik:

```azurecli
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName:  myDeployedVM
osType:  Linux
osDiskVhdUri:  https://mystorageaccount.blob.core.windows.net/vhds/myVM201610292712.vhd
vmSize:  Standard_DS1_v2
existingVirtualNetworkName:  myVnet
existingVirtualNetworkResourceGroup:  myResourceGroup
subnetName:  mySubnet
dnsNameForPublicIP:  mypublicipdeployed
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "mydeployment"
+ Waiting for deployment to complete
+
```


## <a name="re-enable-boot-diagnostics"></a>Engedélyezze újra a rendszerindítási diagnosztika

Amikor a virtuális Gépet hoz létre a meglévő virtuális merevlemez, rendszerindítási diagnosztika automatikusan nem lehet engedélyezni. A következő példában engedélyezzük a diagnosztikai kiterjesztés nevű virtuális gép `myDeployedVM` az erőforráscsoport neve `myResourceGroup`:

```azurecli
azure vm enable-diag --resource-group myResourceGroup --name myDeployedVM
```

## <a name="next-steps"></a>További lépések
Ha a virtuális Géphez való kapcsolódás problémát tapasztal, tekintse meg [hibaelhárítása SSH kapcsolatok egy Azure virtuális gépre](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A virtuális gépen futó alkalmazások elérésével problémákkal kapcsolatban lásd: [alkalmazás csatlakozási problémák a Linux virtuális gép](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
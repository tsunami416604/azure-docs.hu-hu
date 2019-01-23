---
title: Linux virtuális gépek – az Azure data lemez leválasztása |} A Microsoft Docs
description: Ismerje meg, leválasztása adatlemez egy virtuális gépről az Azure-ban az Azure CLI-vel vagy az Azure Portalon.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 07/18/2018
ms.author: cynthn
ms.component: disks
ms.openlocfilehash: 5a0c2f91c5290e679cc2a3ac03cbf226ceb902da
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477702"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>A Linux rendszerű virtuális gépről adatlemez leválasztása

Ha már nincs szüksége egy virtuális géphez csatolt adatlemezre, könnyedén leválaszthatja. Ez eltávolítja a lemezt a virtuális gépről, de ez nem távolítja el a storage-ból. Ebben a cikkben egy Ubuntu LTS 16.04 elosztásáról dolgozunk. Ha egy másik terjesztési használ, a következő útmutatót: a lemez leválasztása ettől eltérő lehet.

> [!WARNING]
> Ha leválaszt egy lemezt, nem törlődnek automatikusan. Ha prémium szintű storage, amelyre Ön feliratkozott, továbbra is a lemez tárolási költségeket fizetni. További információkért lásd: [árak és számlázás a Premium Storage használatakor](../windows/premium-storage.md#pricing-and-billing). 
> 
> 

Ha ismét használni szeretné a lemezen lévő adatokat, újból csatolhatja ugyanahhoz vagy egy másik virtuális géphez.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Csatlakozzon a virtuális Géphez, a lemez leválasztása

Mielőtt leválaszthatna a lemez CLI vagy a portál használatával, meg kell választanunk a lemez és mutató hivatkozásokat Ha a az fstab fájlról.

Csatlakozzon a virtuális géphez. Ebben a példában a virtuális gép nyilvános IP-cím áll *10.0.1.4 cím – ez* , amelynek *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Először keresse meg az adatlemezt, amely a leválasztani kívánt. Az alábbi példában dmesg szűrjön az SCSI-lemezek:

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

Itt *sdc* a lemez, amely a leválasztani kívánt. A lemez UUID is kell helyezésre.

```bash
sudo -i blkid
```

A kimenet a következő példához hasonlóan néz ki:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Szerkessze a */etc/fstab* fájlt a lemezre mutató hivatkozások eltávolításához. 

> [!NOTE]
> Nem megfelelően szerkesztése a **/etc/fstab** fájl meghiúsulását eredményezheti. Ha nem tudja biztosan, tekintse meg a telepítési dokumentációban talál információkat megfelelően szerkesztése ezt a fájlt. Emellett javasoljuk, hogy a /etc/fstab fájl biztonsági másolata jön létre szerkesztése előtt.

Nyissa meg a */etc/fstab* módon fájlt egy szövegszerkesztőben:

```bash
sudo vi /etc/fstab
```

Ebben a példában a következő sort kell törölni kell a */etc/fstab* fájlt:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Használat `umount` leválasztani a lemezt. Az alábbi példa leválasztja a */dev/sdc1* történő particionálása a */datadrive* csatlakoztatási pont:

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Azure CLI-vel adatlemez leválasztása 

Ez a példa leválasztja a *myDataDisk* nevű virtuális gép lemeze *myVM* a *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

A lemez storage-ban marad, de már nincs csatolva egy virtuális géphez.


## <a name="detach-a-data-disk-using-the-portal"></a>Adatlemez leválasztása a portállal

1. A bal oldali menüben válassza ki a **virtuális gépek**.
2. Válassza ki a virtuális gépen, amelyen az adatlemezt, leválasztja, majd kattintson a kívánt **leállítása** felszabadítani a virtuális Gépet.
3. A virtuális gép panelén válassza **lemezek**.
4. Felső részén a **lemezek** ablaktáblán válassza előbb **szerkesztése**.
5. Az a **lemezek** ablaktáblán az adatlemezt, amelyeket szeretne leválasztása, kattintson a jobb szélén a ![leválasztási gomb képe](./media/detach-disk/detach.png) Leválasztás gombra.
5. Miután a lemez el lett távolítva, kattintson a Mentés gombra a panel felső részén.
6. A virtuális gép paneljén kattintson **áttekintése** és kattintson a **Start** gombra a virtuális gép újraindítása a panel tetején.

A lemez storage-ban marad, de már nincs csatolva egy virtuális géphez.



## <a name="next-steps"></a>További lépések
Ha szeretné újra felhasználhatja az adatlemezt, csak [csatlakoztassa azt egy másik virtuális Géphez](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


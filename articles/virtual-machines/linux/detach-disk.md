---
title: Adatlemez leválasztása Linux os virtuális gépről – Azure
description: Ismerje meg, hogyan választhat le egy adatlemezt egy virtuális gépről az Azure CLI vagy az Azure Portal használatával.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: f8a0790169b17ad7755386f9bdd4f9372efc83e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74036372"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Adatlemez leválasztása Linux rendszerű virtuális gépről

Ha már nincs szüksége egy virtuális géphez csatolt adatlemezre, könnyedén leválaszthatja. Ezzel eltávolítja a lemezt a virtuális gépről, de nem távolítja el a tárolóból. Ebben a cikkben egy Ubuntu LTS 16.04 disztribúcióval dolgozunk. Ha más disztribúciót használ, a lemez leválasztására vonatkozó utasítások eltérőek lehetnek.

> [!WARNING]
> Ha leválaszt egy lemezt, az nem törlődik automatikusan. Ha előfizetett a Prémium szintű tárhelyre, továbbra is tárolási díjat kell fizetnie a lemezért. További információ: [Díjszabás és számlázás a Prémium szintű storage használata esetén.](https://azure.microsoft.com/pricing/details/storage/page-blobs/)

Ha ismét használni szeretné a lemezen lévő adatokat, újból csatolhatja ugyanahhoz vagy egy másik virtuális géphez.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Csatlakozás a virtuális géphez a lemez leválasztásához

Mielőtt leválaszthatná a lemezt a CLI vagy a portál használatával, le kell választania a lemezt, és el kell távolítania a hivatkozásokat, ha az fstab fájlból.

Csatlakozzon a virtuális géphez. Ebben a példában a virtuális gép nyilvános IP-címe *10.0.1.4* az *azureuser*felhasználó névvel: 

```bash
ssh azureuser@10.0.1.4
```

Először keresse meg a leválasztani kívánt adatlemezt. A következő példa a dmesg segítségével szűri az SCSI-lemezeket:

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

Itt, *sdc* a lemez, hogy szeretnénk leválasztani. Azt is meg kell ragadnia a lemez UUID azonosítóját.

```bash
sudo -i blkid
```

A kimenet a következő példához hasonlóan néz ki:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Az */etc/fstab* fájl szerkesztése a lemezre mutató hivatkozások eltávolításához. 

> [!NOTE]
> Az **/etc/fstab** fájl helytelen szerkesztése nem indítható rendszert eredményezhet. Ha nem biztos a dolgában, a fájl megfelelő szerkesztésével kapcsolatos információkért olvassa el a disztribúció dokumentációját. Azt is javasoljuk, hogy az /etc/fstab fájl biztonsági mentése szerkesztés előtt létrejön.

Nyissa meg az */etc/fstab* fájlt egy szövegszerkesztőben az alábbiak szerint:

```bash
sudo vi /etc/fstab
```

Ebben a példában a következő sort törölni kell az */etc/fstab* fájlból:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

A `umount` lemez leválasztására használható. A következő példa leválasztása a */dev/sdc1* partíciót a */datadrive* csatlakoztatási pontról:

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Adatlemez leválasztása az Azure CLI használatával 

Ez a példa leválasztja a *myDataDisk* lemezt a *myResourceGroup myVM*nevű vm-ről. *myVM*

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

A lemez a tárolóban marad, de már nem csatlakozik a virtuális géphez.


## <a name="detach-a-data-disk-using-the-portal"></a>Adatlemez leválasztása a portállal

1. A bal oldali menüben válassza a **Virtuális gépek**lehetőséget.
2. Válassza ki azt a virtuális gépet, amely rendelkezik a leválasztani kívánt adatlemezzel, és kattintson a **Leállítás** gombra a virtuális gép felszabadításához.
3. A virtuális gép ablaktábláján válassza a **Lemezek**lehetőséget.
4. A **Lemezek** ablaktábla tetején válassza a **Szerkesztés**lehetőséget.
5. A **Lemezek** ablaktáblán, a leválasztani kívánt adatlemez jobb széléről ![kattintson](./media/detach-disk/detach.png) a Leválasztás gombra a képleválasztás gombra.
5. A lemez eltávolítása után kattintson a Mentés gombra az ablaktábla tetején.
6. A virtuális gép ablaktábláján kattintson az **Áttekintés gombra,** majd az ablaktábla tetején található **Start** gombra a virtuális gép újraindításához.

A lemez a tárolóban marad, de már nem csatlakozik a virtuális géphez.



## <a name="next-steps"></a>További lépések
Ha újra fel szeretné használni az adatlemezt, egyszerűen [csatolhatja egy másik virtuális géphez.](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


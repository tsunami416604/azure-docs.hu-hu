---
title: "Válassza le a Linux virtuális gép - Azure adatlemezét |} Microsoft Docs"
description: "Ismerje meg, egy data lemezt leválasztani a virtuális gépen az Azure CLI 2.0 vagy az Azure-portálon."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: c589dd8c9d597145fd87a00d9a2ba040988cd8ec
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Hogyan egy Linux virtuális gép adatlemez leválasztása

Ha már nincs szüksége egy virtuális géphez csatolt adatlemezre, könnyedén leválaszthatja. Ez a lemez eltávolítása a virtuális gépről, de ez nem távolítja el a tárolóból. 

> [!WARNING]
> A lemez leválasztása esetén nem automatikusan törlődik. Ha prémium szintű Storage, a lemez tárolási költségek továbbra. További információt talál [árak és számlázás prémium szintű Storage használatakor](../windows/premium-storage.md#pricing-and-billing). 
> 
> 

Ha ismét használni szeretné a lemezen lévő adatokat, újból csatolhatja ugyanahhoz vagy egy másik virtuális géphez.  

## <a name="detach-a-data-disk-using-cli-20"></a>Parancssori felület 2.0 használatával adatlemez leválasztása

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

A lemez a tárolóban marad, de már nincs virtuális géphez csatlakoztatva.


## <a name="detach-a-data-disk-using-the-portal"></a>Adatlemez leválasztása a portállal
1. A bal oldali menüben válassza ki a **virtuális gépek**.
2. Válassza ki a virtuális gépet, amely rendelkezik a adatlemez leválasztása, és kattintson a kívánt **leállítása** felszabadítani a virtuális Gépet.
3. Jelölje ki a virtuális gép ablaktáblán **lemezek**.
4. Felső részén a **lemezek** ablaktáblán válassza előbb **szerkesztése**.
5. Az a **lemezek** ablaktáblán, a adatlemez, amelyeket szeretne leválasztani, kattintson a jobb a ![leválasztási gomb képe](./media/detach-disk/detach.png) leválasztani gombra.
5. A lemez eltávolítása után kattintson a Mentés az ablaktábla tetején.
6. A virtuális gép ablaktáblán kattintson **áttekintése** , majd a **Start** gombra, ha a virtuális gép újraindítására a panel tetején.

A lemez a tárolóban marad, de már nincs virtuális géphez csatlakoztatva.


## <a name="next-steps"></a>Következő lépések
Ha szeretné ismét felhasználni a adatlemez, most [csatlakoztassa azt egy másik virtuális gép](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


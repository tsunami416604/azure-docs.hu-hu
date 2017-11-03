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
ms.date: 03/21/2017
ms.author: cynthn
ms.openlocfilehash: de0222d897ed2cf94be98501c39385ac88f866fc
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
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
az vm disk detach -g myResourceGroup --vm-name myVm -n myDataDisk
```

A lemez a tárolóban marad, de már nincs virtuális géphez csatlakoztatva.


## <a name="detach-a-data-disk-using-the-portal"></a>Adatlemez leválasztása a portállal
1. Válassza ki a portál hub **virtuális gépek**.
2. Válassza ki a virtuális gépet, amely rendelkezik a adatlemez leválasztása, és kattintson a kívánt **leállítása** felszabadítani a virtuális Gépet.
3. Válassza ki a virtuális gépek paneljét **lemezek**.
4. Felső részén a **lemezek** panelen válassza **szerkesztése**.
5. Az a **lemezek** panelen a adatlemez, amelyeket szeretne leválasztani, kattintson a jobb a ![leválasztási gomb képe](./media/detach-disk/detach.png) leválasztani gombra.
5. A lemez eltávolítása után a panel tetején kattintson a Mentés gombra.
6. A virtuális gép paneljén kattintson **áttekintése** , majd a **Start** gombra a virtuális gép újraindítására a panel tetején.

A lemez a tárolóban marad, de már nincs virtuális géphez csatlakoztatva.








## <a name="next-steps"></a>Következő lépések
Ha szeretné ismét felhasználni a adatlemez, most [csatlakoztassa azt egy másik virtuális gép](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


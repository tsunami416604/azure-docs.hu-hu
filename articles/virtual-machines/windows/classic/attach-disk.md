---
title: "Lemez csatolása a klasszikus Azure virtuális gépek |} Microsoft Docs"
description: "A klasszikus üzembe helyezési modellel létrehozott Windows virtuális gépek adatlemezt csatolni, és inicializálja."
services: virtual-machines-windows, storage
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cynthn
ms.openlocfilehash: 0440cd2fadffd945c75d1b94df67e0c8b8bf7c1e
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Adatlemez csatolása hagyományos módon üzembe helyezett windowsos virtuális géphez

Ez a cikk bemutatja, hogyan a klasszikus telepítési modellt az Azure portál használatával Windows virtuális géphez létre új és meglévő lemez csatolása.



Emellett [adatlemezt csatolni egy Linux virtuális Gépet az Azure portálon](../../linux/attach-disk-portal.md).

Mielőtt lemezt csatlakoztatni, tekintse át a következő tippek:

* A virtuális gép mérete csatolhat hány adatlemezek szabályozza. További információkért lásd: [virtuális gépek méretei](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* A prémium szintű storage, kell a DS-méretek és GS sorozatnak virtuális gép. Ezek a virtuális gépek lemezeit mind prémium és Standard storage-fiókok használatával. Prémium szintű storage bizonyos régiókban érhető el. További információkért lásd: [prémium szintű Storage: nagy teljesítményű tárolást Azure virtuális gépek terheléseihez](../premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Egy új lemezt nem kell először hozza létre, mert az Azure létrehozza azt csatolása.

Emellett [powershellel adatlemezzel](../../virtual-machines-windows-attach-disk-ps.md).

> [!IMPORTANT]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="find-the-virtual-machine"></a>A virtuális gép található.
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza ki a virtuális gépet az irányítópulton szereplő erőforrásra a.
3. Kattintson a bal oldali ablaktábla **beállítások**, kattintson a **lemezek**.

    ![Nyissa meg a lemez beállításai](./media/attach-disk/virtualmachinedisks.png)

A következő utasítások vagy csatlakoztatása a következő lépésként a [új lemez](#option-1-attach-a-new-disk) vagy egy [meglévő lemez](#option-2-attach-an-existing-disk).

## <a name="option-1-attach-and-initialize-a-new-disk"></a>1. lehetőség: Csatolja, és az új lemez inicializálása

1. Az a **lemezek** panelen kattintson a **új Attach**.
2. Tekintse át az alapértelmezett beállításokat, szükség szerint frissítése, és kattintson a **OK**.

   ![Lemez beállítások áttekintése](./media/attach-disk/attach-new.png)

3. Után az Azure létrehozza a lemezt, és csatolja azt a virtuális gépet, az új lemez szerepel-e a virtuális gép lemezbeállításokat alatt **adatlemezek**.

### <a name="initialize-a-new-data-disk"></a>Az új adatok lemez inicializálása

1. Csatlakozzon a virtuális géphez. Útmutatásért lásd: [csatlakoztatása, és jelentkezzen be a Windowst futtató Azure virtuális gép](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
2. A bejelentkezés után a virtuális géphez, nyissa meg a **Kiszolgálókezelő**. A bal oldali panelen válassza ki a **fájl- és tárolási szolgáltatások**.

    ![Nyissa meg a Kiszolgálókezelőt](../media/attach-disk-portal/fileandstorageservices.png)

3. Válassza ki **lemezek**.
4. A **lemezek** szakasz sorolja fel a lemezeket. Leggyakrabban a virtuális gépnek lemez 0, 1, és lemezzel 2. 0 lemez az operációs rendszer lemez lemez az ideiglenes lemez 1 és 2 lemez az adatok lemez újonnan csatolva a virtuális géphez. Az adatlemez felsorolja a későbbi **ismeretlen**.

 Kattintson a jobb gombbal a lemezre, és válassza ki **inicializálása**.

5. Értesítést kap, hogy minden adat törlődik, a lemez inicializálásakor. Kattintson a **Igen** nyugtázza a figyelmeztetést, és végezze el a lemez inicializálását. Művelet befejeződése után a partíció állapottal jelenik **GPT**. Kattintson ismét a jobb gombbal a lemezre, és válassza ki **új kötet**.

6. A varázsló az alapértelmezett értékekkel. Ha a varázsló befejeződött, a **kötetek** a szakasz az új kötet sorolja fel. A lemez most már készen áll a adatainak tárolásához.

    ![Sikeresen inicializálta kötet](./media/attach-disk/newdiskafterinitialization.png)

## <a name="option-2-attach-an-existing-disk"></a>2. lehetőség: A meglévő lemez csatolása
1. Az a **lemezek** panelen kattintson a **Csatolás meglévő**.
2. A **meglévő lemez csatolása**, kattintson a **hely**.

   ![Meglévő lemez csatolása](./media/attach-disk/attachexistingdisksettings.png)
3. A **tárfiókok**, válassza ki a fiókot, és a tároló, amely a .vhd fájlt.

   ![Található virtuális merevlemez helye](./media/attach-disk/existdiskstorageaccountandcontainer.png)

4. Válassza ki a .vhd fájlt.
5. A **meglévő lemez csatolása**, csak a kiválasztott fájl megtalálható-e **VHD-fájl**. Kattintson az **OK** gombra.
6. Azure a lemez csatolása a virtuális gép, miután a virtuális gép lemezbeállításokat alatt felsorolt **adatlemezek**.

## <a name="use-trim-with-standard-storage"></a>Standard szintű tárolóval vágást használata

Standard szintű tárolást (HDD) használatakor vágást kell engedélyeznie. VÁGÁS elveti a nem használt blokkok a lemezen, így csak a ténylegesen használt tárolási kell fizetni. VÁGÁS használatával mentheti a költségek, beleértve a nem használt blokkok a nagy fájlok törlése.

Ez a parancs a vágás beállítás ellenőrzéséhez futtathatja. Nyissa meg a Windows virtuális gép, és írja be a parancssorba:

```
fsutil behavior query DisableDeleteNotify
```

A parancs a 0 értéket adja vissza, vágást engedélyezve van-e megfelelően. Ha a visszaadott érték 1, vágást engedélyezéséhez a következő parancsot:
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>Következő lépések
Ha az alkalmazás a d meghajtó adatainak tárolásához, érdemes [a meghajtóbetűjel, a Windows ideiglenes lemez](../../virtual-machines-windows-change-drive-letter.md).

## <a name="additional-resources"></a>További források
[Lemez és virtuális merevlemezek a virtuális gépek](../../virtual-machines-linux-about-disks-vhds.md)

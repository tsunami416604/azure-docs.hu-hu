---
title: "Felügyelt adatlemezt csatolni egy Windows Virtuálisgép - Azure |} Microsoft Docs"
description: "Hogyan lehet új felügyelt adatlemezt csatolni egy Windows virtuális Gépet az Azure portálon a Resource Manager üzembe helyezési modellben."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: cynthn
ms.openlocfilehash: f0cf88a06c5470ef173b22e7213419a6c8760723
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-attach-a-managed-data-disk-to-a-windows-vm-in-the-azure-portal"></a>Hogyan felügyelt adatlemezt csatolni egy Windows virtuális Gépet az Azure-portálon

Ez a cikk bemutatja, hogyan Windows virtuális gépek az Azure portálon keresztül felügyelt adatlemezt csatolni. Mielőtt ezt megtehetné, tekintse át az alábbi tippek:

* A virtuális gép mérete csatolhat hány adatlemezek szabályozza. További információkért lásd: [virtuális gépek méretei](sizes.md).
* Egy új lemezt nem kell először hozza létre, mert az Azure létrehozza azt csatolása.

Emellett [powershellel adatlemezzel](attach-disk-ps.md).



## <a name="add-a-data-disk"></a>Adatlemez hozzáadása
1. A bal oldali menüben kattintson a **virtuális gépek**.
2. Válassza ki a virtuális gépet a listából.
3. A virtuális gép paneljén kattintson **lemezek**.
   4. Az a **lemezek** panelen kattintson a **+ Hozzáadás adatlemez**.
5. Válassza ki a legördülő lista az új lemezhez, **hozzon létre üres**.
6. Az a **létrehozás felügyelt lemezes** panelen adjon meg egy nevet, a lemez és a további beállításokat, szükség esetén. Amikor elkészült, kattintson a **létrehozása**.
7. Az a **lemezek** paneljén válassza a Mentés az új lemez konfigurációját a virtuális gép mentéséhez.
6. Után az Azure létrehozza a lemezt, és csatolja azt a virtuális gépet, az új lemez szerepel-e a virtuális gép lemezbeállításokat alatt **adatlemezek**.


## <a name="initialize-a-new-data-disk"></a>Az új adatok lemez inicializálása

1. Csatlakoztassa a virtuális Gépet.
1. Kattintson a start menüben, a virtuális gép és a típus belül **diskmgmt.msc** és találati **Enter**. Elindítja a Lemezkezelés beépülő modult.
2. A Lemezkezelés eszköz felismeri, hogy van-e egy új, nem inicializált lemezt, és a lemez inicializálása ablak jelenik meg.
3. Győződjön meg arról, hogy az új lemez legyen kijelölve, és kattintson a **OK** inicializálása azt.
4. Az új lemez lesz **le nem foglalt**. Kattintson a jobb gombbal a lemezre, és válasszon **új egyszerű kötet**. A **új egyszerű kötet varázslóban** indul el.
5. Lépkedjen végig a varázslón, továbbra is az alapértelmezett beállításokat, amikor elkészült, válassza ki **Befejezés**.
6. Zárja be a Lemezkezelés eszközben.
7. Elérhetővé válik egy előugró ablak, amelyekre szüksége van az új lemez formázása előtt is használhatja. Kattintson a **lemez formázása**.
8. Az a **új lemez formázása** párbeszédpanelen ellenőrizze a beállításokat, és kattintson a **Start**.
9. Figyelmezteti rá, hogy a lemezek formázása fogja az összes adat törlésére, kattintson az elérhetővé válik **OK**.
10. A formátum befejeztével kattintson **OK**.

## <a name="use-trim-with-standard-storage"></a>Standard szintű tárolóval vágást használata

Standard szintű tárolást (HDD) használatakor vágást kell engedélyeznie. VÁGÁS elveti a nem használt blokkok a lemezen, így csak a ténylegesen használt tárolási kell fizetni. Ez is mentheti költségek Ha nagy fájlok létrehozása, majd törli őket. 

Ez a parancs a vágás beállítás ellenőrzéséhez futtathatja. Nyissa meg a Windows virtuális gép, és írja be a parancssorba:

```
fsutil behavior query DisableDeleteNotify
```

A parancs a 0 értéket adja vissza, vágást engedélyezve van-e megfelelően. Ha a visszaadott érték 1, vágást engedélyezéséhez a következő parancsot:
```
fsutil behavior set DisableDeleteNotify 0
```

Adatok törlése a lemezről, után gondoskodhat a vágás töredezettségmentesítési kiürítési megfelelően futtatásával vágás műveletek:

```
defrag.exe <volume:> -l
```

A teljes kötet van levágja a kötet formázását is biztosítható.

## <a name="next-steps"></a>Következő lépések
Ha Ön alkalmazás van szüksége a d meghajtó adatainak tárolásához, érdemes [a meghajtóbetűjel, a Windows ideiglenes lemez](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

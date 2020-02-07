---
title: Felügyelt adatlemez csatolása Windows rendszerű virtuális géphez – Azure
description: Hogyan lehet egy Windows virtuális gép felügyelt adatlemez csatolása az Azure portal használatával.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 8b651d6e5ca9262d38f5bfb9e10c404e2614809e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77055668"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Windows virtuális gép felügyelt adatlemez csatolása az Azure portal használatával

Ez a cikk bemutatja, hogyan kell új felügyelt adatlemez csatolása egy Windows virtuális gép (VM) az Azure portal használatával. A virtuális gép mérete határozza meg, hány adatlemez csatolható. További információ: [virtuális gépek méretei](sizes.md).


## <a name="add-a-data-disk"></a>Adatlemez hozzáadása

1. Adatlemez hozzáadásához nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.
2. Válasszon egy virtuális gépet a listából.
3. A **virtuális gép** lapon válassza a **lemezek**lehetőséget.
4. A **lemezek** oldalon válassza az **adatlemez hozzáadása**elemet.
5. Az új lemez legördülő menüjében válassza a **lemez létrehozása**lehetőséget.
6. A **felügyelt lemez létrehozása** lapon írja be a lemez nevét, és szükség szerint módosítsa a többi beállítást. Amikor elkészült, válassza a **Létrehozás** lehetőséget.
7. A **lemezek** lapon válassza a **Mentés** lehetőséget a virtuális gép új lemez-konfigurációjának mentéséhez.
8. Miután az Azure létrehozta a lemezt, és csatolja a virtuális géphez, az új lemez megjelenik a virtuális gép lemez-beállításaiban az **adatlemezek**területen.


## <a name="initialize-a-new-data-disk"></a>Új adatlemez inicializálása

1. Csatlakozzon a virtuális géphez.
1. Válassza ki a Windows **Start** menüt a futó virtuális gépen, és írja be a **diskmgmt. msc** kifejezést a keresőmezőbe. Megnyílik a **Lemezkezelés** konzol.
2. A Lemezkezelés felismeri, hogy új, nem inicializált lemezzel és a **lemez inicializálása** ablak jelenik meg.
3. Ellenőrizze, hogy az új lemez van-e kiválasztva, majd kattintson **az OK gombra** az inicializáláshoz.
4. Az új lemez nem **lefoglaltként**jelenik meg. Kattintson a jobb gombbal a lemezen bárhová, és válassza az **új egyszerű kötet**lehetőséget. Megnyílik az **új egyszerű kötet varázsló** ablak.
5. Folytassa a varázslót, és őrizze meg az összes alapértelmezett értéket, és ha elkészült, válassza a **Befejezés**gombot.
6. A **Lemezkezelés**ablak bezárásához.
7. Egy előugró ablak jelenik meg, hogy arról tájékoztatja, az új lemez formázásához, mielőtt használhatná azt kell. Válassza a **lemez formázása**lehetőséget.
8. Az **új lemez formázása** ablakban jelölje be a beállításokat, majd kattintson a **Start**gombra.
9. Megjelenik egy figyelmeztetés értesíti, hogy a lemezek formázása törli az összes adatot. Kattintson az **OK** gombra.
10. Ha a formázás elkészült, kattintson **az OK gombra**.

## <a name="next-steps"></a>Következő lépések

- Az [adatlemezeket a PowerShell használatával is csatlakoztathatja](attach-disk-ps.md).
- Ha az alkalmazásnak a *D:* meghajtót kell használnia az adattároláshoz, [módosíthatja a Windows ideiglenes lemez meghajtóbetűjelét](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

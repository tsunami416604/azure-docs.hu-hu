---
title: Felügyelt adatlemez csatolása Windows virtuális géphez – Azure
description: Felügyelt adatlemez csatolása Windows virtuális géphez az Azure Portal használatával.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0fe04941821de2ac6e4e873e8d073c3e9b9d9508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919379"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Felügyelt adatlemez csatolása Windows virtuális géphez az Azure Portal használatával

Ez a cikk bemutatja, hogyan csatolhat egy új felügyelt adatlemezt egy Windows virtuális géphez (VM) az Azure Portal használatával. A virtuális gép mérete határozza meg, hogy hány adatlemezek csatolható. További információ: [Méretek a virtuális gépekhez.](sizes.md)


## <a name="add-a-data-disk"></a>Adatlemez hozzáadása

1. Az [Azure Portalon](https://portal.azure.com) hozzáadhat egy adatlemezt. Keressen és válasszon **virtuális gépek**lehetőséget.
2. Válasszon ki egy virtuális gépet a listából.
3. A **Virtuálisgép** lapon válassza a **Lemezek**lehetőséget.
4. A **Lemezek** lapon válassza az **Adatlemez hozzáadása**lehetőséget.
5. Az új lemez legördülő legördülő menüben válassza a **Lemez létrehozása lehetőséget.**
6. A **Felügyelt lemez létrehozása** lapon írja be a lemez nevét, és szükség szerint módosítsa a többi beállítást. Amikor elkészült, válassza a **Létrehozás** lehetőséget.
7. A **Lemezek** lapon válassza a **Mentés** lehetőséget a virtuális gép új lemezkonfigurációjának mentéséhez.
8. Miután az Azure létrehozza a lemezt, és csatolja a virtuális géphez, az új lemez megjelenik a virtuális gép lemezbeállításai között **Adatlemezek**.


## <a name="initialize-a-new-data-disk"></a>Új adatlemez inicializálása

1. Csatlakozzon a virtuális géphez.
1. Válassza ki a Windows **Start** menüt a futó virtuális gépbelsejében, és írja be a **diskmgmt.msc értéket** a keresőmezőbe. Megnyílik **a Lemezkezelés** konzol.
2. A Lemezkezelés felismeri, hogy új, inicializálatlan lemeze van, és megjelenik a **Lemez inicializálása** ablak.
3. Ellenőrizze, hogy az új lemez ki van-e **jelölve,** majd az OK gombra kell-e választva inicializálni.
4. Az új lemez **nem lefoglalt**lemezként jelenik meg. Kattintson a jobb gombbal a lemez tetszőleges pontjára, és válassza az **Új egyszerű kötet**lehetőséget. Megnyílik **az Új egyszerű kötet varázsló** ablaka.
5. Folytassa a varázslót az összes alapértelmezett beállítás megtartásával, és amikor elkészült, válassza a **Befejezés gombot.**
6. A **Lemezkezelés bezárása**.
7. Megjelenik egy előugró ablak, amely értesíti, hogy használat előtt formáznia kell az új lemezt. Válassza **a Lemez formázása**lehetőséget.
8. Az **Új lemez formázása** ablakban ellenőrizze a beállításokat, majd válassza a **Start**gombot.
9. Egy figyelmeztetés figyelmezteti, hogy a lemezek formázása törli az összes adatot. Válassza **az OK gombot.**
10. Ha a formázás befejeződött, kattintson az **OK gombra.**

## <a name="next-steps"></a>További lépések

- [Adatlemezt](attach-disk-ps.md)a PowerShell használatával is csatolhat.
- Ha az alkalmazásnak a *D:* meghajtót kell használnia az adatok tárolásához, [módosíthatja a Windows ideiglenes lemez meghajtóbetűjelét.](change-drive-letter.md)

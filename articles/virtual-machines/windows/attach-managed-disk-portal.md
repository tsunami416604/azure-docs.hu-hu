---
title: Felügyelt adatlemez csatolása a Windows virtuális gép – Azure |} A Microsoft Docs
description: Hogyan lehet egy Windows virtuális gép felügyelt adatlemez csatolása az Azure portal használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 81fbf650597813baaf6618f82ef55a937f1870d9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476026"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Windows virtuális gép felügyelt adatlemez csatolása az Azure portal használatával

Ez a cikk bemutatja, hogyan kell új felügyelt adatlemez csatolása egy Windows virtuális gép (VM) az Azure portal használatával. A virtuális gép mérete határozza meg, hány adatlemez csatolható. További információkért lásd: [virtuális gépek méretei](sizes.md).


## <a name="add-a-data-disk"></a>Adatlemez hozzáadása

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali menüben válassza ki a **virtuális gépek**.
2. Válasszon egy virtuális gépet a listából.
3. Az a **virtuális gép** lapon jelölje be **lemezek**.
4. Az a **lemezek** lapon jelölje be **Hozzáadás adatlemez**.
5. Válassza ki a listából az új lemez, **létrehozása lemez**.
6. Az a **létrehozása felügyelt lemez** lapon írja be a lemez nevét és a további beállításokat szükség szerint. Amikor elkészült, válassza a **Létrehozás** lehetőséget.
7. Az a **lemezek** lapon jelölje be **mentése** az új lemez konfigurációját a virtuális gép mentéséhez.
8. Után az Azure létrehozza a lemezt, és csatlakoztatja a virtuális gép, az új lemez szerepel-e a virtuális gép lemezbeállításokat alatt **adatlemezek**.


## <a name="initialize-a-new-data-disk"></a>Új adatlemez inicializálása

1. Csatlakozzon a virtuális géphez.
1. Válassza ki a Windows **Start** a futó virtuális gép menüben, és adja meg **diskmgmt.msc** kifejezést a keresőmezőbe. A **Lemezkezelés** konzolon nyílik.
2. Lemezkezelés felismeri, hogy egy új, nem inicializált lemez és a **lemez inicializálása** ablak jelenik meg.
3. Ellenőrizze, hogy az új lemez kiválasztva, és majd **OK** inicializálása azt.
4. Megjelenik az új lemez **le nem foglalt**. Kattintson a jobb gombbal a lemezre, majd válassza a bárhol **új egyszerű kötet**. A **új egyszerű kötet varázslóban** ablak nyílik meg.
5. Végezze el a varázsló továbbra is az alapértelmezett beállításokat, és ha elkészült, válassza ki **Befejezés**.
6. Bezárás **lemez kezelése**.
7. Egy előugró ablak jelenik meg, hogy arról tájékoztatja, az új lemez formázásához, mielőtt használhatná azt kell. Válassza ki **lemez formázása**.
8. Az a **új lemez formázása** ablakban ellenőrizze a beállításokat, és válassza ki **Start**.
9. Megjelenik egy figyelmeztetés értesíti, hogy a lemezek formázása törli az összes adatot. Kattintson az **OK** gombra.
10. Válassza a formázás befejeződése után **OK**.

## <a name="next-steps"></a>További lépések

- Emellett [adatlemez csatolása a PowerShell-lel](attach-disk-ps.md).
- Ha az alkalmazásnak kell használnia a *D:* meghajtó adatokat tárolni, használhatja [a Windows ideiglenes lemez meghajtóbetűjelét](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

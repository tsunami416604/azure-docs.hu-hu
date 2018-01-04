---
title: "A d meghajtó, a virtuális gépek adatlemezt |} Microsoft Docs"
description: "Ismerteti, hogyan módosíthat meghajtóbetűjelet egy Windows virtuális gép számára, hogy a d meghajtó használhatja egy adatmeghajtó."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: a4e29ce7ebb20753c3313e1148e1c91e853f49f5
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>A d meghajtó használata a Windows virtuális gép egy adatmeghajtó
Ha az alkalmazást kell használni, a D meghajtó adatainak tárolásához, a következő lépések követésével más meghajtóbetűt használ az ideiglenes lemez. Soha ne használja az ideiglenes lemezt, akkor szükség adatok tárolására.

Átméretezésekor vagy **Stop (Deallocate)** virtuális gép, ez a virtuális gép egy olyan új hipervizorra elhelyezésének indíthat. A tervezett vagy nem tervezett karbantartási események is kiválthatják az elhelyezést. Ebben a forgatókönyvben a mennyiségű ideiglenes lemezes lesz hozzárendelve az első elérhető meghajtóbetűjelet. Ha az alkalmazás kifejezetten a d meghajtón van, akkor lépések végrehajtásával ideiglenesen helyezze át a pagefile.sys, új adatlemezzel és rendelje hozzá a D betűt, majd helyezze át a pagefile.sys vissza az ideiglenes meghajtón. Művelet befejeződése után Azure nem veszi vissza a d.: Ha a virtuális gép áthelyezése egy másik hipervizor.

További információ a hogyan Azure az ideiglenes lemezt használ: [az ideiglenes meghajtón a Microsoft Azure virtuális gépeken ismertetése](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Az adatlemez csatolása
Először a adatlemez csatolása a virtuális gép lesz szüksége. Ehhez a portál használatával lásd: [hogyan csatlakoztathatja az Azure-portálon kezelt adatlemezt](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Ideiglenesen a C meghajtó pagefile.sys áthelyezése
1. Csatlakozzon a virtuális géphez. 
2. Kattintson a jobb gombbal a **Start** menüre, majd válassza **rendszer**.
3. A bal oldali menüben válassza ki a **Speciális rendszerbeállítások**.
4. Az a **teljesítmény** szakaszban jelölje be **beállítások**.
5. Válassza ki a **speciális** fülre.
6. Az a **virtuális memória** szakaszban jelölje be **módosítása**.
7. Válassza ki a **C** meghajtó, és kattintson a **rendszer kezeli a méretet** majd **beállítása**.
8. Válassza ki a **D** meghajtó, és kattintson a **nincs lapozófájl** majd **beállítása**.
9. Az Alkalmaz gombra. Egy, hogy a számítógép újra kell indítani a módosítások érvénybe lépéséhez figyelmeztetés fog megjelenni.
10. A virtuális gép újraindításához.

## <a name="change-the-drive-letters"></a>A meghajtó-betűjelek módosítása
1. Miután a virtuális gép újraindult, jelentkezzen be a virtuális gép újra.
2. Kattintson a **Start** menü és típus **diskmgmt.msc** és le az ENTER billentyűt. A Lemezkezelés eszköz indul el.
3. Kattintson a jobb gombbal a **D**, az ideiglenes tárolási meghajtó, és válassza ki **meghajtóbetűjel és elérési utak**.
4. A meghajtóbetűjelet, válasszon egy új meghajtót például **T** majd **OK**. 
5. Kattintson a jobb gombbal az adatok lemezre, és válassza ki **módosítása Meghajtóbetűjel és elérési út**.
6. A meghajtóbetűjelet, jelölje ki a meghajtót **D** majd **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Pagefile.sys helyezze vissza a ideiglenes tárolási meghajtó
1. Kattintson a jobb gombbal a **Start** menüre, majd válassza **rendszer**
2. A bal oldali menüben válassza ki a **Speciális rendszerbeállítások**.
3. Az a **teljesítmény** szakaszban jelölje be **beállítások**.
4. Válassza ki a **speciális** fülre.
5. Az a **virtuális memória** szakaszban jelölje be **módosítása**.
6. Válassza ki az operációs rendszer meghajtót **C** kattintson **nincs lapozófájl** majd **beállítása**.
7. Jelölje ki az átmeneti tárolási meghajtót **T** , majd **rendszer kezeli a méretet** majd **beállítása**.
8. Kattintson az **Alkalmaz** gombra. Egy, hogy a számítógép újra kell indítani a módosítások érvénybe lépéséhez figyelmeztetés fog megjelenni.
9. A virtuális gép újraindításához.

## <a name="next-steps"></a>További lépések
* A tárterület érhető el a virtuális gép által növelhető [adatlemezt csatol további](attach-managed-disk-portal.md).


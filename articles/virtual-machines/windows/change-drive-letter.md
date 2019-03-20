---
title: 'Győződjön meg arról, a D: meghajtó, adatlemez virtuális gépek |} A Microsoft Docs'
description: 'Ismerteti, hogyan módosíthat egy Windows virtuális gép meghajtóbetűjelet, így használhatja a D: meghajtó adatmeghajtóként való.'
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: cfd46d5e9750a81d89ed6d3a79bcc9bffdc3d0dd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58007289"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Használja a D: meghajtó adatmeghajtóként egy Windows virtuális gépen
Ha az alkalmazás a D meghajtó használata adatok tárolására van szüksége, kövesse ezeket az utasításokat az ideiglenes lemez egy másik meghajtóbetűjelet használja. Soha ne használja az ideiglenes lemez továbbra is szeretné adatokat tárolhat.

Ha átméretezi vagy **leállítása (felszabadítási)** virtuális gép, ez kezdeményezheti egy olyan új hipervizorra a virtuális gép elhelyezését. Egy tervezett vagy nem tervezett karbantartási esemény is kezdeményezheti az elhelyezést. Ebben a forgatókönyvben az első elérhető meghajtóbetűjel lesz(nek) az ideiglenes lemez. Ha az alkalmazás kifejezetten a D: meghajtó, kövesse az alábbi lépéseket ideiglenesen helyezze át a pagefile.sys, új adatlemez csatolása és azt a meghajtóbetűjelet rendelje D és ezután helyezze vissza a pagefile.sys az ideiglenes meghajtó szeretne. Miután elkészült, az Azure nem veszi vissza a D:, ha a virtuális gép áthelyezése egy másik hipervizor.

További információ arról, hogy az Azure hogyan használja az ideiglenes lemez: [az ideiglenes meghajtó a Microsoft Azure Virtual Machines ismertetése](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Az adatlemez csatolása
Először is kell az adatlemez csatolása a virtuális géphez. Ehhez a portál használatával lásd: [bemutatja, hogyan csatlakoztathat az Azure Portalon egy felügyelt adatlemezre](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Átmenetileg a C meghajtó pagefile.sys áthelyezése
1. Csatlakozzon a virtuális géphez. 
2. Kattintson a jobb gombbal a **Start** menüre, majd válassza **rendszer**.
3. A bal oldali menüben válassza ki a **Speciális rendszerbeállítások**.
4. Az a **teljesítmény** szakaszban jelölje be **beállítások**.
5. Válassza ki a **speciális** fülre.
6. Az a **virtuális memória** szakaszban jelölje be **módosítás**.
7. Válassza ki a **C** meghajtó, és kattintson a **rendszer kezeli a méretet** majd **beállítása**.
8. Válassza ki a **D** meghajtó, és kattintson a **nincs lapozófájl** majd **beállítása**.
9. Kattintson a alkalmazni. Megjelenik egy figyelmeztetés, hogy kell-e a számítógépet a módosítások érvénybe lépéséhez újra kell indítani.
10. A virtuális gép újraindításához.

## <a name="change-the-drive-letters"></a>Módosítsa a meghajtó betűjelei
1. A virtuális gép újraindítása után jelentkezzen be újra a virtuális gép.
2. Kattintson a **Start** menü és típusú **diskmgmt.msc** és nyomja le az ENTER billentyűt. Lemezkezelés indul el.
3. Kattintson a jobb gombbal a **D**, az ideiglenes meghajtó, és válassza ki **meghajtóbetűjel és elérési utak**.
4. A meghajtó betűjelét, válasszon ki egy új meghajtót például **T** majd **OK**. 
5. Kattintson a jobb gombbal az adatlemezt, és válassza ki **módosítás meghajtóbetűjel és elérési utak**.
6. A meghajtó betűjelét, válassza ki a meghajtót **D** majd **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Pagefile.sys lépjen vissza az ideiglenes meghajtó
1. Kattintson a jobb gombbal a **Start** menüre, majd válassza **rendszer**
2. A bal oldali menüben válassza ki a **Speciális rendszerbeállítások**.
3. Az a **teljesítmény** szakaszban jelölje be **beállítások**.
4. Válassza ki a **speciális** fülre.
5. Az a **virtuális memória** szakaszban jelölje be **módosítás**.
6. Válassza ki az operációs rendszer meghajtójának **C** kattintson **nincs lapozófájl** majd **beállítása**.
7. Válassza ki az ideiglenes meghajtó **T** és kattintson a **rendszer kezeli a méretet** majd **beállítása**.
8. Kattintson az **Alkalmaz** gombra. Megjelenik egy figyelmeztetés, hogy kell-e a számítógépet a módosítások érvénybe lépéséhez újra kell indítani.
9. A virtuális gép újraindításához.

## <a name="next-steps"></a>További lépések
* Növelheti a virtuális gép által elérhető tárhely [egy további adatlemez csatolása](attach-managed-disk-portal.md).


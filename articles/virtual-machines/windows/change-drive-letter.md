---
title: 'A virtuális gép d: meghajtójának adatlemezsé '
description: 'Ez a témakör azt ismerteti, hogy miként módosítható a Windows virtuális gépek meghajtóbetűinek módosítása a D: meghajtó adatmeghajtóként való használata érdekében.'
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 15df3178f2860fa066a82cb1429e0c1a6e5c2b08
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083422"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>A D: meghajtó használata adatmeghajtóként Windows virtuális gépen
Ha az alkalmazásnak a D meghajtót kell használnia az adatok tárolásához, kövesse az alábbi utasításokat, ha az ideiglenes lemezhez egy másik meghajtóbetűjelet szeretne használni. Soha ne használja az ideiglenes lemezt a megtartandó adatok tárolására.

Ha átméretezi vagy **leállítja (Felszabadítja)** a virtuális gépet, ez a virtuális gép új hipervizorra való elhelyezését válthatja ki. Egy tervezett vagy nem tervezett karbantartási esemény is kiválthatja ezt az elhelyezést. Ebben az esetben az ideiglenes lemez újra hozzá lesz rendelve az első elérhető meghajtóbetűjelhez. Ha olyan alkalmazással rendelkezik, amely kifejezetten megköveteli a D: meghajtót, az alábbi lépéseket kell végrehajtania a pagefile.sys fájl ideiglenes áthelyezéséhez, egy új adatlemez csatolásához, és hozzárendelheti a D betűt, majd vissza kell helyeznie a pagefile.sys fájlt az ideiglenes meghajtóra. Ha elkészült, az Azure nem veszi vissza a D: ha a virtuális gép egy másik hipervizor.

Ha többet szeretne tudni arról, hogy az Azure hogyan használja az ideiglenes lemezt, [olvassa el Az ideiglenes meghajtó ismertetése a Microsoft Azure virtuális gépeken című témakört.](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Az adatlemez csatolása
Először csatolnia kell az adatlemezt a virtuális géphez. Ehhez a portál használatával, [lásd: Felügyelt adatlemez csatolása az Azure Portalon.](attach-managed-disk-portal.md)

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>A pagefile.sys fájl ideiglenes áthelyezése a C meghajtóra
1. Csatlakozzon a virtuális géphez. 
2. Kattintson a jobb gombbal a **Start** menüre, és válassza **a Rendszer parancsot.**
3. A bal oldali menüben válassza a **Speciális rendszerbeállítások lehetőséget**.
4. A **Teljesítmény csoportban** válassza a **Beállítások lehetőséget.**
5. Lépjen a **Speciális** lapra.
6. A **Virtuális memória csoportban** válassza a **Módosítás**lehetőséget.
7. Jelölje ki a **C** meghajtót, majd kattintson **a Rendszer által kezelt méret,** majd a **Beállítás**gombra.
8. Jelölje ki a **D** meghajtót, majd kattintson a **Nincs lapozófájl,** majd a **Beállítás**gombra.
9. Kattintson az Apply (Alkalmaz) gombra. Figyelmeztetést kap, hogy a számítógépet újra kell indítani ahhoz, hogy a módosítások hatással legyenek.
10. Indítsa újra a virtuális gépet.

## <a name="change-the-drive-letters"></a>A meghajtóbetűjelek módosítása
1. A virtuális gép újraindítása után jelentkezzen be újra a virtuális gépre.
2. Kattintson a **Start** menüre, írja be a **diskmgmt.msc parancsot,** és nyomja le az Enter billentyűt. Elindul a Lemezkezelés.
3. Kattintson a jobb gombbal a **D**, az ideiglenes tároló meghajtóra, és válassza **a Meghajtóbetűjel és elérési utak módosítása parancsot.**
4. A Meghajtó betűjele csoportban jelöljön ki egy új meghajtót, például **T,** majd kattintson **az OK**gombra. 
5. Kattintson a jobb gombbal az adatlemezre, és válassza **a Meghajtóbetűjel és elérési utak módosítása parancsot.**
6. A Meghajtó betűjele csoportban válassza a **D** meghajtót, majd kattintson **az OK**gombra. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>A pagefile.sys fájl visszaáthelyezése az ideiglenes tárolómeghajtóra
1. Kattintson a jobb gombbal a **Start** menüre, és válassza a **Rendszer parancsot**
2. A bal oldali menüben válassza a **Speciális rendszerbeállítások lehetőséget**.
3. A **Teljesítmény csoportban** válassza a **Beállítások lehetőséget.**
4. Lépjen a **Speciális** lapra.
5. A **Virtuális memória csoportban** válassza a **Módosítás**lehetőséget.
6. Jelölje ki a **C** operációsrendszer-meghajtót, és kattintson a **Nincs lapozófájl,** majd a **Beállítás**gombra.
7. Jelölje ki a T ideiglenes **tárolómeghajtót,** majd kattintson **a Rendszer által kezelt méret,** majd a **Beállítás**gombra.
8. Kattintson az **Alkalmaz** gombra. Figyelmeztetést kap, hogy a számítógépet újra kell indítani ahhoz, hogy a módosítások hatással legyenek.
9. Indítsa újra a virtuális gépet.

## <a name="next-steps"></a>További lépések
* A virtuális gép számára rendelkezésre álló tárhelyet [egy további adatlemez csatlakoztatásával](attach-managed-disk-portal.md)növelheti.


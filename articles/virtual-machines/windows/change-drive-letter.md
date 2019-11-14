---
title: 'A D: virtuális gép meghajtójának elkészítése adatlemezként '
description: 'Útmutató a Windows rendszerű virtuális gépek meghajtóbetűjelének módosításához, hogy a D: meghajtót adatmeghajtóként használhassa.'
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 491e7b7be084017cc370fe431c3175ac5b2673f9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033654"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>A D: meghajtó használata adatmeghajtóként Windows-alapú virtuális gépen
Ha az alkalmazásnak a D meghajtót kell használnia az adattároláshoz, kövesse az alábbi utasításokat, hogy az ideiglenes lemezhez egy másik meghajtóbetűjelet használjon. Soha ne használja az ideiglenes lemezt a megőrizni kívánt adatmennyiség tárolására.

Ha átméretezi vagy **leállítja (felszabadítja)** a virtuális gépet, akkor ez a virtuális gép új hypervisorba való elhelyezését eredményezheti. Előfordulhat, hogy a tervezett vagy nem tervezett karbantartási esemény is aktiválhatja ezt az elhelyezést. Ebben az esetben az ideiglenes lemezt az első elérhető meghajtóbetűjelhez rendeli hozzá a rendszer. Ha olyan alkalmazásra van szüksége, amely kifejezetten a D: meghajtót igényli, akkor az alábbi lépéseket követve ideiglenesen helyezze át a pagefile. sys fájlját, csatoljon egy új adatlemezt, és rendelje hozzá a D betűt, majd helyezze át a lapozófájlt. sys vissza az ideiglenes meghajtóra. Ha elkészült, az Azure nem veszi vissza a D-t: Ha a virtuális gép egy másik hypervisorba kerül.

További információ arról, hogy az Azure hogyan használja az ideiglenes lemezt: [az ideiglenes meghajtó ismertetése Microsoft Azure Virtual Machines](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Az adatlemez csatolása
Először csatlakoztatnia kell az adatlemezt a virtuális géphez. Ha ezt a portálon szeretné elvégezni, tekintse meg a [felügyelt adatlemez csatolása a Azure Portal](attach-managed-disk-portal.md)című témakört.

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>A pagefile. sys fájl ideiglenes áthelyezése C meghajtóra
1. Csatlakozzon a virtuális géphez. 
2. Kattintson a jobb gombbal a **Start** menüre, és válassza a **System (rendszeren**) lehetőséget.
3. A bal oldali menüben válassza a **Speciális rendszerbeállítások**lehetőséget.
4. A **teljesítmény** szakaszban válassza a **Beállítások**lehetőséget.
5. Válassza a **speciális** lapot.
6. A **virtuális memória** szakaszban válassza a **módosítás**lehetőséget.
7. Válassza ki a **C** meghajtót, majd kattintson a **rendszer által felügyelt méret** elemre, majd a **beállítás**elemre.
8. Válassza ki a **D** meghajtót, majd kattintson a **nincs lapozófájl** elemre, majd a **beállítás**gombra.
9. Kattintson az Alkalmaz gombra. A módosítások érvénybe lépéséhez figyelmeztetést kap, hogy a számítógépet újra kell indítani.
10. Indítsa újra a virtuális gépet.

## <a name="change-the-drive-letters"></a>A meghajtóbetűjelek módosítása
1. A virtuális gép újraindítása után jelentkezzen be újra a virtuális gépre.
2. Kattintson a **Start** menüre, és írja be a **diskmgmt. msc** parancsot, és nyomja le az ENTER billentyűt. A Lemezkezelés elindul.
3. Kattintson a jobb gombbal a **D**, az ideiglenes tároló meghajtóra, majd válassza a meghajtóbetűjel **és elérési utak módosítása**lehetőséget.
4. A meghajtóbetűjel területen válasszon egy új meghajtót, például **t** , majd kattintson **az OK gombra**. 
5. Kattintson a jobb gombbal az adatlemezre, és válassza a meghajtóbetűjel **és elérési utak módosítása**lehetőséget.
6. A meghajtóbetűjel területen válassza a **D** meghajtó lehetőséget, majd kattintson **az OK**gombra. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>A pagefile. sys fájl áthelyezése az ideiglenes tárolóba
1. Kattintson a jobb gombbal a **Start** menüre, és válassza a **System** lehetőséget.
2. A bal oldali menüben válassza a **Speciális rendszerbeállítások**lehetőséget.
3. A **teljesítmény** szakaszban válassza a **Beállítások**lehetőséget.
4. Válassza a **speciális** lapot.
5. A **virtuális memória** szakaszban válassza a **módosítás**lehetőséget.
6. Válassza ki a **C** operációsrendszer-meghajtót, és kattintson a **nincs lapozófájl** elemre, majd a **beállítás**gombra.
7. Válassza ki az ideiglenes tároló **meghajtót, majd kattintson** a **rendszer által felügyelt méret** elemre, majd a **beállítás**gombra.
8. Kattintson az **Alkalmaz** gombra. A módosítások érvénybe lépéséhez figyelmeztetést kap, hogy a számítógépet újra kell indítani.
9. Indítsa újra a virtuális gépet.

## <a name="next-steps"></a>Következő lépések
* A virtuális gép számára elérhető tárterületet [további adatlemez csatolásával](attach-managed-disk-portal.md)növelheti.


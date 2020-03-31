---
title: Iteratív fejlesztés és hibakeresés az Azure Data Factoryban
description: Megtudhatja, hogyan fejleszthet és debugotíthat a Data Factory folyamatokat iteratív módon az Azure Portalon.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 3a771181f8f2785339cbc47e0a0234b9c4e39adc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926850"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iteratív fejlesztés és hibakeresés az Azure Data Factoryval

Az Azure Data Factory lehetővé teszi a Data Factory folyamatai iteratív fejlesztését és hibakeresését.

A funkció nyolcperces bemutatásához és bemutatásához tekintse meg az alábbi videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Iteratív hibakeresési szolgáltatások
Hozzon létre folyamatokat, és végezze el a tesztfuttatásokat a **Folyamatvásznon** lévő Debug funkció használatával anélkül, hogy egyetlen kódsort írna.

![Hibakeresési képesség a folyamatvásznon](media/iterative-development-debugging/iterative-development-image1.png)

Tekintse meg a tesztfuttatások eredményeit a folyamatvászon **Kimenet ablakában.**

![A csővezetékvászon kimeneti ablaka](media/iterative-development-debugging/iterative-development-image2.png)

Miután egy tesztfuttatás sikeres, adjon hozzá további tevékenységeket a folyamathoz, és folytassa a hibakeresést iteratív módon. **A tesztfuttatást is megszakíthatja,** amíg az folyamatban van.

![Próbafuttatás megszakítása](media/iterative-development-debugging/iterative-development-image3.png)

Ha tesztfuttatásokat végez, a Hibakeresés kiválasztása előtt nem kell közzétennie a módosításokat az **adat-előállítóban.** Ez a funkció olyan esetekben hasznos, amikor az adat-előállító munkafolyamat frissítése előtt meg szeretne győződni arról, hogy a módosítások a várt módon működnek.

> [!IMPORTANT]
> A **Hibakeresés** kiválasztása valójában futtatja a folyamatot. Így például ha a folyamat másolási tevékenységet tartalmaz, a tesztfuttatás adatokat másol a forrástól a célig. Ennek eredményeképpen azt javasoljuk, hogy a hibakeresés során használjon tesztmappákat a másolási tevékenységekben és más tevékenységekben. Miután hibakeresést végzett a folyamaton, váltson a normál műveletekhez használni kívánt mappákra.

## <a name="visualizing-debug-runs"></a>Hibakeresési futtatások megjelenítése

Egy helyen jelenítheti meg az adat-előállító összes olyan hibakeresési futtatását. Válassza a **View debug runs (Keresés) futtatások** jelölőnégyzetet a lap jobb felső sarkában. Ez a szolgáltatás olyan esetekben hasznos, ahol a fő folyamatok a hibakeresési futtatásokat futtatják a gyermekfolyamatok számára, és egyetlen nézetben szeretné látni az összes aktív hibakeresési futtatást.

![Válassza a View active debug runs ikont](media/iterative-development-debugging/view-debug-runs-image1.png)

![Az aktív hibakeresési futtatások mintalistája](media/iterative-development-debugging/view-debug-runs-image2.png)

Ha aktív adatfolyam-hibakeresési munkamenetekkel rendelkezik, ezek a munkamenetek az aktív hibakeresési ablak alsó részén jelennek meg. Kijelölhet egy aktív adatfolyam-munkamenetet, és leállíthatja az adott fürtöt.

![Az aktív adatfolyam-hibakeresési futtatások mintalistája](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Hibakeresési futtatások figyelése

A **Debug** funkcióval kezdeményezett tesztfuttatások nem érhetők el a **Figyelő** lap listájában. A **Figyelő** lapon csak az **Eseményindító most**, **az Ütemezés**vagy a **Tumbling ablak** eseményindítóival aktivált futtatások láthatók. A **Debug** funkcióval kezdeményezett utolsó tesztfuttatása a folyamatvászon **Kimenet ablakában** látható.

## <a name="setting-breakpoints-for-debugging"></a>Töréspontok beállítása hibakereséshez

A Data Factory lehetővé teszi a hibakeresést is, amíg el nem ér egy adott tevékenységet a folyamatvásznon. Csak tegyen egy töréspontot arra a tevékenységre, amelynek tesztelése tartandó, és válassza a **Hibakeresés**lehetőséget. A Data Factory biztosítja, hogy a teszt csak a folyamatvásznon a töréspont-tevékenységig fut. Ez *a Hibakeresés Amíg* szolgáltatás akkor hasznos, ha nem szeretné tesztelni a teljes folyamatot, de csak a folyamaton belüli tevékenységek egy részét.

![Töréspontok a csővezeték vásznán](media/iterative-development-debugging/iterative-development-image4.png)

Töréspont beállításához jelöljön ki egy elemet a folyamatvásznon. A *Debug Amíg* beállítás üres piros körként jelenik meg az elem jobb felső sarkában.

![Töréspont beállítása előtt a kijelölt elem](media/iterative-development-debugging/iterative-development-image5.png)

Miután kiválasztotta a *Hibakeresési Amíg* lehetőséget, kitöltött piros körre változik, jelezve, hogy a töréspont engedélyezve van.

![Töréspont beállítása után a kijelölt elemen](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>További lépések
[Folyamatos integráció és üzembe helyezés az Azure Data Factoryban](continuous-integration-deployment.md)

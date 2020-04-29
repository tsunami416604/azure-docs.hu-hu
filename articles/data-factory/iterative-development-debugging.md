---
title: Ismétlődő fejlesztés és hibakeresés a Azure Data Factoryban
description: Megtudhatja, hogyan fejleszthet és kereshet Data Factory folyamatokat a Azure Portal iteratív.
ms.date: 09/26/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 49e406f0730189439bb1c2529b23d8ad6d760a2f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81411515"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iteratív fejlesztés és hibakeresés az Azure Data Factoryval
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory lehetővé teszi Data Factory folyamatok fejlesztését és hibakeresését iteratív.

A szolgáltatás nyolc perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Ismétlődő hibakeresési funkciók
Folyamatokat hozhat létre, és tesztelési műveleteket hajthat végre a folyamat vásznon található **hibakeresési** funkcióval anélkül, hogy egyetlen sor kódot kellene írnia.

![Hibakeresési képesség a folyamat vásznon](media/iterative-development-debugging/iterative-development-image1.png)

A teszt eredményeinek megtekintése a folyamat vászonjának **output (kimenet** ) ablakában.

![A folyamat vászonjának kimeneti ablaka](media/iterative-development-debugging/iterative-development-image2.png)

A tesztek sikeres futtatása után további tevékenységeket adhat hozzá a folyamathoz, és ismétlődő módon folytathatja a hibakeresést. Egy teszt futtatása is **megszakítható** , amíg folyamatban van.

![Teszt futtatásának megszakítása](media/iterative-development-debugging/iterative-development-image3.png)

A tesztek futtatásakor nem kell közzétennie a módosításokat az adatgyárban a **hibakeresés**kiválasztása előtt. Ez a funkció olyan esetekben hasznos, amikor azt szeretné, hogy a módosítások a várt módon működjenek, mielőtt frissíti a adatfeldolgozó munkafolyamatot.

> [!IMPORTANT]
> A **hibakeresés** kiválasztásával a folyamat ténylegesen fut. Így például, ha a folyamat másolási tevékenységet tartalmaz, a teszt futtatása a forrásról a célhelyre másolja az adatokból. Ennek eredményeképpen javasolt a másolási tevékenységek és más tevékenységek tesztelési mappáinak használata a hibakereséshez. A folyamat hibakeresése után váltson át a normál műveletekben használni kívánt tényleges mappákra.

## <a name="visualizing-debug-runs"></a>Hibakeresési futtatások megjelenítése

Egy helyen megjelenítheti az összes folyamatban lévő hibakeresési futtatást. Válassza a **hibakeresési futtatások megjelenítése** lehetőséget a lap jobb felső sarkában. Ez a funkció olyan esetekben hasznos, amikor a főfolyamatok elindítják a hibakeresési futtatásokat a alárendelt folyamatokban, és egyetlen nézetben szeretnék látni az összes aktív hibakeresési futtatást.

![Válassza az aktív hibakeresési futtatások megtekintése ikont.](media/iterative-development-debugging/view-debug-runs-image1.png)

![Az aktív hibakeresési futtatások mintáinak listája](media/iterative-development-debugging/view-debug-runs-image2.png)

Ha aktív adatfolyam-hibakeresési munkamenetek vannak, akkor ezek a munkamenetek az aktív hibakeresési ablak alsó részén jelennek meg. Választhat aktív adatfolyam-munkamenetet, és leállíthatja a megfelelő fürtöt.

![Az aktív adatfolyam-hibakeresési futtatások mintáinak listája](media/data-flow/dfsessions.png)

## <a name="monitoring-debug-runs"></a>Hibakeresési hibák figyelése

A **hibakeresési** funkcióval kezdeményezett tesztek nem érhetők el a **figyelés** lap listájában. A **figyelés** lapon csak az **eseményindító most**, a **Schedule**vagy a **bukdácsoló ablakos** eseményindítók által aktivált futtatások láthatók. A folyamat-vászon **kimeneti** ablakában a **hibakeresési** képességgel indított utolsó tesztet láthatja.

## <a name="setting-breakpoints-for-debugging"></a>Töréspontok beállítása hibakereséshez

A Data Factory a hibakeresést is lehetővé teszi, amíg el nem éri a folyamat vásznon lévő adott tevékenységet. Csak akkor helyezzen el egy töréspontot a tevékenységre, ha tesztelni szeretné, majd válassza a **hibakeresés**lehetőséget. Data Factory biztosítja, hogy a teszt csak a folyamat vásznon lévő töréspont tevékenységig fusson. Ez a *hibakeresés addig* nem hasznos, ha nem szeretné tesztelni a teljes folyamatot, de csak a folyamaton belüli tevékenységek egy részhalmazát.

![Töréspontok a folyamat vásznon](media/iterative-development-debugging/iterative-development-image4.png)

Töréspont beállításához válasszon ki egy elemet a folyamat vásznon. Egy *hibakeresés, amíg* a lehetőség üres vörös körként jelenik meg a elem jobb felső sarkában.

![Mielőtt beállította a töréspontot a kijelölt elemre](media/iterative-development-debugging/iterative-development-image5.png)

Miután kiválasztotta a *hibakeresést, amíg* a lehetőség be nem fejeződik, a Töréspont engedélyezésének jelzésére a kitöltött piros kör változik.

![Miután beállította a töréspontot a kijelölt elemen](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>További lépések
[Folyamatos integráció és üzembe helyezés a Azure Data Factory](continuous-integration-deployment.md)

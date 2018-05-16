---
title: Ismétlődő fejlesztési és az Azure Data Factory-hibakeresés |} Microsoft Docs
description: Megtudhatja, hogyan fejleszthet és hibakeresési adat-előállító adatcsatornák ismételt az Azure-portálon.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 05/14/2018
ms.topic: article
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: e403afa5c870b2d007a8c5e1d46162cd899ead29
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Ismétlődő fejlesztési és az Azure Data Factory-hibakeresés

Az Azure Data Factory lehetővé teszi ismételt fejlesztéséhez és adat-előállító adatcsatornák hibakeresését.

Nyolc perces bevezető és a szolgáltatás bemutató tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Ismétlődő hibakeresési szolgáltatások
Adatcsatornák és ezek tesztelésével használatával fut a **Debug** képesség a feldolgozási sor vászonra egysoros kód írása nélkül.

![A feldolgozási sor vásznon funkció hibakeresése](media/iterative-development-debugging/iterative-development-image1.png)

Futtatja a vizsgálat eredményeinek megtekintése a **kimeneti** a feldolgozási sor vászon ablak.

![Kimeneti ablakában a feldolgozási sor vászonra](media/iterative-development-debugging/iterative-development-image2.png)

Követően a teszt futtatása sikeres, további tevékenységek hozzáadása a folyamat, és folytatni a hibakeresés ismétlődő módon. Emellett **Mégse** egy tesztet futtatni, amíg folyamatban van.

![Szakítsa meg a teszt futtatása](media/iterative-development-debugging/iterative-development-image3.png)

Tesztelésekor fut, nem kell a változások közzétételére a data factory, kiválasztása előtt **Debug**. Ez akkor hasznos forgatókönyvekben, ahol szeretné győződjön meg arról, hogy a módosítások a data factory munkafolyamat frissítése előtt várt módon működik-e.

## <a name="more-info-about-debugging"></a>További információ a hibakeresés

1. A vizsgálat futtatása a kezdeményezett a **Debug** funkció nem érhetők el a listában szereplő a **figyelő** fülre. Csak tekintse meg a kiváltott futtatja **eseményindító most**, **ütemezés**, vagy **Átfedésmentes ablak** a elindítja a **figyelő** lapon. Láthatja, hogy a legutóbbi teszt futtatásakor a kezdeményezett a **Debug** képesség a **kimeneti** a feldolgozási sor vászon ablak.

2. Kiválasztása **Debug** ténylegesen fut a folyamatot. Igen például a feldolgozási sor tartalmazza a másolási tevékenység során, ha a vizsgálat másol adatokat forrás célhelyre. Ennek eredményeképpen ajánlott teszt mappákat használni a másolási tevékenység és más tevékenységekkel nyomkövetésére. Miután a folyamat már indítja, a normál műveletek használandó tényleges mappák vált.

## <a name="setting-breakpoints-for-debugging"></a>A hibakereséshez töréspontok beállítása

Adat-előállító emellett lehetővé teszi a hibakeresési, amíg el nem éri a feldolgozási sor vásznon egy adott tevékenységet. Csak a tevékenység, ameddig szeretné tesztelni, és válassza ki a töréspont elhelyezése **Debug**. Adat-előállító biztosítja, hogy a vizsgálat a töréspont tevékenység csak a folyamat vásznon. Ez *Debug amíg* szolgáltatás akkor hasznos, ha nem kívánja tesztelni a teljes folyamat, de a tevékenységek a feldolgozási sor belül csak egy részét.

![Töréspontokat a feldolgozási sor vászonra](media/iterative-development-debugging/iterative-development-image4.png)

Töréspontokat állíthasson, a folyamat vászonra egy elem kijelölése. A *Debug amíg* beállítás elem jobb felső sarkában, egy üres piros kör jelenik meg.

![A kijelölt elem Töréspont beállítása előtt](media/iterative-development-debugging/iterative-development-image5.png)

Miután kiválasztotta a *Debug mindaddig, amíg* jelzésére kitöltött piros kör módosítja a beállítást, a a töréspont engedélyezve van.

![Miután beállította a Töréspont a kijelölt elem](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>További lépések
[Folyamatos integrációt és üzembe helyezés az Azure Data Factory](continuous-integration-deployment.md)

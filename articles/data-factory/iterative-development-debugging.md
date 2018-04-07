---
title: Ismétlődő fejlesztési és az Azure Data Factory-hibakeresés |} Microsoft Docs
description: Megtudhatja, hogyan fejleszthet és hibakeresési adat-előállító adatcsatornák ismételt az Azure-portálon.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 04/04/2018
ms.topic: article
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: adb5ffd65d4b2b591abba5d4b3481b7ca08de579
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Ismétlődő fejlesztési és az Azure Data Factory-hibakeresés

Az Azure Data Factory lehetővé teszi ismételt fejlesztéséhez és adat-előállító adatcsatornák hibakeresését.

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

Adat-előállító lehetővé teszi az adatcsatorna vászonra egy adott tevékenységet addig debug. Csak a tevékenység, ameddig szeretné tesztelni, és válassza ki a töréspont elhelyezése **Debug**. Adat-előállító biztosítja, hogy a vizsgálat a töréspont tevékenység csak a folyamat vásznon. Ez *Debug amíg* szolgáltatás akkor hasznos, ha nem kívánja tesztelni a teljes folyamat, de a tevékenységek a feldolgozási sor belül csak egy részét.

![Töréspontokat a feldolgozási sor vászonra](media/iterative-development-debugging/iterative-development-image4.png)

## <a name="next-steps"></a>További lépések
[Folyamatos integrációt és üzembe helyezés az Azure Data Factory](continuous-integration-deployment.md)

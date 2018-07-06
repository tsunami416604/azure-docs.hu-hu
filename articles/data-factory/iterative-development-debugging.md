---
title: Iteratív fejlesztés és hibakeresés az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan fejlesztése és hibakeresése a Data Factory-folyamatok iteratív az Azure Portalon.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 05/14/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: a4d3f991dbba8a686c7242aabff11d9228300777
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865165"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iteratív fejlesztés és hibakeresés az Azure Data Factoryvel

Az Azure Data Factory használatával iteratív fejlesztés és hibakeresés a Data Factory-folyamatok.

Egy nyolc perces bevezető és a funkció bemutatójáért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="iterative-debugging-features"></a>Iteratív hibakeresési funkciók
Folyamatok létrehozása és tesztelése a használatával fut a **Debug** egy egyetlen sor kód megírása nélkül folyamatvásznon képesség.

![Képesség a folyamatvásznon hibakeresése](media/iterative-development-debugging/iterative-development-image1.png)

Fut a teszt eredményeinek megtekintése a **kimeneti** folyamatvásznon ablakában.

![A folyamat vászon kimeneti ablak](media/iterative-development-debugging/iterative-development-image2.png)

Egy tesztfuttatás sikeres lesz, miután további tevékenységek hozzáadása a folyamatban, és továbbra is, folytatható iteratív hibakeresést. Emellett **Mégse** egy tesztfuttatás, amíg folyamatban van.

![Egy tesztfuttatás megszakítása](media/iterative-development-debugging/iterative-development-image3.png)

Ha fut, nem kell a módosítások közzététele a data Factory kiválasztása előtt **Debug**. Ez hasznos forgatókönyvekben, ahol szeretné győződjön meg arról, hogy a módosítások a data factory munkafolyamat frissítése előtt várt módon működik-e.

## <a name="more-info-about-debugging"></a>További információ a hibakeresés

1. A teszt futtatása a kezdeményezett a **Debug** képességek nem érhetők el a listában a **figyelő** fülre. Csak lásd futtatja az aktivált **Aktiválás most**, **ütemezés**, vagy **Átfedésmentes ablak** eseményindítókra a **figyelő** fülre. Láthatja, hogy az utolsó teszt futtatásakor a kezdeményezett a **Debug** a képesség a **kimeneti** folyamatvásznon ablakában.

2. Kiválasztásával **Debug** ténylegesen futtatja a folyamatot. Így például, ha a folyamat másolási tevékenységet tartalmaz, a teszt futtatása adatokat másol forrás célhelyre. Emiatt ajánlott teszt mappákat használni a másolási tevékenységek és egyéb tevékenységeket történő hibakeresése során. A folyamat már hibakereséséhez, váltson a normál műveletek során használni kívánt tényleges mappákat.

## <a name="setting-breakpoints-for-debugging"></a>Hibakeresési töréspontok beállításával

Data Factory használatával végzett hibakeresésben, amíg el nem éri a folyamatvásznon egy adott tevékenységet. Ameddig szeretné tesztelni, és válassza ki a tevékenység csak tegyen egy töréspontot **Debug**. A Data Factory biztosítja, hogy a vizsgálatot futtat a töréspont tevékenység csak a folyamatvásznon. Ez *Debug amíg* funkció akkor hasznos, ha nem szeretné a teljes folyamat, de csak egy részhalmazát tevékenységek belül a folyamat teszteléséhez.

![A folyamat vásznon töréspontok keresése](media/iterative-development-debugging/iterative-development-image4.png)

Állítson be egy töréspontot, hogy a folyamatvásznon elem kiválasztása. A *Debug amíg* lehetőséget az elem jobb felső sarokban található egy üres piros kör jelenik meg.

![A kijelölt elem egy töréspontot beállítása előtt](media/iterative-development-debugging/iterative-development-image5.png)

Kiválasztása után a *Debug amíg* beállítást, azt egy kitöltött vörös körre változik jelzi a töréspont engedélyezve van.

![Miután beállította a egy töréspontot a kijelölt elem](media/iterative-development-debugging/iterative-development-image6.png)

## <a name="next-steps"></a>További lépések
[Folyamatos integráció és üzembe helyezés az Azure Data Factoryban](continuous-integration-deployment.md)

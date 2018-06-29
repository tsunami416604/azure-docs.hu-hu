---
title: Vizuálisan figyelése az Azure adat-előállítók |} Microsoft Docs
description: Ismerje meg, vizuálisan figyelése az Azure adat-előállítók
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2018
ms.author: shlo
ms.openlocfilehash: e654cc23d6a558469ea238fc5ade82b44562f9a2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050369"
---
# <a name="visually-monitor-azure-data-factories"></a>Vizuálisan figyelése az Azure adat-előállítók
Az Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás. Lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek alkalmasak az adatok átvitelének és átalakításának irányítására és automatizálására. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok, feldolgozhatók és átalakíthatók az adatok különböző számítási szolgáltatások használatával (pl. Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning), és a kimeneti adatok közzétehetők olyan adattárakban, mint például az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket.
A gyors üzembe helyezési megtudhatja vizuálisan egysoros kód írása nélkül a data factory v2 folyamatok figyelése.
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="monitor-data-factory-v2-pipelines"></a>Data factory v2 folyamatok figyelése

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/).
3. Keresse meg a létrehozott adatok gyári panel az Azure-portálon, és kattintson a "A figyelő & kezelése" csempére. Ez elindítja a ADF v2 visual figyeléshez.

## <a name="list-view-monitoring"></a>Listanézet figyelés

A figyelő feldolgozási sorban lévő és a tevékenység fut, egy egyszerű lista nézet felületet. Minden futtatás a helyi böngésző időzónájában jelenik meg. Az időzóna módosításával minden dátum- és időmező át fog váltani a választott időzónára.  

#### <a name="monitoring-pipeline-runs"></a>Figyelési folyamat fut
A listanézetben látható a Data Factory v2 verzióban futó összes folyamat. Felvett oszlopok:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve. |
| Műveletek | Egyetlen művelettel tevékenységek megtekintéséhez. |
| Futtassa a Start | A következő feldolgozási sorban Futtatás kezdő időpontja (hh/nn/éééé, óó: pp: mm AM/PM) |
| Időtartam | Futtassa a időtartama (ÓÓ: pp:) |
| Kiváltó | Kézi indítási, ütemezés eseményindító |
| status | Nem sikerült, sikeres volt, a folyamatban lévő |
| Paraméterek | Feldolgozási sor futtatása (név, érték párok) paraméterek |
| Hiba | Feldolgozási sor hiba (ha/any) futtatása |
| Futtatási azonosító | Futtassa a folyamat azonosítója |

![Folyamatfuttatások monitorozása](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>Figyelési tevékenység fut
A listanézetben láthatók az egyes folyamatokhoz tartozó tevékenység-végrehajtások. Kattintson a **tevékenység fut** ikon alapján a **"Műveletek"** oszlopban megtekintheti a tevékenység futtatása minden folyamatkezelési futtatásához. Felvett oszlopok:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Műveletnév | A tevékenység belül a folyamat nevét. |
| Művelettípus | A tevékenység típusa például a másolás, HDInsightSpark, HDInsightHive stb. |
| Futtassa a Start | Futtatása tevékenység kezdési időpontja (hh/nn/éééé, óó: pp: mm AM/PM) |
| Időtartam | Futtassa a időtartama (ÓÓ: pp:) |
| status | Nem sikerült, sikeres volt, a folyamatban lévő |
| Input (Bemenet) | A tevékenység bemeneti leíró JSON-tömb |
| Kimenet | A tevékenység kimeneteiből leíró JSON-tömb |
| Hiba | Hiba (ha/any) futtatása tevékenységhez |

![Tevékenységfuttatások monitorozása](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Ha még **"Frissítés"** felül a feldolgozási sorban lévő és a tevékenység futtatása listájának frissítése ikonra. Az automatikus frissítés jelenleg nem támogatott.
>

![Frissítés](media/monitor-visually/refresh.png)

## <a name="features"></a>Szolgáltatások

#### <a name="rich-ordering-and-filtering"></a>Részletes rendezés és szűrés

Futtassa a Start desc és az ASC érték rendelés csővezeték ablakban fut, és szűrő folyamat fut a következő oszlop alapján:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve. Beállítások gyorsszűrők tartalmazza az "utolsó 24 óránként", "Elmúlt hét", "utolsó 30 nap", vagy válassza ki a megfelelő egyéni dátum. |
| Futtassa a Start | A következő feldolgozási sorban Futtatás kezdő időpontja |
| Futtatási állapot | Szűrő fut állapot szerint azaz sikeres, sikertelen, folyamatban lévő |

![Szűrés](media/monitor-visually/filter.png)

#### <a name="addremove-columns-to-list-view"></a>Oszlopok hozzáadása a listanézethez, és eltávolításuk
Kattintson a jobb gombbal a lista nézet fejléc és a lista nézetben megjelenítendő oszlopok kiválasztása

![Oszlopok](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>Átrendezése oszlopszélesség a listanézet
Növelheti és csökkentheti a listanézetben oszlopszélesség egyszerűen fölé az oszlop fejlécére

#### <a name="select-data-factory"></a>Adat-előállító kiválasztása
Vigye a mutatót a "Data Factory" ikonra a bal felső sarokban. Kattintson a "Nyíl" ikonra azure előfizetések és az adatok előállítók megfigyeléséhez listájának megjelenítéséhez.

![Adat-előállító kiválasztása](media/monitor-visually/select-datafactory.png)

#### <a name="guided-tours"></a>Az interaktív bemutatók
Kattintson a bal alsó a "információs ikon", majd kattintson a "Bemutatók interaktív" részletesen hozzáférhet a feldolgozási sorban lévő és a tevékenység futtatása figyelése.

![Az interaktív bemutatók](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>Visszajelzés
Kattintson az "Visszajelzés" ikonra, küldjön visszajelzést a különféle szolgáltatások vagy probléma merül fel, akkor előfordulhat, hogy elérhető.

![Visszajelzés](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>További lépések

Lásd: [figyelő programozott folyamatok kezelését és](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) cikkben tájékozódhat az figyelése és folyamatok kezelése

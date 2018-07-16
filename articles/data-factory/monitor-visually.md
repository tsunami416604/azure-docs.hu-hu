---
title: Azure-beli adat-előállítók vizuális monitorozására |} A Microsoft Docs
description: 'Útmutató: Azure adat-előállítók vizuális monitorozására'
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
ms.date: 07/12/2018
ms.author: shlo
ms.openlocfilehash: 4b3828e1857d17a128de346449d5cf2041709e50
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041075"
---
# <a name="visually-monitor-azure-data-factories"></a>Azure-beli adat-előállítók vizuális monitorozására
Az Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás. Lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek alkalmasak az adatok átvitelének és átalakításának irányítására és automatizálására. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok, feldolgozhatók és átalakíthatók az adatok különböző számítási szolgáltatások használatával (pl. Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning), és a kimeneti adatok közzétehetők olyan adattárakban, mint például az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket.
Ez a rövid útmutatóban, megtudhatja, hogyan vizuálisan egy egyetlen sor kód megírása nélkül a data factory v2-folyamatok figyelése.
Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="monitor-data-factory-v2-pipelines"></a>Data factory v2-folyamatok figyelése

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Jelentkezzen be a [az Azure portal](https://portal.azure.com/).
3. Keresse meg a létrehozott data factory panel az Azure Portalon, és kattintson a "Monitor & Manage" csempére. Ekkor megnyílik az ADF v2 visual figyeléshez.

## <a name="list-view-monitoring"></a>Listanézet figyelés

Egyszerű lista nézet felülettel folyamat és a tevékenységek futásának monitorozása. Minden futtatás a helyi böngésző időzónájában jelenik meg. Az időzóna módosításával minden dátum- és időmező át fog váltani a választott időzónára.  

#### <a name="monitoring-pipeline-runs"></a>Folyamatfuttatások figyelése
A listanézetben látható a Data Factory v2 verzióban futó összes folyamat. Belefoglalt oszlopok:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve. |
| Műveletek | Egyetlen művelettel tevékenységfuttatások megtekintéséhez. |
| Futtatás kezdete | Folyamat-Futtatás kezdő időpontja (hh/nn/éééé, óó: pp: mm AM és PM) |
| Időtartam | Futtatás időtartama (ÓÓ) |
| Kiváltó | Manuális eseményindító, az ütemezési eseményindító |
| status | Nem sikerült, a feladat végül sikerült folyamatban |
| Paraméterek | Folyamatfuttatás paraméterei (név, érték párok) |
| Hiba | Folyamat futásának (ha/any) hiba |
| Futtatási azonosító | A folyamat futásának azonosítóját |

![Folyamatfuttatások monitorozása](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>Tevékenység-végrehajtások figyelése
A listanézetben láthatók az egyes folyamatokhoz tartozó tevékenység-végrehajtások. Kattintson a **"Tevékenységfuttatások"** ikon mellett a **"Actions"** oszlopban megtekintheti a tevékenység fut minden folyamatfuttatáshoz. Belefoglalt oszlopok:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Műveletnév | A folyamat belül a tevékenység neve. |
| Művelettípus | A tevékenység típusa például másolás, HDInsightSpark, HDInsightHive stb. |
| Futtatás kezdete | Tevékenység-végrehajtásonként kezdő dátum (hh/nn/éééé, óó: pp: mm AM és PM) |
| Időtartam | Futtatás időtartama (ÓÓ) |
| status | Nem sikerült, a feladat végül sikerült folyamatban |
| Input (Bemenet) | A tevékenység bemeneti leíró JSON-tömb |
| Kimenet | A tevékenység kimeneteiből leíró JSON-tömb |
| Hiba | Tevékenységfuttatási hiba (ha/any) |

![Tevékenységfuttatások monitorozása](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Kattintson a kell **"Frissítés"** ikon felül a frissítési folyamat- és tevékenységfuttatások listája. Az automatikus frissítés jelenleg nem támogatott.
>

![Frissítés](media/monitor-visually/refresh.png)

## <a name="features"></a>Szolgáltatások

#### <a name="select-a-data-factory-to-monitor"></a>Válassza ki az adat-előállító figyelése
Vigye a mutatót a **adat-előállító** ikonra a bal felső sarokban. Kattintson a "" nyílikon megfigyeléséhez azure előfizetések és data factoryk listájának megtekintéséhez.

![Adat-előállító kiválasztása](media/monitor-visually/select-datafactory.png)

#### <a name="rich-ordering-and-filtering"></a>Részletes rendezés és szűrés

Rendelés folyamatfuttatásokat a az asc/desc elindításához futtassa, és szűrő folyamatfuttatásokat következő oszlopok:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve. Beállítások gyors szűrők belefoglalása: elmúlt 24 órában","Elmúlt hét","az elmúlt 30 nap", vagy válasszon ki egy egyéni dátum és időpont. |
| Futtatás kezdete | Folyamat futásának kezdő dátum |
| Futtatási állapota | Szűrő fut állapot szerint azaz sikeres, sikertelen, a folyamatban lévő |

![Szűrés](media/monitor-visually/filter.png)

#### <a name="addremove-columns-in-list-view"></a>A listanézetben oszlopok hozzáadása és eltávolítása
Kattintson a jobb gombbal a listanézet fejlécére, és válassza ki a nézetben megjeleníteni kívánt oszlopok

![Oszlopok](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>Listanézet oszlopszélességeinek átrendezése
Növelheti és csökkentheti a listanézet oszlopszélességeinek az az oszlop fejlécére egyszerűen egérmutatót

#### <a name="user-properties"></a>Felhasználói tulajdonságok

Minden entitás, amely nyomon követheti, hogy minden tevékenység karakterekhez is előléptetni egy felhasználói tulajdonságot. Például, előléptetheti az **forrás** és **cél** felhasználó tulajdonságai, a folyamat másolási tevékenysége tulajdonságait. Lehetőség kiválasztásával **automatikus** létrehozni a **forrás** és **cél** egy másolási tevékenység felhasználói tulajdonságok.

![Felhasználói tulajdonságok létrehozása](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Legfeljebb 5 folyamat tevékenység tulajdonságai csak előléptetni felhasználói tulajdonságokat.

Miután létrehozta a felhasználói tulajdonságok, ezt követően megfigyelheti őket a figyelési nézetekben listája. Ha a másolási tevékenység a forrás egy tábla nevét, a forrás-tábla neve egy oszlopot az a tevékenység futása lista nézetben követheti nyomon.

![Tevékenységfuttatások listája a felhasználói tulajdonságok nélkül](media/monitor-visually/monitor-user-properties-image2.png)

![Felhasználói tulajdonságok oszlopok hozzáadása a tevékenységek futtatások listája](media/monitor-visually/monitor-user-properties-image3.png)

![Tevékenységfuttatások listája a felhasználói tulajdonságok oszlopokkal](media/monitor-visually/monitor-user-properties-image4.png)

#### <a name="guided-tours"></a>Az interaktív bemutatók
Kattintson a bal alsó a "információ ikonra", majd kattintson a "Bemutatók irányított" a lépésenkénti útmutatást a folyamat és a tevékenységek futásának monitorozása.

![Az interaktív bemutatók](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>Visszajelzés
Kattintson a visszajelzését a különböző funkciókról és esetleges problémákat, amelyek előfordulnak "Visszajelzés" ikonra.

![Visszajelzés](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>További lépések

Lásd: [figyelő programozással felügyelheti a folyamatokat és](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) figyelése és kezelése a folyamatok kapcsolatos cikk

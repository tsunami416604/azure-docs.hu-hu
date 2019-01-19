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
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: shlo
ms.openlocfilehash: df684860cd3d1b6a002a300682ca4c6398461ba6
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2019
ms.locfileid: "54409962"
---
# <a name="visually-monitor-azure-data-factories"></a>Azure-beli adat-előállítók vizuális monitorozására
Az Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás. Lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek alkalmasak az adatok átvitelének és átalakításának irányítására és automatizálására. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok, feldolgozhatók és átalakíthatók az adatok különböző számítási szolgáltatások használatával (pl. Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning), és a kimeneti adatok közzétehetők olyan adattárakban, mint például az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket.

Ez a rövid útmutatóban megismerheti, hogyan vizuálisan egy egyetlen sor kód megírása nélkül a Data Factory-folyamatok figyelése.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="monitor-data-factory-pipelines"></a>Data Factory-folyamatok figyelése

Egyszerű lista nézet felülettel folyamat és a tevékenységek futásának monitorozása. Minden Futtatás a helyi böngésző időzónájában jelenik meg. Módosíthatja az időzónát, és minden dátum-idő mezők oszlopokhoz illesztés a választott időzónára.  

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Jelentkezzen be a [az Azure portal](https://portal.azure.com/).
3. A létrehozott data factory panel az Azure Portalon keresse meg, és kattintson a "Monitor & Manage" csempére, és indítsa el a Data Factory visual figyeléshez.

## <a name="monitor-pipeline-runs"></a>Folyamatfuttatások monitorozása
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
| Futtatási azonosító | A folyamat futásának Azonosítóját |

![Folyamatfuttatások monitorozása](media/monitor-visually/pipeline-runs.png)

## <a name="monitor-activity-runs"></a>Tevékenységfuttatások monitorozása
A listanézetben láthatók az egyes folyamatokhoz tartozó tevékenység-végrehajtások. Kattintson a **"Tevékenységfuttatások"** ikon mellett a **"Actions"** oszlopban megtekintheti a tevékenység fut minden folyamatfuttatáshoz. Belefoglalt oszlopok:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Műveletnév | A folyamat belül a tevékenység neve. |
| Tevékenységtípus | A tevékenység típusa, például a másolás, HDInsightSpark, HDInsightHive, stb. |
| Futtatás kezdete | Tevékenység-végrehajtásonként kezdő dátum (hh/nn/éééé, óó: pp: mm AM és PM) |
| Időtartam | Futtatás időtartama (ÓÓ) |
| status | Nem sikerült, a feladat végül sikerült folyamatban |
| Input (Bemenet) | A tevékenység bemeneti leíró JSON-tömb |
| Kimenet | A tevékenység kimeneteiből leíró JSON-tömb |
| Hiba | Tevékenységfuttatási hiba (ha/any) |

![Tevékenységfuttatások monitorozása](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Kattintson a kell **"Frissítés"** ikon felül a frissítési folyamat- és tevékenységfuttatások listája. Az automatikus frissítés jelenleg nem támogatott.

![Frissítés](media/monitor-visually/refresh.png)

## <a name="select-a-data-factory-to-monitor"></a>Válassza ki az adat-előállító figyelése
Vigye a mutatót a **adat-előállító** ikonra a bal felső sarokban. Kattintson a "" nyílikon megfigyeléséhez azure előfizetések és data factoryk listájának megtekintéséhez.

![Adat-előállító kiválasztása](media/monitor-visually/select-datafactory.png)

## <a name="configure-the-list-view"></a>A listanézet konfigurálása

### <a name="apply-rich-ordering-and-filtering"></a>Részletes rendezés és szűrés alkalmazása

Rendelés folyamatfuttatásokat a az asc/desc elindításához futtassa, és szűrő folyamatfuttatásokat következő oszlopok:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve. Beállítások gyors szűrők belefoglalása: elmúlt 24 órában","Elmúlt hét","az elmúlt 30 nap", vagy válasszon ki egy egyéni dátum és időpont. |
| Futtatás kezdete | Folyamat futásának kezdő dátum |
| Futtatási állapota | Állapot: sikeres, sikertelen, a folyamatban lévő szűrő futtathatja |

![Szűrés](media/monitor-visually/filter.png)

### <a name="add-or-remove-columns"></a>Oszlopok hozzáadása vagy eltávolítása
Kattintson a jobb gombbal a listanézet fejlécére, és válassza ki a nézetben megjeleníteni kívánt oszlopok

![Oszlopok](media/monitor-visually/columns.png)

### <a name="adjust-column-widths"></a>Oszlopszélességeinek módosítása
Növelheti és csökkentheti a listanézet oszlopszélességeinek az egérmutatót az oszlop fejlécére

## <a name="promote-user-properties-to-monitor"></a>Felhasználói tulajdonságok figyelése előléptetése

Minden entitás, amely nyomon követheti, hogy minden tevékenység karakterekhez is előléptetni egy felhasználói tulajdonságot. Például, előléptetheti az **forrás** és **cél** felhasználó tulajdonságai, a folyamat másolási tevékenysége tulajdonságait. Lehetőség kiválasztásával **automatikus** létrehozni a **forrás** és **cél** egy másolási tevékenység felhasználói tulajdonságok.

![Felhasználói tulajdonságok létrehozása](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> Legfeljebb 5 folyamat tevékenység tulajdonságai csak előléptetni felhasználói tulajdonságokat.

Miután létrehozta a felhasználói tulajdonságok, ezt követően megfigyelheti őket a figyelési nézetekben listája. Ha a másolási tevékenység a forrás egy tábla nevét, a forrás-tábla neve egy oszlopot az a tevékenység futása lista nézetben követheti nyomon.

![Tevékenységfuttatások listája a felhasználói tulajdonságok nélkül](media/monitor-visually/monitor-user-properties-image2.png)

![Felhasználói tulajdonságok oszlopok hozzáadása a tevékenységek futtatások listája](media/monitor-visually/monitor-user-properties-image3.png)

![Tevékenységfuttatások listája a felhasználói tulajdonságok oszlopokkal](media/monitor-visually/monitor-user-properties-image4.png)

## <a name="rerun-activities-inside-a-pipeline"></a>Futtassa újra a folyamatot belüli tevékenységek

Tevékenységek belüli folyamat most már futtathatja. Kattintson a **tevékenységfuttatások megtekintéséhez** , és válassza ki a tevékenységet a folyamat melyik pontból szeretné újra futtathatja a folyamatot.

![Tevékenységfuttatások megtekintése](media/monitor-visually/rerun-activities-image1.png)

![Válasszon egy tevékenységet](media/monitor-visually/rerun-activities-image2.png)

### <a name="view-rerun-history"></a>Futtassa újra a nézet előzmények

Újrafuttatás előzményeinek megtekintheti az összes a folyamatfuttatások a listanézetben.

![Előzmények megtekintése](media/monitor-visually/rerun-history-image1.png)

Egy adott folyamatfuttatást előzményeinek megtekintése, futtassa újra a is.

![Folyamatfuttatás előzményeinek megtekintése](media/monitor-visually/rerun-history-image2.png)

## <a name="guided-tours"></a>Az interaktív bemutatók
Kattintson a bal alsó a "információ ikonra", majd kattintson a "Bemutatók irányított" a lépésenkénti útmutatást a folyamat és a tevékenységek futásának monitorozása.

![Az interaktív bemutatók](media/monitor-visually/guided-tours.png)

## <a name="feedback"></a>Visszajelzés
Kattintson a visszajelzését a különböző funkciókról és esetleges problémákat, amelyek előfordulnak "Visszajelzés" ikonra.

![Visszajelzés](media/monitor-visually/feedback.png)

## <a name="alerts"></a>Riasztások

A támogatott mérőszámok az adat-előállító riasztásokat is növelheti. Válassza ki **-> figyelő, értesítések és metrikák** első lépések a Data Factory figyelő oldalon.

![](media/monitor-visually/alerts01.png)

A 7 perces bevezető és a funkció bemutatójáért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Riasztások létrehozása

1.  Kattintson a **Új riasztási szabály** egy új riasztást létrehozni.

    ![](media/monitor-visually/alerts02.png)

1.  Adja meg a szabály nevét, és válassza ki a riasztást **súlyossági**.

    ![](media/monitor-visually/alerts03.png)

1.  Válassza ki a riasztási feltételeket.

    ![](media/monitor-visually/alerts04.png)

    ![](media/monitor-visually/alerts05.png)

1.  Az Alert logic konfigurálása. A kiválasztott metrika folyamatok és a hozzájuk tartozó tevékenységek riasztást hozhat létre. Kiválaszthat egy adott tevékenység típusa, a tevékenység nevét, a folyamat neve vagy a egy hiba típusa is.

    ![](media/monitor-visually/alerts06.png)

1.  Konfigurálása **e-mailek és SMS és leküldéses/Hangvétel** a riasztáshoz tartozó értesítések. Hozzon létre vagy válasszon egy meglévő **műveletcsoport** a riasztási értesítésekhez.

    ![](media/monitor-visually/alerts07.png)

    ![](media/monitor-visually/alerts08.png)

1.  A riasztási szabályt létrehozni.

    ![](media/monitor-visually/alerts09.png)

## <a name="next-steps"></a>További lépések

Lásd: [figyelő programozással felügyelheti a folyamatokat és](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) figyelése és kezelése a folyamatok kapcsolatos cikket.

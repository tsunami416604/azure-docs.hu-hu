---
title: Hibakeresés az Azure Stream Analytics helyi lekérdezéseia Visual Studio feladatábrájával
description: Ez a cikk ismerteti, hogyan hibakeresés lekérdezések helyileg az Azure Stream Analytics-eszközök a Visual Studio feladatdiagram használatával.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 106b1f0b765700803d2cd55b5e049fae5be3dfad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76847198"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Hibakeresés az Azure Stream Analytics helyi lekérdezéseia Visual Studio feladatábrájával

Olyan feladatok, amelyek nem eredményeznek eredményt vagy váratlan eredményeket, gyakori hibaelhárítási forgatókönyvek a streamelési lekérdezésekhez. A feladatdiagram ot a lekérdezés helyi tesztelése közben használhatja a Visual Studióban, hogy megvizsgálja az egyes lépésekkel kapcsolatban a köztes eredményhalmazt és a metrikákat. A feladatábrák segítségével gyorsan elkülönítheti a probléma forrását a problémák elhárításakor.

## <a name="debug-a-query-using-job-diagram"></a>Lekérdezés hibakeresése feladatdiagram használatával

Az Azure Stream Analytics-parancsfájl a bemeneti adatok kimeneti adatokká alakítására szolgál. A feladatdiagram bemutatja, hogyan áramlik a bemeneti forrásokból (Event Hub, IoT Hub, stb.) több lekérdezési lépés, és végül a kimeneti fogadók. Minden lekérdezési lépés egy `WITH` utasítás használatával a parancsfájlban definiált ideiglenes eredményhalmazhoz van rendelve. Megtekintheti az adatokat, valamint a metrikák minden lekérdezési lépés minden köztes eredményhalmaz, hogy megtalálják a forrása a probléma.

> [!NOTE]
> Ez a feladatdiagram csak a helyi tesztelés adatait és metrikákat jeleníti meg egyetlen csomópontban. Nem használható teljesítményhangolásra és hibaelhárításra.

### <a name="start-local-testing"></a>Helyi tesztelés megkezdése

Ebből a [rövid útmutatóból](stream-analytics-quick-create-vs.md) megtudhatja, hogyan hozhat létre Stream Analytics-feladatot a Visual Studio használatával, illetve hogyan [exportálhat egy meglévő feladatot egy helyi projektbe.](stream-analytics-vs-tools.md#export-jobs-to-a-project) Ha a lekérdezést helyi bemeneti adatokkal szeretné tesztelni, kövesse az [alábbi utasításokat.](stream-analytics-live-data-local-testing.md) Ha élő bevitellel szeretné tesztelni, lépjen a következő lépésre.

> [!NOTE]
> Ha exportál egy feladatot a helyi projektbe, és egy élő bemeneti adatfolyamon szeretne tesztelni, újra meg kell adnia az összes bemenet hitelesítő adatait.  

Válassza ki a bemeneti és kimeneti forrást a parancsfájlszerkesztőből, és válassza a **Futtatás helyileg**lehetőséget. A feladatdiagram a jobb oldalon jelenik meg.

### <a name="view-the-intermediate-result-set"></a>A köztes eredményhalmaz megtekintése  

1. A parancsfájlra való navigáláshoz válassza ki a lekérdezési lépést. A rendszer automatikusan a megfelelő parancsfájlhoz irányítja a bal oldali szerkesztőben.

   ![Navigálás a feladatdiagramparancsfájlban](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Jelölje ki a lekérdezési lépést, és válassza az **Előnézet** lehetőséget a felpezsdüljön párbeszédpanelen. Az eredményhalmaz az alsó eredményablak egyik lapján jelenik meg.

   ![Feladatdiagram előnézetének eredménye](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Lépésmérők megtekintése

Ebben a szakaszban a diagram egyes részeihez elérhető mutatókat ismerteti.

#### <a name="input-sources-live-stream"></a>Bemeneti források (élő közvetítés)

![Feladatdiagram élő bemeneti forrásai](./media/debug-locally-using-job-diagram/live-input.png)

|Metrika|Leírás|
|-|-|
|**TaxiRide**| A bemenet neve.|
|**Eseményközpont** | Bemeneti forrás típusa.|
|**Események**|A felolvasott események száma.|
|**Eldugott eseményforrások**|Hány további üzeneteket kell olvasni az Event Hubs és az IoT Hub bemenetek.|
|**Események bájtban**|Az olvasott bájtok száma.|
| **Leromlott események**|Azoknak az eseményeknek a száma, amelyek nem deszerializálási problémával kapcsolatosak.|
|**Korai események**| Azoknak az eseményeknek a száma, amelyek alkalmazásidőbélyeggel rendelkeznek a magas vízjel előtt.|
|**Késői események**| Az alkalmazások időbélyeggel a magas vízjel után lévő események száma.|
|**Eseményforrások**| A beolvasott adategységek száma. Például a blobok száma.|

#### <a name="input-sources-local-input"></a>Bemeneti források (helyi bemenet)

![Feladatdiagram helyi bemeneti forrásai](./media/debug-locally-using-job-diagram/local-input.png)

|Metrika|Leírás|
|-|-|
|**TaxiRide**| A bemenet neve.|
|**Sorszám**| A lépésből létrehozott sorok száma.|
|**Adatméret**| Az ebből a lépésből létrehozott adatok mérete.|
|**Helyi bevitel**| Helyi adatok használata bevitelként.|

#### <a name="query-steps"></a>A lekérdezés lépései

![Feladatdiagram lekérdezési lépése](./media/debug-locally-using-job-diagram/query-step.png)

|Metrika|Leírás|
|-|-|
|**TripData**|Az ideiglenes eredményhalmaz neve.|
|**Sorszám**| A lépésből létrehozott sorok száma.|
|**Adatméret**| Az ebből a lépésből létrehozott adatok mérete.|
  
#### <a name="output-sinks-live-output"></a>Kimeneti fogadók (élő kimenet)

![Feladatdiagram helyi kimeneti fogadói](./media/debug-locally-using-job-diagram/live-output.png)

|Metrika|Leírás|
|-|-|
|**regionaggEH**|A kimenet neve.|
|**Események**|A fogadók hoz létreaz események kimenetének száma.|

#### <a name="output-sinks-local-output"></a>Kimeneti fogadók (helyi kimenet)

![Feladatdiagram helyi kimeneti fogadói](./media/debug-locally-using-job-diagram/local-output.png)

|Metrika|Leírás|
|-|-|
|**regionaggEH**|A kimenet neve.|
|**Helyi kimenet**| Eredménykimenet helyi fájlba.|
|**Sorszám**| A helyi fájlba kimeneti sorok száma.|
|**Adatméret**| A helyi fájlba irányuló adatkimenet mérete.|

### <a name="close-job-diagram"></a>Munkadiagram bezárása

Ha már nincs szüksége a feladatdiagramra, válassza a Jobb felső sarokban a **Bezárás** lehetőséget. A diagramablak bezárása után újra el kell kezdenie a helyi tesztelést, hogy láthassa.

### <a name="view-job-level-metrics-and-stop-running"></a>Feladatszint-mérőszámok megtekintése és futás ának leállítása

Más feladatszintű metrikák jelennek meg az előugró konzolon. Ha le szeretné állítani a feladatot, nyomja le a **Ctrl+C billentyűkombinációt** a konzolon.

![Feladatdiagram leállítási feladata](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Korlátozások

* A Power BI és az Azure Data Lake Storage Gen1 kimeneti fogadók nem támogatottak a hitelesítési modell korlátai miatt.

* Csak a felhőalapú beviteli beállítások rendelkeznek [időházirend-támogatással,](stream-analytics-out-of-order-and-late-events.md) míg a helyi beviteli beállítások nem.

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: Stream Analytics-feladat létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Az Azure Stream Analytics-feladatok megtekintése a Visual Studióval](stream-analytics-vs-tools.md)
* [Élő adatok helyi tesztelése az Azure Stream Analytics-eszközök kel a Visual Studio számára (előzetes verzió)](stream-analytics-live-data-local-testing.md)

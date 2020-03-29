---
title: Élő adatok tesztelése az Azure Stream Analytics for Visual Studio szolgáltatással
description: Ismerje meg, hogyan tesztelheti az Azure Stream Analytics-feladatot helyileg az élő streamelési adatok használatával.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: f2876ea32bdcd900a454ae6b7ac58c11b8ec67c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840485"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Élő adatok helyi tesztelése az Azure Stream Analytics-eszközök kel a Visual Studio számára (előzetes verzió)

A Visual Studio Azure Stream Analytics-eszközei lehetővé teszik, hogy az Azure Event Hubról, az IoT Hubról és a Blob Storageból származó élő eseménystreamek használatával helyileg tesztelje a feladatokat az IDE-ből. Az élő adatok helyi tesztelése nem helyettesítheti a felhőben végrehajtható [teljesítmény- és méretezhetőségi tesztelést,](stream-analytics-streaming-unit-consumption.md) de a funkcionális tesztelés során időt takaríthat meg, ha nem kell minden alkalommal, amikor tesztelni szeretné a Stream Analytics-feladatot, nem kell beküldenie a felhőbe. Ez a funkció előzetes verzióban érhető el, és nem használható éles számítási feladatokhoz.

## <a name="testing-options"></a>Tesztelési lehetőségek

A következő helyi tesztelési lehetőségek támogatottak:

|**Bemenet**  |**Kimenet**  |**Feladat típusa**  |
|---------|---------|---------|
|Helyi statikus adatok   |  Helyi statikus adatok   |   Felhő/perem |
|Élő bemeneti adatok   |  Helyi statikus adatok   |   Felhő |
|Élő bemeneti adatok   |  Élő kimeneti adatok   |   Felhő |

## <a name="local-testing-with-live-data"></a>Helyi tesztelés élő adatokkal

1. Miután létrehozott egy [Azure Stream Analytics felhőprojektet a Visual Studióban,](stream-analytics-quick-create-vs.md)nyissa meg a **script.asaql**alkalmazást. A helyi tesztelés alapértelmezés szerint helyi bemenetet és helyi kimenetet használ.

   ![Az Azure Stream Analytics Visual Studio helyi bemenete és helyi kimenete](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Az élő adatok teszteléséhez válassza a legördülő lista **Felhőbevitel használata lehetőséget.**

   ![Az Azure Stream Analytics Visual Studio élő felhőalapú bevitele](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. Állítsa be a **kezdési időt** annak meghatározására, hogy a feladat mikor kezdi meg a bemeneti adatok feldolgozását. Előfordulhat, hogy a pontos eredmények érdekében a feladatnak előre be kell olvasnia a bemeneti adatokat. Az alapértelmezett idő 30 perccel az aktuális idő előtt van beállítva.

   ![Az Azure Stream Analytics Visual Studio élő adatok kezdési időpontja](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Kattintson a **Futtatás helyileg gombra.** Megjelenik egy konzolablak a futó folyamattal és feladatmérőszámokkal. Ha le szeretné állítani a folyamatot, ezt manuálisan is megteheti. 

   ![Az Azure Stream Analytics Visual Studio élő adatfeldolgozási folyamatának ablaka](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   A kimeneti eredmények három másodpercenként frissülnek a helyi futtatási eredményablak első 500 kimeneti sorával, és a kimeneti fájlok a projekt elérési **útBA, az ASALocalRun** mappába kerülnek. A kimeneti fájlokat úgy is megnyithatja, hogy a helyi futtatáseredményablakBan az **Eredmények mappa megnyitása** gombra kattint.

   ![Az Azure Stream Analytics Visual Studio élő adatok megnyitása találati mappája](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Ha azt szeretné, hogy az eredmények et a felhőkimeneti fogadók, válassza **a Kimenet a felhőbe** a második legördülő mezőbe. A Power BI és az Azure Data Lake Storage nem támogatott kimeneti fogadók.

   ![Az Azure Stream Analytics Visual Studio élő adatkimenete a felhőbe](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Korlátozások

* A Power BI és az Azure Data Lake Storage nem támogatott kimeneti fogadók a hitelesítési modell korlátai miatt.

* Csak a felhőalapú beviteli beállítások rendelkeznek [időházirend-támogatással,](stream-analytics-out-of-order-and-late-events.md) míg a helyi beviteli beállítások nem.

## <a name="next-steps"></a>További lépések

* [Stream Analytics-feladat létrehozása az Azure Stream Analytics-eszközök kel a Visual Studio számára](stream-analytics-quick-create-vs.md)
* [A Test Stream Analytics lekérdezései helyileg a Visual Studióval](stream-analytics-vs-tools-local-run.md)
* [Az Azure Stream Analytics-feladatok megtekintése a Visual Studióval](stream-analytics-vs-tools.md)

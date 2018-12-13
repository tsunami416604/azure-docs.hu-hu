---
title: Az Azure Stream Analytics élő adatok tesztelése a Visual Studio
description: Ismerje meg, hogyan tesztelheti az Azure Stream Analytics-feladat használatával helyben élő streamelési adatok.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ea55d2f96a87503d43a69d288ce85dcff32a39ce
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090329"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Teszt élő adatok használatával helyben az Azure Stream Analytics tools for Visual Studio (előzetes verzió)

Az Azure Stream Analytics tools for Visual Studio lehetővé teszi az IDE-ből helyileg feladatok tesztelése élő adatfolyamokat az Azure Event Hub, IoT Hub és a Blob Storage használatával. Élő adatok helyi tesztelést nem lehet kicserélni a [teljesítmény és méretezhetőség tesztelése](stream-analytics-streaming-unit-consumption.md) is végezhet a felhőben, de mentheti ideje alatt nem kellene elküldeni a felhőbe minden alkalommal, amikor a Stream tesztelni szeretné a tesztelési célokra Analytics-feladatot. Ez a funkció előzetes verzióban érhető el, és nem használható éles számítási feladatok esetében.

## <a name="testing-options"></a>Tesztelési beállítások

A következő helyi tesztelési beállítások támogatottak:

|**Input** (Bemenet)  |**Kimenet**  |**Feladat típusa**  |
|---------|---------|---------|
|Helyi statikus adatok   |  Helyi statikus adatok   |   Felhőalapú és peremhálózati |
|A bemeneti élő adatok   |  Helyi statikus adatok   |   Felhő |
|A bemeneti élő adatok   |  Élő kimeneti adatok   |   Felhő |

## <a name="local-testing-with-live-data"></a>Élő adatok helyi tesztelése

1. Miután létrehozott egy [Azure Stream Analytics felhőalapú projektre a Visual Studióban](stream-analytics-quick-create-vs.md), nyissa meg **script.asaql**. A helyi tesztelés helyi kimenetként és bemenetként helyi alapértelmezés szerint használ.

   ![Az Azure Stream Analytics Visual Studio helyi bemeneti és a helyi kimenete](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Az élő adatok teszteléséhez válassza **használható felhőalapú bemeneti** elemet a legördülő listából.

   ![Az Azure Stream Analytics Visual Studio cloud élő bemenet](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. Állítsa be a **kezdő időpont** definiálásához, ha a feladat elkezdi feldolgozni a bemeneti adatokat. A feladat bemeneti adatokat a pontos eredmények biztosításához előre lehet szükség. Alapértelmezett az aktuális időpont előtt 30 percre van beállítva.

   ![Az Azure Stream Analytics Visual Studio élő adatok kezdési időpontja](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Kattintson a **helyileg történő futtatása**. A konzolablakban megjelenik a futó folyamat és a feladat metrikákkal. Ha azt szeretné, a folyamat leállítása érdekében, megteheti manuálisan. 

   ![Az Azure Stream Analytics Visual Studio élő adatok folyamat ablak](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   A kimeneti eredmények a helyi futtatás eredménye ablakban első 500 kimeneti sorait ot három másodperc alatt frissülnek, és a kimeneti fájlok kerülnek a projekt elérési útját **ASALocalRun** mappát. A kimeneti fájlok kattintva is megnyithatja **eredmények mappa megnyitása** gomb a helyi futtatási eredmények ablakában.

   ![Az Azure Stream Analytics Visual Studio élő adatok eredmények mappa megnyitása](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Ha szeretné az eredményeket a felhő kimeneti fogadóinak, válassza a **felhőbe kimeneti** a második legördülő listából. A Power BI és az Azure Data Lake Storage nejsou támogatott kimeneti fogadóként.

   ![Az Azure Stream Analytics Visual Studio élő adatok felhőbe kimeneti](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Korlátozások

* A Power BI és az Azure Data Lake Storage nejsou támogatott kimeneti fogadóinak hitelesítési modell korlátozásai miatt.

* Csak a felhőben bemeneti lehetőségek [házirendek idő](stream-analytics-out-of-order-and-late-events.md) támogatják, míg a helyi adatbeviteli beállítások nem.

## <a name="next-steps"></a>További lépések

* [Stream Analytics-feladat létrehozása az Azure Stream Analytics tools for Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Az Azure Stream Analytics tools for Visual Studio telepítése](stream-analytics-tools-for-visual-studio-install.md)
* [Stream Analytics-lekérdezések Visual studióval helyileg tesztelése](stream-analytics-vs-tools-local-run.md)
* [A Visual Studio használata az Azure Stream Analytics-feladatok megtekintése](stream-analytics-vs-tools.md)
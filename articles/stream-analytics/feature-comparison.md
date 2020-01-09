---
title: Azure Stream Analytics szolgáltatások összehasonlítása
description: Ez a cikk a Azure Portal, a Visual Studio és a Visual Studio Code-ban Azure Stream Analytics Felhőbeli és IoT Edge feladatokhoz támogatott funkciókat hasonlítja össze.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 8cdba27ac949584e1fa96e3f7b0874f4fc0d4212
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443664"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure Stream Analytics szolgáltatások összehasonlítása

A Azure Stream Analytics segítségével a felhőben és a IoT Edge a [Azure Portal](stream-analytics-quick-create-portal.md), a [Visual Studio](stream-analytics-quick-create-vs.md)és a [Visual Studio Code](quick-create-vs-code.md)használatával hozhat létre streaming-megoldásokat. A cikkben szereplő táblázatok azt mutatják be, hogy az egyes platformok milyen szolgáltatásokat támogatnak mindkét feladattípus esetében.

## <a name="cloud-job-features"></a>Cloud Job-funkciók


|Szolgáltatás  |Portál  |Visual Studio  |Visual Studio-kód  |
|---------|---------|---------|---------|
|Platformfüggetlen     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Parancsfájlok készítése     |Igen         |Igen         |Igen         |
|Parancsfájl IntelliSense     |Szintaxis kiemelése         |Szintaxis kiemelése</br>Kód befejezése</br>Hiba jelölője         |Szintaxis kiemelése</br>Kód befejezése</br>Hiba jelölője         |
|A bemenetek, a kimenetek és a feladatok összes típusának meghatározása     |Igen         |Igen         |Igen         |
|Verziókövetés     |Nem         |Igen         |Igen         |
|CI/CD-támogatás     |Részleges         |Igen         |Igen         |
|Bemenetek és kimenetek megosztása több lekérdezés között     |Nem         |Igen         |Igen         |
|Tesztelés lekérdezése egy minta fájllal     |Igen         |Igen        |Igen         |
|Élő adathelyi tesztelés     |Nem         |Igen       |Igen      |
|Feladatok listázása és feladat-entitások megtekintése     |Igen         |Igen        |Igen         |
|Feladatok exportálása helyi projektbe     |Nem         |Igen         |Igen         |
|Feladatok elküldése, elindítása és leállítása     |Igen         |Igen         |Igen         |
|Feladatok metrikáinak és diagramjának megtekintése     |Igen         |Igen         |Megnyitás a portálon         |
|Feladatok futásidejű hibáinak megtekintése     |Igen         |Igen         |Nem         |
|Diagnosztikai naplók     |Igen         |Nem         |Nem         |
|Egyéni üzenet tulajdonságai     |Igen         |Igen         |Nem       |
|C#Egyéni kód funkció és deszerializáló|Írásvédett mód|Igen|Nem|
|JavaScript UDF és UDA     |Igen         |Igen         |Csak Windowson         |
|Machine Learning Service     |Igen, de a lekérdezés nem tesztelhető        |Igen         |Nem         |
|Machine Learning Studio     |Igen, de a lekérdezés nem tesztelhető        |Igen |Nem         |
|Kompatibilitási szint     |1.0</br>1.1</br>1,2 (alapértelmezett)         |1.0</br>1.1</br>1,2 (alapértelmezett)           |1.0</br>1.1</br>1,2 (alapértelmezett)           |
|Beépített ML-alapú anomáliák észlelési funkciói     |Igen         |Igen         |Igen         |
|Beépített térinformatikai függvények     |Igen         |Igen         |Igen         |



## <a name="iot-edge-job-features"></a>IoT Edge feladatok funkciói

|Szolgáltatás  |Portál  |Visual Studio  |Visual Studio-kód  |
|---------|---------|---------|---------|
|Feladatok készítése     |Igen         |Igen         |Nem         |
|Verziókövetés     |Nem         |Igen         |Nem         |
|Feladatok exportálása helyi projektbe     |Nem         |Igen         |Nem         |
|Tesztelés lekérdezése egy minta fájllal     |Igen         |Igen         |Nem         |
|Bemenetek és kimenetek megosztása több lekérdezés között     |Nem         |Igen         |Nem         |
|C#UDF     |Nem         |Igen         |Nem         |
|Feladatok elküldése     |Igen         |Igen         |Nem         |
|Feladatok listázása és feladat-entitások megtekintése     |Igen         |Igen         |Nem         |
|Feladatok metrikáinak és diagramjának megtekintése     |Igen         |Részleges         |Nem         |
|Feladatok futásidejű hibáinak megtekintése     |Igen         |Részleges         |Nem         |
|CI/CD-támogatás     |Nem         |Nem         |Nem         |


## <a name="next-steps"></a>Következő lépések

* [IoT Edge-eszközökön futó Azure Stream Analytics](stream-analytics-edge.md)
* [Oktatóanyag: C# felhasználó által definiált függvény írása Azure stream Analytics IoT Edge feladathoz (előzetes verzió)](stream-analytics-edge-csharp-udf.md)
* [Stream Analytics IoT Edge feladatok fejlesztése a Visual Studio Tools használatával](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [A Visual Studio használata Azure Stream Analytics feladatok megtekintéséhez](stream-analytics-vs-tools.md)
* [Ismerkedés a Azure Stream Analytics a Visual Studio Code (előzetes verzió) szolgáltatással](visual-studio-code-explore-jobs.md)



---
title: Az Azure Stream Analytics funkcióinak összehasonlítása
description: Ez a cikk összehasonlítja az Azure Stream Analytics felhő- és IoT Edge-feladatokhoz támogatott funkciókat az Azure Portalon, a Visual Studio-ban és a Visual Studio-kódban.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: cdae6a3b7319aefa9d4f19b5d613d1afb8b6804a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235313"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Az Azure Stream Analytics funkcióinak összehasonlítása

Az Azure Stream Analytics segítségével streamelési megoldásokat hozhat létre a felhőben és az IoT Edge-ben az [Azure Portal](stream-analytics-quick-create-portal.md), a [Visual Studio](stream-analytics-quick-create-vs.md)és a Visual Studio [Code használatával.](quick-create-vs-code.md) A cikkben található táblázatok azt mutatják, hogy az egyes platformok mely funkciókat támogatják mindkét feladattípushoz.

## <a name="cloud-job-features"></a>A felhőalapú feladat funkciói


|Szolgáltatás  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Platformfüggetlen     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Parancsfájl-készítés     |Igen         |Igen         |Igen         |
|Script Intellisense     |Szintaxis kiemelése         |Szintaxis kiemelése</br>Kód kiegészítése</br>Hibajelölő         |Szintaxis kiemelése</br>Kód kiegészítése</br>Hibajelölő         |
|A bemenetek, kimenetek és feladatkonfigurációk összes típusának meghatározása     |Igen         |Igen         |Igen         |
|Verziókövetés     |Nem         |Igen         |Igen         |
|CI/CD támogatás     |Részleges         |Igen         |Igen         |
|Bemenetek és kimenetek megosztása több lekérdezésben     |Nem         |Igen         |Igen         |
|Lekérdezéstesztelés mintafájllal     |Igen         |Igen        |Igen         |
|Élő adatok helyi tesztelése     |Nem         |Igen       |Igen      |
|Feladatok listázása és feladatentitások megtekintése     |Igen         |Igen        |Igen         |
|Feladat exportálása helyi projektbe     |Nem         |Igen         |Igen         |
|Feladatok beküldése, indítása és leállítása     |Igen         |Igen         |Igen         |
|Feladatmérőszámok és diagramok megtekintése     |Igen         |Igen         |Megnyitás a portálon         |
|Feladatfutásidejű hibáinak megtekintése     |Igen         |Igen         |Nem         |
|Diagnosztikai naplók     |Igen         |Nem         |Nem         |
|Egyéni üzenetek tulajdonságai     |Igen         |Igen         |Nem       |
|C# egyéni kódfüggvény és Deszerializáló|Írásvédett mód|Igen|Nem|
|JavaScript UDF és UDA     |Igen         |Igen         |Csak Windowson         |
|Machine Learning-szolgáltatás     |Igen        |Igen         |Nem         |
|Machine Learning Studio     |Igen, de a lekérdezés nem vizsgálható        |Igen |Nem         |
|Kompatibilitási szint     |1.0</br>1.1</br>1.2 (alapértelmezett)         |1.0</br>1.1</br>1.2 (alapértelmezett)           |1.0</br>1.1</br>1.2 (alapértelmezett)           |
|Beépített ML-alapú anomáliadetektálási funkciók     |Igen         |Igen         |Igen         |
|Beépített térinformatikai függvények     |Igen         |Igen         |Igen         |



## <a name="iot-edge-job-features"></a>Az IoT Edge feladat funkciói

|Szolgáltatás  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Feladat készítése     |Igen         |Igen         |Nem         |
|Verziókövetés     |Nem         |Igen         |Nem         |
|Feladat exportálása helyi projektbe     |Nem         |Igen         |Nem         |
|Lekérdezéstesztelés mintafájllal     |Igen         |Igen         |Nem         |
|Bemenetek és kimenetek megosztása több lekérdezésben     |Nem         |Igen         |Nem         |
|C# UDF     |Nem         |Igen         |Nem         |
|Feladatok beküldése     |Igen         |Igen         |Nem         |
|Feladatok listázása és feladatentitások megtekintése     |Igen         |Igen         |Nem         |
|Feladatmérőszámok és diagramok megtekintése     |Igen         |Részleges         |Nem         |
|Feladatfutásidejű hibáinak megtekintése     |Igen         |Részleges         |Nem         |
|CI/CD támogatás     |Nem         |Nem         |Nem         |


## <a name="next-steps"></a>További lépések

* [IoT Edge-eszközökön futó Azure Stream Analytics](stream-analytics-edge.md)
* [Oktatóanyag: C# felhasználó által definiált függvény írása az Azure Stream Analytics IoT Edge-feladathoz (előzetes verzió)](stream-analytics-edge-csharp-udf.md)
* [Stream Analytics IoT Edge-feladatok fejlesztése a Visual Studio eszközeivel](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Az Azure Stream Analytics-feladatok megtekintése a Visual Studióval](stream-analytics-vs-tools.md)
* [Az Azure Stream Analytics felfedezése a Visual Studio-kóddal (előzetes verzió)](visual-studio-code-explore-jobs.md)



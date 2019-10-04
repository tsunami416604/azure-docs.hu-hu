---
title: Az Azure Stream Analytics szolgáltatás összehasonlítása
description: Ez a cikk összehasonlítja az Azure Stream Analytics felhőalapú és az IoT Edge-feladatok az Azure portal, a Visual Studio és a Visual Studio Code támogatott funkciókról.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 22d7ef90ee0cf4d09467516b7bb0664327b7dabe
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509780"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Az Azure Stream Analytics szolgáltatás összehasonlítása

Az Azure Stream Analytics, megoldásokat hozhat létre streamelési a felhőben és az IoT Edge használatával [az Azure portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md), és [Visual Studio Code](quick-create-vs-code.md). Az ebben a cikkben táblázatokban mindkét projekt esetében minden egyes platform által támogatott szolgáltatásokat.

## <a name="cloud-job-features"></a>Felhőalapú feladat funkciók


|Funkció  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Platformokon átívelő     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Parancsprogram készítése     |Igen         |Igen         |Igen         |
|Parancsfájl Intellisense     |Szintaxiselemek kiemelése         |Szintaxiselemek kiemelése</br>Kódkiegészítés</br>Hiba jelölő         |Szintaxiselemek kiemelése</br>Kódkiegészítés</br>Hiba jelölő         |
|Bemenetek, kimenetek és feladatkonfigurációkat megadása     |Igen         |Igen         |Igen         |
|BLOB kimeneti particionálása     |Igen         |Igen         |Igen         |
|A Power BI kimenetként     |Igen         |Igen         |Nem         |
|Az SQL database referenciaadatok     |Igen         |Igen         |Igen         |
|Egyéni üzenettulajdonságok     |Igen         |Nem         |Nem         |
|Bemenetek és kimenetek megosztása több lekérdezés     |Nem         |Igen         |Igen         |
|JavaScript UDF és UDA     |Igen         |Igen         |Csak Windows         |
|Machine Learning felhasználásával     |Igen, de a lekérdezés nem tesztelhető        |Igen, de nem tesztelhető, helyileg         |Nem         |
|Kompatibilitási szint     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|Anomáliadetektálás gépi tanulás alapú beépített függvények     |Igen         |Igen         |Igen         |
|Beépített térinformatikai függvényekkel     |Igen         |Igen         |Igen         |
|A lekérdezés egy mintafájlt a tesztelés     |Igen         |Igen         |Igen         |
|Élő adatok helyi tesztelése     |Nem         |Igen         |Nem         |
|Feladatok listázása és entitások feladat megtekintése     |Igen         |Igen         |Igen         |
|Feladatok exportálása helyi-projektbe     |Nem         |Igen         |Igen         |
|Küldje el, a kezdés és a feladatok leállítása     |Igen         |Igen         |Igen         |
|Verziókövetés     |Nem         |Igen         |Igen         |
|CI/CD-támogatás     |Részleges         |Igen         |Igen         |
|Feladat metrikáinak megtekintése-diagram     |Igen         |Igen         |Megnyitás a portálon         |
|Feladat futásidejű hibák megtekintése     |Igen         |Igen         |Nem         |
|Diagnosztikai naplók     |Igen         |Nem         |Nem         |


## <a name="iot-edge-job-features"></a>IoT Edge-feladat funkciók

|Funkció  |Portál  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Szerzői feladatok     |Igen         |Igen         |Nem         |
|Verziókövetés     |Nem         |Igen         |Nem         |
|Feladatok exportálása helyi-projektbe     |Nem         |Igen         |Nem         |
|A lekérdezés egy mintafájlt a tesztelés     |Igen         |Igen         |Nem         |
|Bemenetek és kimenetek megosztása több lekérdezés     |Nem         |Igen         |Nem         |
|C# UDF     |Nem         |Igen         |Nem         |
|Küldje el, a kezdés és a feladatok leállítása     |Igen         |Igen         |Nem         |
|Feladatok listázása és entitások feladat megtekintése     |Igen         |Igen         |Nem         |
|Feladat metrikáinak megtekintése-diagram     |Igen         |Részleges         |Nem         |
|Feladat futásidejű hibák megtekintése     |Igen         |Részleges         |Nem         |
|CI/CD-támogatás     |Nem         |Nem         |Nem         |


## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics az IoT Edge-ben](stream-analytics-edge.md)
* [Oktatóanyag: Írhat egy C# felhasználó által definiált függvény Azure Stream Analytics az IoT Edge-feladat (előzetes verzió)](stream-analytics-edge-csharp-udf.md)
* [A Visual Studio-eszközökkel Stream Analytics az IoT Edge-feladatok fejlesztése](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [A Visual Studio használata az Azure Stream Analytics-feladatok megtekintése](stream-analytics-vs-tools.md)
* [Ismerkedés az Azure Stream Analytics Visual Studio Code-dal (előzetes verzió)](vscode-explore-jobs.md)



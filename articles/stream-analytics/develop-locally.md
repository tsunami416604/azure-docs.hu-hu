---
title: Az Azure Stream Analytics-feladatok helyi fejlesztése és hibakeresése
description: Ismerje meg, hogyan fejlesztheti és tesztelheti az Azure Stream Analytics-feladatokat a helyi számítógépen, mielőtt futtatna őket az Azure Portalon.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879842"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Az Azure Stream Analytics-feladatok helyi fejlesztése és hibakeresése

Miközben azure Stream Analytics-feladatokat hozhat létre és tesztelhet az Azure Portalon, sok fejlesztő a helyi fejlesztési élményt részesíti előnyben. A Stream Analytics segítségével egyszerűen használhatja kedvenc kódszerkesztőjét és fejlesztői eszközeit, hogy az Azure Event Hubról, az IoT Hubról és a Blob Storageból származó élő eseménystreamekkel hozzon létre és teszteljen feladatokat egy teljesen működőképes egycsomópontos helyi futásidejű használatával. A feladatokat közvetlenül a helyi fejlesztési környezetből is elküldheti az Azure-ba.

## <a name="local-development-environments"></a>Helyi fejlesztési környezetek

A Stream Analytics-feladatok helyi számítógépen történő fejlesztésének módja az eszközbeállításoktól és a funkciók elérhetőségétől függ. Tekintse meg [az Azure Stream Analytics-funkciók összehasonlítását,](feature-comparison.md) és tekintse meg, hogy az egyes fejlesztési környezetekben mely funkciók támogatottak.

Az alábbi táblázatban található környezetek támogatják a helyi fejlesztést:

|Környezet                              |Leírás    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| Az [Azure Stream Analytics Tools bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) a Visual Studio-kódhoz lehetővé teszi, hogy a Stream analytics-feladatát helyileg és a felhőben is megmunkálja, kezelje és tesztelje gazdag IntelliSense és natív forrásvezérlés segítségével. Támogatja a fejlesztést Linux, MacOS és Windows rendszeren. További információ: [Azure Stream Analytics-feladat létrehozása a Visual Studio-kódban](quick-create-vs-code.md)című témakörben olvashat.|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |A Stream Analytics Tools az Azure fejlesztési és adattárolási és -feldolgozási számítási feladatainak része a Visual Studióban. A Visual Studio segítségével egyéni C# felhasználó által definiált függvényeket és deszerializálókat írhat. További információ: [Azure Stream Analytics-feladat létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)című témakörben olvashat.|
|[Parancssor vagy terminál](stream-analytics-tools-for-visual-studio-cicd.md)|Az Azure Stream Analytics CI/CD NuGet csomag eszközöket biztosít a Visual Studio projektbuildekhez, helyi teszteléshez egy tetszőleges gépen. Az Azure Stream Analytics CI/CD npm csomag eszközöket biztosít a Visual Studio Code projektbuildekhez (amely egy Azure Resource Manager-sablont hoz létre) egy tetszőleges gépen.|

## <a name="next-steps"></a>További lépések

* [A Test Stream Analytics helyi lekérdezéseket lekérdezése imént a Visual Studio-kód használatával](visual-studio-code-local-run.md)
* [A Test Stream Analytics helyi legfelhasználhatósági lekérdezéseket használ a Visual Studio-kód használatával](visual-studio-code-local-run-live-input.md)
* [A Test Stream Analytics lekérdezései helyileg a Visual Studióval](stream-analytics-vs-tools-local-run.md)
* [Élő adatok helyi tesztelése a Visual Studio Azure Stream Analytics eszközeivel](stream-analytics-live-data-local-testing.md)

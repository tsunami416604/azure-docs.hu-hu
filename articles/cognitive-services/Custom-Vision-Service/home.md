---
title: Mi a Custom Vision?
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy hozhat létre egyéni képosztályozókat az Azure-felhőben a Custom Vision Service használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 9e455a42945ba4d6ce334d557b7390c3c50e7ff9
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177824"
---
# <a name="what-is-custom-vision"></a>Mi a Custom Vision?

A Custom Vision egy olyan kognitív szolgáltatás, amely lehetővé teszi saját rendszerkép-besorolások összeállítását, üzembe helyezését és fejlesztését. A képosztályozó egy olyan AI-szolgáltatás, amely a vizualizáció tulajdonságai alapján a képekre vonatkozó címkéket ( _osztályokat_jelképező) alkalmaz. A [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) szolgáltatástól eltérően Custom Vision lehetővé teszi az alkalmazandó címkék meghatározását.

## <a name="what-it-does"></a>Funkció

A Custom Vision szolgáltatás gépi tanulási algoritmust használ a címkék képekre való alkalmazásához. Ön, a fejlesztőnek be kell küldenie a szolgáltatásban szereplő rendszerképek csoportjait, és nem kell megadnia a szóban forgó jellemzőket. A képeket saját maga címkézheti a beküldéskor. Ezt követően az algoritmus ezeket az adatokhoz rendeli, és kiszámítja a saját pontosságát az azonos lemezképeken végzett teszteléssel. Az algoritmus betanítása után tesztelheti, Újrataníthatja és végül felhasználhatja az új rendszerképek besorolását az alkalmazás igényeinek megfelelően. A modellt exportálhatja offline használatra is.

### <a name="classification-and-object-detection"></a>Osztályozás és objektumészlelés

A Custom Vision funkciói két szolgáltatásra oszlanak. A **képbesorolás** egy vagy több címkét alkalmaz egy képre. Az **objektum-észlelés** hasonló, de visszaadja a koordinátákat abban a képen, amelyben az alkalmazott felirat (ok) található.

### <a name="optimization"></a>Optimalizálás

A Custom Vision szolgáltatás úgy van optimalizálva, hogy gyorsan felismerje a képek közötti jelentős különbségeket, így a modell prototípusát kis mennyiségű adattal is elindíthatja. a címkével ellátott 50-lemezképek általában jól indulnak. A szolgáltatás azonban nem optimális megoldás a képek enyhe különbségének észlelésére (például kisebb repedések vagy horpadások észlelése minőségbiztosítási forgatókönyvekben).

Emellett számos olyan Custom Vision-algoritmus közül választhat, amelyek bizonyos tárgyi anyagokhoz, például a no__t-0for, a tereptárgyak vagy a kereskedelmi elemekhez optimalizált rendszerképekre vannak optimalizálva. További információt az [osztályozó](getting-started-build-a-classifier.md) útmutatójának összeállítása című témakörben talál.

## <a name="what-it-includes"></a>Mit tartalmaz?

A Custom Vision Service natív SDK-k készleteként is elérhető egy webalapú felületen a [Custom Vision kezdőlapján](https://customvision.ai/). A modelleket akár interfészen keresztül is létrehozhatja, tesztelheti és betaníthatja, vagy mindkettőt együtt is használhatja.

![Custom Vision-kezdőlap egy Chrome-böngészőablakban](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes Cognitive Services esetében, a Custom Vision szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft adatkezelési szabályzatait. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>Következő lépések

Kövesse az osztályozási [útmutató létrehozása](getting-started-build-a-classifier.md) című témakört, amellyel megkezdheti Custom Vision használatát a weben, vagy elvégezheti a [képbesorolási oktatóanyagot](csharp-tutorial.md) , amely alapszintű forgatókönyvet valósít meg a kódban.

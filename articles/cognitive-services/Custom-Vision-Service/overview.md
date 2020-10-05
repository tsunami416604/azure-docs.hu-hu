---
title: Mi a Custom Vision?
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan hozhat létre egyéni AI-rendszerképeket és-észleléseket az Azure-felhőben az Azure Custom Vision szolgáltatással.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 09/14/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: képek felismerése, rendszerkép-azonosító, képfelismerési alkalmazás, egyéni jövőkép
ms.openlocfilehash: 47227a60b1ed45499afdb42fbc062abc5ae51ff9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90605537"
---
# <a name="what-is-custom-vision"></a>Mi a Custom Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Custom Vision egy képfelismerési szolgáltatás, amely lehetővé teszi saját rendszerkép-azonosítók elkészítését, üzembe helyezését és fejlesztését. A képazonosítók a képekre mutató címkéket (amelyek osztályokat vagy objektumokat jelölnek) a vizualizáció tulajdonságaik szerint. A [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) szolgáltatástól eltérően a Custom Vision lehetővé teszi a címkék megadását és az egyéni modellek betanítását az észleléshez.

## <a name="what-it-does"></a>Művelet

A Custom Vision szolgáltatás gépi tanulási algoritmust használ a rendszerképek elemzéséhez. Ön, a fejlesztő, beküldi a szolgáltatásban szereplő rendszerképek csoportjait, és nem adja meg a szóban forgó jellemzőket. A képeket saját maga címkézheti a beküldéskor. Ezt követően az algoritmus ezeket az adatokhoz rendeli, és kiszámítja a saját pontosságát az azonos lemezképeken végzett teszteléssel. Az algoritmus betanítása után tesztelheti, áttaníthatja és végül felhasználhatja azt a képfelismerési alkalmazásban az új lemezképek besorolásához. A modellt exportálhatja offline használatra is.

### <a name="classification-and-object-detection"></a>Osztályozás és objektumészlelés

A Custom Vision funkciói két szolgáltatásra oszlanak. A **képbesorolás** egy vagy több címkét alkalmaz egy képre. Az **objektum-észlelés** hasonló, de visszaadja a koordinátákat abban a képen, amelyben az alkalmazott felirat (ok) található.

### <a name="optimization"></a>Optimalizálás

A Custom Vision szolgáltatás úgy van optimalizálva, hogy gyorsan felismerje a képek közötti jelentős különbségeket, így a modell prototípusát kis mennyiségű adattal is elindíthatja. a címkével ellátott 50-lemezképek általában jól indulnak. A szolgáltatás azonban nem optimális megoldás a képek enyhe különbségének észlelésére (például kisebb repedések vagy horpadások észlelése minőségbiztosítási forgatókönyvekben).

Emellett számos olyan Custom Vision-algoritmus közül választhat, amelyek bizonyos tulajdonosi anyagokkal &mdash; (például tereptárgyak vagy kereskedelmi elemek) való rendszerképekre vannak optimalizálva. További információ: [osztályozó](getting-started-build-a-classifier.md) létrehozása vagy [Object detektor](get-started-build-detector.md) -útmutatók készítése.

## <a name="what-it-includes"></a>Mit tartalmaz?

A Custom Vision Service natív SDK-k, valamint a [Custom Vision webhely](https://customvision.ai/)webalapú felületén keresztül érhető el. A modelleket akár interfészen keresztül is létrehozhatja, tesztelheti és betaníthatja, vagy mindkettőt együtt is használhatja.

![Custom Vision webhely Chrome böngészőablakban](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes Cognitive Services esetében, a Custom Vision szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft adatkezelési szabályzatait. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>További lépések

Kövesse az [osztályozó](getting-started-build-a-classifier.md) létrehozásával kapcsolatos útmutatót, amellyel megkezdheti az Custom Vision használatát a webes portálon, vagy elvégezheti az [ügyféloldali kódtár](quickstarts/image-classification.md) gyors üzembe helyezését a kódban található alapszintű forgatókönyvek megvalósításához.

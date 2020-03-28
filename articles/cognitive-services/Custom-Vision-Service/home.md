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
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: a48c553f1c96b8777e0a591f428dca3f15d7d30e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80053643"
---
# <a name="what-is-custom-vision"></a>Mi a Custom Vision?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A Custom Vision egy Cognitive Services-szolgáltatás, amellyel egyéni képosztályozókat hozhat létre, helyezhet üzembe és fejleszthet tovább. A képosztályozó olyan AI-szolgáltatás, amely a képekre címkéket _(amelyek osztályokat_jelölnek) a vizuális jellemzőiknek megfelelően. A [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) szolgáltatással ellentétben a Custom Vision lehetővé teszi az alkalmazandó címkék meghatározását.

## <a name="what-it-does"></a>Művelet

A Custom Vision szolgáltatás egy gépi tanulási algoritmust használ a címkék képek re alkalmazásához. Önnek, a fejlesztőnek olyan képcsoportokat kell benyújtania, amelyek a szóban forgó jellemzőkkel rendelkeznek, és nem rendelkeznek azokkal. A képeket saját maga címkézi fel a benyújtás időpontjában. Ezután az algoritmus betanítása ezekre az adatokra, és kiszámítja a saját pontosságát tesztelésével magát ugyanazon képeken. Az algoritmus betanítása után tesztelheti, újraképezheti és végül használhatja az új képek besorolására az alkalmazás igényeinek megfelelően. A modellt exportálhatja offline használatra is.

### <a name="classification-and-object-detection"></a>Osztályozás és objektumészlelés

A Custom Vision funkciói két szolgáltatásra oszlanak. **A képbesorolás** egy vagy több címkét alkalmaz egy képre. **Az objektumészlelés** hasonló, de a képen lévő koordinátákat is visszaadja, ahol az alkalmazott címke(ok) találhatók.

### <a name="optimization"></a>Optimalizálás

A Custom Vision szolgáltatás úgy van optimalizálva, hogy gyorsan felismerje a képek közötti jelentős különbségeket, így kis mennyiségű adattal elkezdheti a modell prototípusát. 50 kép címkénként általában jó kezdet. A szolgáltatás azonban nem optimális a képek finom különbségeinek észlelésére (például kisebb repedések vagy horpadások észlelésére a minőségbiztosítási forgatókönyvekben).

Ezenkívül a Custom Vision algoritmus több fajtája közül is választhat, amelyek&mdash;bizonyos tárgyanyagokkal rendelkező képekre vannak optimalizálva, például tájékozódási pontokvagy kiskereskedelmi cikkek. További információt az [Osztályozó készítése](getting-started-build-a-classifier.md) útmutatóban talál.

## <a name="what-it-includes"></a>Mit tartalmaz?

A Custom Vision Service natív SDK-k készleteként is elérhető egy webalapú felületen a [Custom Vision kezdőlapján](https://customvision.ai/). Létrehozhat, tesztelhet és betaníthat egy modellt a felületen keresztül, vagy együtt használhatja mindkettőt.

![Custom Vision-kezdőlap egy Chrome-böngészőablakban](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

A Cognitive Services hez csakúgy, mint az összes Cognitive Services, a Custom Vision szolgáltatást használó fejlesztőknek is tisztában kell lenniük a Microsoft ügyféladatokra vonatkozó szabályzataival. További információért tekintse meg a [Cognitive Services lapot](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a Microsoft Adatvédelmi központban.

## <a name="next-steps"></a>További lépések

Kövesse az [osztályozó útmutató létrehozása](getting-started-build-a-classifier.md) a Custom Vision használatának megkezdéséhez az interneten, vagy töltsön ki egy [képbesorolási oktatóanyagot](csharp-tutorial.md) egy alapvető forgatókönyv kódban történő megvalósításához.

---
title: Mi az az Azure Custom Vision?
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogy hozhat létre egyéni képosztályozókat az Azure-felhőben a Custom Vision Service használatával.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 10/26/2018
ms.author: anroth
ms.openlocfilehash: 5e8e675b32bfd4c741b82b1ab341a80adbb0529d
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741640"
---
# <a name="what-is-azure-custom-vision"></a>Mi az az Azure Custom Vision?

Az Azure Custom Vision API egy Cognitive Services-szolgáltatás, amellyel egyéni képosztályozóakt hozhat létre, helyezhet üzembe és javíthat. A képosztályozó egy AI-szolgáltatás, amely a képeket osztályokba rendezi (címkézi) bizonyos tulajdonságoknak megfelelően. A [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) szolgáltatástól eltérően a Custom Visionnel saját osztályozást is létrehozhat.

## <a name="what-it-does"></a>Művelet

A Custom Vision Service a képek osztályozásához egy gépi tanulási algoritmust használ. A fejlesztőnek be kell küldenie képek csoportjait, amelyek a szóban forgó osztályozás(oka)t tartalmazzák, illetve nem tartalmazzák. A képek megfelelő címkéit a küldéskor határozhatja meg. Ezután az algoritmus betanulja az adatokat, majd kiszámolja a saját pontosságát úgy, hogy teszteli magát ugyanezeken az adatokon. A modell betanítása után tesztelheti, újrataníthatja, és végül felhasználhatja azt új képek osztályozásához az alkalmazása igényeinek megfelelően. A modellt exportálhatja offline használatra is.

### <a name="classification-and-object-detection"></a>Osztályozás és objektumészlelés

A Custom Vision funkciói két szolgáltatásra oszlanak. A **Képosztályozás** az egyes képekhez egy osztályozáseloszlást rendel hozzá. Az **Objektumészlelés** hasonló, azonban visszaadja a kép által tartalmazott koordinátákat is, ahol az alkalmazott címkék találhatók.

### <a name="optimization"></a>Optimalizálás

A Custom Vision Service által használt módszerek általában a jelentős különbségeket érzékelik, így már kis mennyiségű adattal is neki lehet állni a prototípus készítésének. Címkénként 50 kép általában jó kiindulópont. Azonban ez azt jelenti, hogy a szolgáltatás nem optimális a képek közötti apró különbségek észlelésére (például kisebb repedések és horpadások észlelésére minőségbiztosítási célból).

Emellett a Custom Vision-algoritmus számos változata közül választhat, amelyek optimalizálva vannak bizonyos témakörökre – például nevezetességekre vagy kereskedelmi cikkekre. Az ezekkel kapcsolatos további információkért tekintse meg a [tartalomosztályozó létrehozását](getting-started-build-a-classifier.md) ismertető útmutatót.

## <a name="what-it-includes"></a>Mit tartalmaz?
A Custom Vision Service natív SDK-k készleteként is elérhető egy webalapú felületen a [Custom Vision kezdőlapján](https://customvision.ai/). Az egyik vagy mindkét felületen létrehozhat, tesztelhet és betaníthat egy modellt.

![Custom Vision-kezdőlap egy Chrome-böngészőablakban](media/browser-home.png)

## <a name="next-steps"></a>További lépések

Kövesse a [tartalomosztályozó létrehozását](getting-started-build-a-classifier.md) ismertető útmutatót, hogy megismerje a Custom Vision webes használatának első lépéseit, vagy végezzen el egy [képosztályozási oktatóanyagot](csharp-tutorial.md) a forgatókönyv kódban történő megvalósításához.

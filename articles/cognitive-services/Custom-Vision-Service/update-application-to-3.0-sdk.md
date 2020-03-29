---
title: A projekt frissítése a 3.0-s API-ra
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan frissítheti a Custom Vision-projekteket az API előző verziójából a 3.0-s API-ra.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647505"
---
# <a name="update-to-the-30-api"></a>Frissítés a 3.0 API-hoz

A Custom Vision elérte az általános elérhetőséget, és api-frissítésen ment keresztül.
Ez a frissítés néhány új funkciót tartalmaz, és fontos, hogy néhány apró változtatást:

* Az előrejelzési API most két részre oszlik a projekt típusa alapján.
* A Vision AI Developer Kit (VAIDK) exportálási beállításhoz a projekt et egy adott módon kell létrehozni.
* Az alapértelmezett ismétléseket eltávolították egy elnevezett iteráció közzététele/visszavonása miatt.

Ez az útmutató bemutatja, hogyan frissítheti a projekteket az új API-verzióval való együttműködésre. A módosítások teljes listáját a [Kibocsátási megjegyzések ben](release-notes.md) láthatja.

## <a name="use-the-updated-prediction-api"></a>A frissített előrejelzési API használata

A 2.x API-k ugyanazt az előrejelzési felhívást használták mind a képosztályozók, mind az objektumdetektor-projektek esetében. Mindkét projekttípus elfogadható volt a **PredictImage** és **predictimageurl** hívások hoz. A 3.0-s szinttől kezdve felosztottuk ezt az API-t, így a projekttípusát a híváshoz kell egyeztetnie:

* **[A ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** és **[a ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** használatával előrejelzéseket kaphat a képbesorolási projektekhez.
* Használja **[detectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** és **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** objektumészlelési projektek előrejelzéseit.

## <a name="use-the-new-iteration-publishing-workflow"></a>Az új iterációs közzétételi munkafolyamat használata

A 2.x API-k az alapértelmezett iterációt vagy egy megadott iterációs azonosítót használták az előrejelzéshez használandó iteráció kiválasztásához. A 3.0-s kiadással kezdődően egy közzétételi folyamatot alkalmazunk, amelynek során először közzétehet egy ismétlést egy megadott néven a betanítási API-ból. Ezután adja át a nevet az előrejelzési módszereknek, hogy megadja, melyik iterációt kell használni.

> [!IMPORTANT]
> A 3.0 API-k nem használják az alapértelmezett iterációs funkciót. Amíg nem adják elavultnak a régebbi API-kat, továbbra is használhatja a 2.x API-kat, hogy alapértelmezettként egy iterációt váltson. Ezek az API-k egy ideig megmaradnak, és meghívhatja az **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** metódust, hogy egy iterációt alapértelmezettként jelöljön meg.

### <a name="publish-an-iteration"></a>Iteráció közzététele

Miután egy iteráció be van tanítva, elérhetővé teheti előrejelzéshez a **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** metódus használatával. Iteráció közzétételéhez szüksége lesz az előrejelzési erőforrás-azonosítóra, amely elérhető a CustomVision webhely beállítások oldalán.

![A Custom Vision webhelybeállítások lap az előrejelzési erőforrás-azonosító körvonalazott.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Ezeket az információkat az [Azure Portalon](https://portal.azure.com) is beszerezheti, ha megnyílik az egyéni jövőkép-előrejelzési erőforrás, és kiválasztja a **Tulajdonságok lehetőséget.**

Az iteráció közzététele után az alkalmazások előrejelzéshez használhatják, ha megadják a nevet az előrejelzési API-hívásukban. Ha azt szeretné, hogy az előrejelzési hívások hoz egy iteráció nem érhető el, használja a **[Visszavonás imitációs API-t.](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)**

## <a name="next-steps"></a>További lépések

* [Betanítási API referenciadokumentáció (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Előrejelzési API-referenciadokumentáció (REST)](https://go.microsoft.com/fwlink/?linkid=865445)
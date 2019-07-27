---
title: A projekt migrálása a 3,0 API-ba
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan telepíthet át Custom Vision projekteket az API előző verziójáról az 3,0 API-ra.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 353fc0a2d8396def17b8e23d9a1c685c755349c5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560897"
---
# <a name="migrate-to-the-30-api"></a>Migrálás a 3,0 API-ra

A Custom Vision mostantól elérte az általános elérhetőséget, és egy API-frissítésen esett át.
Ez a frissítés néhány új funkciót tartalmaz, és fontos, hogy néhány megszakítási változás:

* Az előrejelzési API mostantól két, a projekt típusa alapján oszlik meg.
* A jövőkép AI Developer Kit (VAIDK) exportálási beállításához adott módon kell létrehozni egy projektet.
* Az alapértelmezett iterációk el lettek távolítva a közzététel vagy a Közzététel visszavonása egy elnevezett iteráció mellett.

Ez az útmutató bemutatja, hogyan frissítheti projektjeit az új API-verzióval való együttműködéshez. A módosítások teljes listájáért tekintse meg a [kibocsátási megjegyzéseket](release-notes.md) .

## <a name="use-the-updated-prediction-api"></a>A frissített előrejelzési API használata

A 2. x API-k ugyanazt az előrejelzési hívást használták mind a képosztályozó, mind az objektum-Kiderítő projekt esetében. Mindkét projekttípus elfogadható volt a **PredictImage** és a **PredictImageUrl** -hívások esetében is. Az 3,0-es verziótól kezdődően az API-t felosztjuk, így a projekt típusának meg kell egyeznie a hívással:

* A **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** és a **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** használatával előrejelzéseket kaphat a képbesorolási projektekhez.
* Az **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** és a **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** használatával megtekintheti az objektum-észlelési projektek előrejelzéseit.

## <a name="use-the-new-iteration-publishing-workflow"></a>Az új iterációs közzétételi munkafolyamat használata

A 2. x API-k az alapértelmezett iterációt vagy egy megadott iterációs azonosítót használták az előrejelzéshez használandó iteráció kiválasztásához. A 3,0-től kezdődően egy közzétételi folyamatot hoztunk létre, amelynek során először közzé kell tenni egy iterációt a betanítási API megadott neve alatt. Ezután adja át a nevet az előrejelzési metódusoknak a használni kívánt iteráció megadásához.

> [!IMPORTANT]
> Az 3,0 API-k nem az alapértelmezett iterációs funkciót használják. Amíg a régebbi API-k elavultak, továbbra is használhatja a 2. x API-kat az iterációk alapértelmezettként való váltásához. Ezek az API-k egy adott időszakban megmaradnak, és meghívhatja a **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** metódust az iteráció alapértelmezettként való megjelölésére.

### <a name="publish-an-iteration"></a>Iteráció közzététele

Az iteráció betanítása után elérhetővé teheti az előrejelzést a **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** metódus használatával. Az iteráció közzétételéhez szüksége lesz a CustomVision webhely beállítások lapján elérhető előrejelzési erőforrás-AZONOSÍTÓra.

![A Custom Vision webhely beállításai lap, amely az előrejelzési erőforrás AZONOSÍTÓját ismerteti.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Ezt az információt az [Azure Portalon](https://portal.azure.com) is megtekintheti, ha a Custom Vision előrejelzési erőforrást választja, és kiválasztja a **Tulajdonságok**elemet.

Miután közzétette az iterációt, az alkalmazások az előrejelzési API-hívásban megadhatják a nevet az előrejelzéshez. Ha nem szeretné, hogy egy iteráció előrejelzési hívásokhoz ne legyen elérhető, használja a **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API-t.

## <a name="additional-export-options"></a>További exportálási beállítások

A 3,0 API-kkal két további exportálási célpontot teszünk közzé: ARM Architecture és a jövőkép AI fejlesztői készlet.

* Az ARM használatához csak egy kompakt tartományt kell kiválasztania, majd az exportálási lehetőségként válassza a Docker, majd a ARM lehetőséget.
* A jövőkép AI fejlesztői csomag esetében a projektet az __általános (Compact)__ tartománnyal kell létrehozni, valamint a VAIDK megadását a cél-exportálási platformok argumentumban.

## <a name="next-steps"></a>További lépések

* [A képzés API-referenciájának dokumentációja (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Előrejelzési API-referenciák dokumentációja (REST)](https://go.microsoft.com/fwlink/?linkid=865445)
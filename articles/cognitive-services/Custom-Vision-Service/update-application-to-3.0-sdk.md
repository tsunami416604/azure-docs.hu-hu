---
title: A projekt áttelepítése a 3.0-s API
titlesuffix: Azure Cognitive Services
description: Ismerje meg a Custom Vision projektek áttelepítése az API az előző verzióhoz képest a 3.0-s API-t.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: 9dd473aadd7123cafc27209f5c34322fdbcffb71
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59044006"
---
# <a name="migrate-to-the-30-api"></a>A 3.0-s át API

Egyéni vizuális most már elérte az általános rendelkezésre állás és ment keresztül az API frissítése.
A frissítés néhány új szolgáltatása, és ami fontosabb, néhány kompatibilitástörő változásokat tartalmazza:

* Az előrejelzési API-t most már van felosztva, amelyek két projekt típusa alapján.
* A Vision AI Developer Kit (VAIDK) exportálási beállítás azt igényli,-projekt létrehozása a meghatározott módon.
* Alapértelmezett ismétlések értéke közzétételi el lettek távolítva, vagy egy nevesített iteráció közzétételének visszavonása.

Ez az útmutató bemutatja, hogyan működik az új API-verzió a projektek frissítését. Tekintse meg a [kibocsátási megjegyzések](release-notes.md) a változások teljes listáját.

## <a name="use-the-updated-prediction-api"></a>A frissített előrejelzési API-val

A 2.x-es API-kat használja az adott előrejelzési hívásban kép besorolások és a objektum detector használatával projektek. Mindkét projekt típusa is számára elfogadható az **PredictImage** és **PredictImageUrl** hívások. 3.0 kezdve rendelkezik osztottuk fel, az API-t, hogy a projekt típusa, a hívásnak egyeznie kell:

* Használat **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** és **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** való kaphatnak előjelzéseket kép besorolási projektekhez.
* Használat **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** és **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** objektum észlelési projektek adatokat beolvasni.

## <a name="use-the-new-iteration-publishing-workflow"></a>Az új közzétételi iteráció-munkafolyamat

A 2.x-es API-k az alapértelmezett iterációhoz vagy a megadott iteráció ID segítségével válassza ki a használandó előrejelzési verzió továbbfejlesztésében. A 3.0-től kezdődően a Microsoft elfogadta egy közzétételi folyamatot, amelynek során először tegye közzé egy iterációját a képzési API megadott név alatt. Akkor továbbítja a nevét adja meg, mely az iteráció használandó előrejelzési módszereket.

> [!IMPORTANT]
> A 3.0-s API-k ne használja az alapértelmezett iteráció funkciót. Amíg nem tudjuk kivezetjük a régebbi API-k, továbbra is ki-és bekapcsolásához egy iterációját alapértelmezés szerint a 2.x-es API-k használatával. Ezen API-k egy ideig maradnak, és meghívhatja a **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** megjelölni egy iterációját az alapértelmezett mód.

### <a name="publish-an-iteration"></a>Egy iteráció közzététele

Egy iteráció be van tanítva, miután elérhetővé teheti azt előrejelzési használatának a **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** metódust. Az előrejelzési erőforrás-azonosító, a beállítások lapon a CustomVision webhelyen elérhető kell egy iterációját közzétételéhez.

![A Custom Vision webhely beállításai lapon ismertetett előrejelzési erőforrás-azonosító.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Ezeket az adatokat is megtekintheti a [az Azure Portal](https://portal.azure.com) fog az egyéni vizuális előrejelzési erőforráshoz, és válassza **tulajdonságok**.

Az ismétlés közzététele után alkalmazások használhatnak, az előrejelzési az előrejelzési API-hívás az a név megadásával. Ahhoz, hogy egy iterációját előrejelzési hívások nem érhető el, használja a **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API-t.

## <a name="additional-export-options"></a>További exportálási lehetőségek

A 3.0-val is közzéteheti, hogy két további API-k exportálása célok: ARM architektúra és vizuális AI szoftverfejlesztői készlet.

* ARM használatához egyszerűen válasszon ki egy kompakt tartományhoz és válassza a docker-fájlban, és az exportálási beállításokat, majd ARM.
* Vizuális AI Dev csomag, a projekt együtt kell létrehozni a __általános (CD)__ tartományban, valamint a VAIDK megadásáról a célként megadott platformok argumentum exportálása.

## <a name="next-steps"></a>További lépések

* [Képzési API referenciadokumentációt tartalmaz (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Az előrejelzési API dokumentációja (REST)](https://go.microsoft.com/fwlink/?linkid=865445)
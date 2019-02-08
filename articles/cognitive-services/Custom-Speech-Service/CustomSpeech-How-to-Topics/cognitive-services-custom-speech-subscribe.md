---
title: Előfizetői azonosítók beszerzése a Custom Speech Service az Azure-ban |} A Microsoft Docs
description: Ismerje meg, a Cognitive Services Custom Speech Service-hívások előfizetési kulcsainak beszerzése.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.subservice: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: nitinme
ms.openlocfilehash: 4be465ed122781edfc48f53f8e0d0c4fb1df2433
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864434"
---
# <a name="obtain-subscription-keys"></a>Előfizetői azonosítók beszerzése

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Első lépésként az Azure Custom Speech Service segítségével, először kapcsolódnia kell a felhasználói fiók Azure-előfizetés. Ingyenes és fizetős csomagokra előfizetések érhetők el. A rétegek kapcsolatos információkért tekintse meg a [díjszabását ismertető lapon](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Előfizetési kulcs lekérése
1. Az Azure Portalon két módon kaphat egy előfizetési kulcsot:

    * Nyissa meg a [az Azure portal](https://ms.portal.azure.com), és keressen rá egy új Cognitive Services API hozzáadása _Cognitive Services_ , és válassza **Cognitive Services API-k**.

      ![Cognitive Services – keresés](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Vagy lépjen közvetlenül a [Cognitive Services API-k](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![Cognitive Services API-k](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
1. Töltse ki a következő kötelező mezőket:

      a. **Fióknév**. Az Önnek legmegfelelőbb nevet használhat. Ne felejtse el ezt a nevet, így az erőforrások listájában találhatja meg a Cognitive Services-előfizetés.

      b. **Előfizetés**. Válasszon ki egy, az Azure-előfizetésekből.

      c. **API-típus**. Válassza ki **Custom Speech Service (előzetes verzió)**.

      d. **Hely**. A szolgáltatás jelenleg **USA nyugati RÉGIÓJA**.

      e. **A tarifacsomag**. Válassza ki a réteg az Önnek legmegfelelőbb. **F0** az ingyenes szinten van. A kvóták, amelyek jogosultak a magyarázatát a [díjszabását ismertető lapon](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Cognitive Services-fiók létrehozása](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

1. Keresse meg az irányítópult nézet vagy szolgáltatás, amely a megadott fióknevet az erőforrások listájában. Ha azt választja, megjelenik a szolgáltatás áttekintése. A bal oldali listában szereplő alatt **erőforrás-kezelés**válassza **kulcsok**. Másolás **kulcs 1**.

      A következő lépésekben az előfizetési kulcs szükséges.

      ![1. KULCS](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Ne másolja át a **előfizetés-azonosító** az Áttekintés lapon. Az előfizetési kulcsot a következő lépésben szüksége lesz.
      >

      ![Előfizetés-azonosító – áttekintés](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

1. Adja meg az előfizetési kulcs, a jobb felső sarokban, a menüszalagon válassza a felhasználói fiók. A legördülő menüben válassza ki a **előfizetések**.

      ![Előfizetések menüpont](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    Előfizetések tábláját ekkor megjelenik, amely üres első megnyitásakor.

    ![Előfizetések-tábla](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

1. Válassza ki **új hozzáadása**. Adjon meg egy nevet az előfizetés és az előfizetési kulcsot. Ez lehet **kulcs 1** (elsődleges kulcs) vagy **kulcs 2** (másodlagos kulcs) az előfizetésből.

      ![Előfizetési kulcs neve](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

Feltölteni az adatokat, a modell betanítását, vagy üzembe helyezés, a Custom Speech Service tevékenységek összekapcsolása egy Azure-előfizetést kell. Ingyenes vagy fizetős csomagra előfizetés lehet. További tájékoztatás a [díjszabási lapon](https://www.microsoft.com/cognitive-services/en-us/pricing) olvasható.

## <a name="get-a-subscription-id"></a>Egy előfizetés-azonosító beszerzése
Egy előfizetés-azonosító az Azure Portalra, keresse fel. Keresse meg *Cognitive Services* és *Custom Speech Service*, és kövesse az utasításokat.

> [!NOTE]
> Ez a folyamat későbbi szakaszában az előfizetési kulcs szükséges.
>

## <a name="next-steps"></a>További lépések
* Létrehozásához indítsa el a [importálni akusztikai modell](cognitive-services-custom-speech-create-acoustic-model.md).
* Létrehozásához indítsa el a [egyéni nyelvi modell](cognitive-services-custom-speech-create-language-model.md).

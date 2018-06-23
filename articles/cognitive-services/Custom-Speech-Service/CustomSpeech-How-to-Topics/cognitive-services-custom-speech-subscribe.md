---
title: Az egyéni beszéd szolgáltatás Azure előfizetés kulcsok beszerzése |} Microsoft Docs
description: Útmutató az egyéni Beszéd szolgáltatáshoz intézett hívások előfizetés kulcsokat kognitív szolgáltatásban.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: fcef86a19a77581ff82b64173e2ac68b26ae708a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347054"
---
# <a name="obtain-subscription-keys"></a>Előfizetői azonosítók beszerzése
Ismerkedés az Azure egyéni beszéd szolgáltatással, először szüksége lesz a felhasználói fiókját az Azure-előfizetéshez. Ingyenes és fizetős rétegek előfizetések érhetők el. A rétegek kapcsolatos információkért tekintse meg a [árképzést ismertető oldalra](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Egy előfizetés kulcs beszerzése
1. Egy előfizetés kulcs lekérheti az Azure portálon két módszer egyikével:

    * Lépjen a [Azure-portálon](https://ms.portal.azure.com), és adja hozzá egy új kognitív szolgáltatások API keresve _kognitív szolgáltatások_ és jelölje be **kognitív szolgáltatások API-k**.

      ![Kognitív Services – keresés](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Vagy közvetlenül a [kognitív szolgáltatások API-k](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![Cognitive Services API-k](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
2. Töltse ki a következő kötelező mezők:

      a. **Fióknév**. Használjon, amely megfelelő nevet. Ne felejtse el ezt a nevet, így kognitív szolgáltatások előfizetését az erőforrások listájában található.

      b. **Előfizetés**. Válasszon egyet az Azure-előfizetését.

      c. **API-típus**. Válassza ki **egyéni beszéd szolgáltatás (előzetes verzió)**.

      d. **Hely**. Jelenleg **USA nyugati régiója**.

      e. **A tarifacsomag**. Válassza ki a réteget, amely megfelelő. **F0** ingyenes szint van. A kvótákat, amelyek számára engedélyezett a magyarázatát a [árképzést ismertető oldalra](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Kognitív Services-fiók létrehozása](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

3. Keresse meg az irányítópult nézetet, vagy a megadott fióknak nevű szolgáltatás az erőforrások listájában. Ha ezt választja ki, a szolgáltatás áttekintése látható. A bal oldali listában alatt **erőforrás-kezelés**, jelölje be **kulcsok**. Másolás **kulcs 1**.

      Az Előfizetés kulcs a következő lépéseket kell megadni.

      ![1. KULCS](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Ne másolja át a **előfizetés-azonosító** a áttekintése oldalon. Az Előfizetés kulcs a következő lépésben van szüksége.
      >

      ![Előfizetés-azonosító – áttekintés](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

4. Az Előfizetés kulcs felső sarokban, a menüszalagon válassza ki a felhasználói fiókot. Válassza a legördülő menü **előfizetések**.

      ![Előfizetések menüpont](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    Előfizetések táblázatát úgy jelenik meg, amely üres első megnyitásakor.

    ![Előfizetések tábla](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

5. Válassza ki **új hozzáadása**. Adja meg az előfizetés és az Előfizetés kulcs nevét. Ez lehet akár **kulcs 1** (elsődleges kulcs) vagy **kulcs 2** (másodlagos kulcs) az előfizetésből.

      ![Előfizetés kulcs neve](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

Feltölteni az adatokat, a modell betanításához vagy telepítésére, össze kell kapcsolni az egyéni beszéd tevékenységeket Azure-előfizetéshez. Ingyenes szint vagy a réteg fizetett előfizetés lehet. További tájékoztatás a [díjszabási lapon](https://www.microsoft.com/cognitive-services/en-us/pricing) olvasható.

## <a name="get-a-subscription-id"></a>Előfizetés-azonosító lekérése
Előfizetés-azonosító, keresse fel az Azure-portálon. Keresse meg *kognitív szolgáltatások* és *egyéni beszéd szolgáltatás*, és kövesse az utasításokat.

> [!NOTE]
> Az előfizetés kulcsra van szükség, a folyamat későbbi részében.
>

## <a name="next-steps"></a>További lépések
* Létrehozásához indítsa el a [egyéni akusztikus modell](cognitive-services-custom-speech-create-acoustic-model.md).
* Létrehozásához indítsa el a [egyéni nyelvi modell](cognitive-services-custom-speech-create-language-model.md).

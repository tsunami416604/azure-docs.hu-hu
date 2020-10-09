---
title: LUIS-erőforrás létrehozása
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80879204"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>LUIS-erőforrások létrehozása a Azure Portalban

1. [Ezzel a hivatkozással](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) megkezdheti a Luis-erőforrások létrehozását a Azure Portalban.

1. Adja meg az összes szükséges beállítást:

    |Name (Név)|Cél|
    |--|--|
    |Előfizetés neve| az erőforrásért fizetendő előfizetés.|
    |Erőforráscsoport| Egy kiválasztott vagy létrehozott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez.|
    |Name (Név)| Egyéni név, amelyet az egyéni altartományként használ a szerzői műveletek és előrejelzési végpontok lekérdezéséhez.|
    |Szerzői hely|A modellhez társított régió.|
    |A szerzői díjak szintjei|Az árképzési szint meghatározza a másodpercenkénti maximális tranzakciót és a havi értéket.|
    |Futtatókörnyezet helye|A közzétett előrejelzési végpont futtatókörnyezetéhez társított régió.|
    |Futtatókörnyezet árképzési szintje|Az árképzési szint meghatározza a másodpercenkénti maximális tranzakciót és a havi értéket.|

    > [!div class="mx-imgBorder"]
    > [![A Language Understanding-erőforrás létrehozása](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Kattintson a **felülvizsgálat + létrehozás** gombra, és várja meg az erőforrás létrehozását.
1. Ha mindkét erőforrást létrehozta, még mindig a Azure Portalban, válassza ki az új szerzői erőforrást **, majd a** rövid útmutatókat a szerzői **VÉGPONT URL-címének** és **kulcsának** beszerzéséhez programozott módon.

> [!TIP]
> Az erőforrások használatához a LUIS portálon [rendelje hozzá az erőforrásokat](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).
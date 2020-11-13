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
ms.openlocfilehash: a077d255648ff07cc88b43dece889a221c46c11f
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561510"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>LUIS-erőforrások létrehozása a Azure Portalban

1. [Ezzel a hivatkozással](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) megkezdheti a Luis-erőforrások létrehozását a Azure Portalban.

1. Adja meg az összes szükséges beállítást:

    |Név|Rendeltetés|
    |--|--|
    |Előfizetés | Az erőforrásért fizetendő előfizetés.|
    |Erőforráscsoport| Az Ön által kiválasztott vagy létrehozott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáféréshez és a felügyelethez.|
    |Név| A kiválasztott egyéni név. A szerzői műveletek és előrejelzési végpontok lekérdezéséhez a rendszer egyéni altartományként használja.|
    |Szerzői hely|A modellhez társított régió.|
    |A szerzői díjak szintjei|Meghatározza a másodpercenkénti és havi maximális tranzakciókat.|
    |Előrejelzés helye|A közzétett előrejelzési végpont futtatókörnyezetéhez társított régió.|
    |Előrejelzések díjszabási szintje|Meghatározza a másodpercenkénti és havi maximális tranzakciókat.|

    > [!div class="mx-imgBorder"]
    > [![Képernyőkép, amely az alapvető beállítások lapot mutatja a létrehozás alatt.](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, és várjon, amíg a rendszer létrehozza az erőforrást.
1. Miután mindkét erőforrás létrejött, még mindig a Azure Portalban, válassza ki az új szerzői erőforrást. Ezután válassza **a rövid útmutatók lehetőséget a** szerzői **VÉGPONT URL-címének** és **kulcsának** beszerzéséhez programozott módon.

> [!TIP]
> Az erőforrások használatához a LUIS portálon [rendelje hozzá az erőforrásokat](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).

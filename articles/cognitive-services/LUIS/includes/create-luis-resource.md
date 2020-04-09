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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879204"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>LUIS-erőforrások létrehozása az Azure Portalon

1. [Ezen a hivatkozáson](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) keresztül kezdheti el a LUIS-erőforrások létrehozását az Azure Portalon.

1. Adja meg az összes szükséges beállítást:

    |Név|Cél|
    |--|--|
    |Előfizetés neve| az erőforrásért kiszámlázott előfizetés.|
    |Erőforráscsoport| A kiválasztott vagy létrehozott egyéni erőforráscsoport-név. Az erőforráscsoportok lehetővé teszik az Azure-erőforrások csoportosítását a hozzáférés hez és a kezeléshez.|
    |Név| Egyéni név, amelyet a szerzői és előrejelzési végpontlekérdezések egyéni altartományaként használ.|
    |Szerzői hely|A modellhez társított régió.|
    |Szerzői tarifacsomag|A tarifacsomag határozza meg a maximális tranzakció másodpercenként és havonta.|
    |Futásidejű hely|A közzétett előrejelzési végpont futásidejéhez társított régió.|
    |Futásidejű tarifacsomag|A tarifacsomag határozza meg a maximális tranzakció másodpercenként és havonta.|

    > [!div class="mx-imgBorder"]
    > [![A nyelvtudáserőforrás létrehozása](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Kattintson **a Véleményezés + létrehozás gombra,** és várja meg az erőforrás létrehozását.
1. Miután mindkét erőforrás létrehozása, továbbra is az Azure Portalon, válassza ki az új szerzői erőforrást, majd **a rövid útmutatók** a szerzői **végpont URL-címének** és **kulcs** a programozási programozási.

> [!TIP]
> Az erőforrások használatához a LUIS portálon [rendelje hozzá az erőforrásokat.](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps)
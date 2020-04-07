---
title: LUIS-erőforrás létrehozása
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2020
ms.author: dapine
ms.openlocfilehash: 88289686e5b091ef3ec309ee9b54ee0f895c8c22
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754371"
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
---
title: Azok a régiók, amelyekben Video Indexer elérhető – Azure
titleSuffix: Azure Media Services
description: Ez a cikk a Azure Media Services Video Indexer elérhető Azure-régiókkal kapcsolatos információkat tárgyalja.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 45da0556d074fdb81002f73cd89d0c35cb37276e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530049"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Azure-régiók, amelyekben Video Indexer létezik

Video Indexer API-k olyan **Location** paramétert tartalmaznak, amelyet arra az Azure-régióra kell beállítani, amelyre a hívást át kell irányítani. Ennek egy olyan [Azure-régiónak kell lennie, amelyben video Indexer érhető el](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Helyek

A `location` paraméternek meg kell adni az Azure-régió kódjának nevét az értékeként. Ha az előnézeti módban Video Indexert használ, a értéket kell megtennie `"trial"` értékként. `trial`a paraméter alapértelmezett értéke `location` . Ellenkező esetben a fiókja által használt Azure-régió kódjának beszerzéséhez és a hívás átirányításához a következő sort futtathatja az [Azure CLI](/cli/azure)-ben:

```azurecli-interactive
az account list-locations
```

Miután futtatta a fenti sort, megjelenik az összes Azure-régió listája. Navigáljon ahhoz az Azure-régióhoz, amelynek a *DisplayName* a nevét keresi, és használja a *Name* értékét a **Location** paraméterhez.

Az USA 2. nyugati régiójában (az alábbi ábrán látható) például az "westus2" kifejezést fogja használni a **Location** paraméterhez.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Következő lépések

- [Nyelvi modell testreszabása API-k használatával](customize-language-model-with-api.md)
- [A Brands modell testreszabása API-k használatával](customize-brands-model-with-api.md)
- [Személy modell testreszabása API-k használatával](customize-person-model-with-api.md)

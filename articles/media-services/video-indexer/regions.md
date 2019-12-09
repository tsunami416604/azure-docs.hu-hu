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
ms.author: anzaman
ms.openlocfilehash: 6ba6f189f4290bb2751adf9b44135eeda7266ca0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892753"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Azure-régiók, amelyekben Video Indexer létezik

Video Indexer API-k olyan **Location** paramétert tartalmaznak, amelyet arra az Azure-régióra kell beállítani, amelyre a hívást át kell irányítani. Ennek egy olyan [Azure-régiónak kell lennie, amelyben video Indexer érhető el](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Földrajzi egységek

A **Location** paraméter értékeként az Azure-régiókód nevét kell megadni. Ha az előnézeti módban Video Indexert használ, a *"próbaverzió"* értéket kell megtennie értékként. Ellenkező esetben a fiókja által használt Azure-régió kódjának beszerzéséhez és a hívás átirányításához a következő sort futtathatja az [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)-ben:

```bash
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

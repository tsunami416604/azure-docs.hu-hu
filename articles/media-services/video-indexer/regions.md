---
title: Olyan régióban, amelyben a Video Indexer érhető el – Azure
titlesuffix: Azure Media Services
description: Azure-régióban, amelyben a Video Indexer érhető el ez a cikk ismerteti.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: 8c1d51c2bb6062f1360c831549e523a5821ff14b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004468"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Azure-régióban, amelyben a Video Indexer létezik

Video Indexer API-kat tartalmazhat egy **hely** paraméter, amely az Azure-régió, amelyhez a hívást kell átirányítani kell beállítania. Ez lehet egy [Azure-régió, amelyben a Video Indexer elérhető](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Helyek

A **hely** paramétert meg kell adni az Azure-régió neve alelem értékként. A Video Indexer használatakor az előnézeti módban helyezze *"próbaverzió"* értékeként. Ellenkező esetben az Azure-régióhoz, hogy a fiókja van, és a hívást kell átirányítani a kód nevének futtassa a következő sort [Azure CLI-vel](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```bash
az account list-locations
```

Egyszer futtatja a sor a fent látható az összes Azure-régiók listájának beolvasása. Keresse meg az Azure-régióban, amely rendelkezik a *displayName* keres, és használja a *neve* értékét a **hely** paraméter.

Ha például az Azure-régiót (alább látható) 2. nyugati RÉGIÓJA, az használandó "westus2" a **hely** paraméter.

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

## <a name="next-steps"></a>További lépések

- [Testre szabhatja a nyelvi modell API-k használatával](customize-language-model-with-api.md)
- [API-k használatával márkái modell testreszabása](customize-brands-model-with-api.md)
- [API-k használatával személy modell testreszabása](customize-person-model-with-api.md)

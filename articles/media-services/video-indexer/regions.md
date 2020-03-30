---
title: Azok a régiók, amelyekben a Video Indexelő elérhető - Azure
titleSuffix: Azure Media Services
description: Ez a cikk az Azure-régiók, ahol az Azure Media Services videoindexelő érhető el.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: c91b38fcbfb9b517651adead010408425e519a82
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382749"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Az Azure-régiók, amelyekben a Video Indexelő létezik

A Video Indexer **location** API-k tartalmaznak egy helyparamétert, amelyet be kell állítania az Azure-régióban, amelyre a hívást kell irányítani. Ennek olyan [Azure-régiónak kell lennie, amelyben a Video Indexelő elérhető.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)

## <a name="locations"></a>Helyek

A **hely** paraméter t kell adni az Azure régió kódnevét, mint az érték. Ha a Video Indexer-t előnézeti módban használja, a *"próbaverziót"* kell értékként használnia. Ellenkező esetben annak az Azure-régiónak a kódnevének lekérnie, amelyben a fiók található, és amelyhez a hívást át kell irányítani, futtathatja a következő sort az [Azure CLI-ben:](/cli/azure)

```azurecli-interactive
az account list-locations
```

Miután futtatta a fenti sort, megjelenik az összes Azure-régió listája. Keresse meg az Azure-régióban, amely rendelkezik a *displayName,* amely a keresett, és használja a *név* értékét a **hely** paraméter.

Például az Azure régió ban USA 2 nyugati régiója (alább látható), a **helyparaméterhez** a "westus2" lesz.

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

- [Nyelvi modell testreszabása API-k használatával](customize-language-model-with-api.md)
- [Márkák modell testreszabása API-k használatával](customize-brands-model-with-api.md)
- [Személymodell testreszabása API-k használatával](customize-person-model-with-api.md)

---
title: A szükséges paraméterek összegyűjtése
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Az összes Cognitive Services-tároló paraméterei
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 06f68d28d06dec7c9e738ad8cb50ca337fa840be
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71102014"
---
## <a name="gathering-required-parameters"></a>A szükséges paraméterek összegyűjtése

Három elsődleges paraméter van a szükséges összes Cognitive Services tárolóhoz. A végfelhasználói licencszerződés (EULA) értékének `accept`a következőnek kell lennie:. Emellett a végponti URL-cím és az API-kulcs is szükséges.

> [!NOTE]
> Erre a három kötelező paraméterre csak akkor van kivétel, ha a tárolók "offline" tárolóknak számítanak. Az offline tárolók nem jelentik a használatot, nem mérik és nem követik a különböző számlázási módszereket.

### <a name="endpoint-uri-endpoint_uri"></a>Végpont URI-ja`{ENDPOINT_URI}`

A **végpont** URI-értéke a megfelelő kognitív szolgáltatás erőforrásának Azure Portal *Áttekintés* lapján érhető el. Navigáljon az *Áttekintés* lapra, vigye a kurzort a végpont fölé `Copy to clipboard` , és egy <span class="docon docon-edit-copy x-hidden-focus"></span> ikon jelenik meg. Szükség esetén másolja és használja.

![A végpont URI-ja összegyűjtése későbbi használatra](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Kulcsok`{API_KEY}`

Ez a kulcs a tároló elindítására szolgál, és a megfelelő kognitív szolgáltatási erőforrás Azure Portal kulcsok lapján érhető el. Navigáljon a *kulcsok* oldalra, és kattintson az `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ikonra.

![A két kulcs egyikének beolvasása későbbi használatra](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Ezek az előfizetési kulcsok hozzáférnek a kognitív szolgáltatás API-hoz. Ne ossza meg a kulcsokat. Biztonságos tárolás, például Azure Key Vault használata. Javasoljuk továbbá, hogy rendszeresen újragenerálja ezeket a kulcsokat. API-hívások létrehozásához csak egy kulcs szükséges. Az első kulcs újragenerálásakor a második kulcsot használhatja a szolgáltatás folyamatos eléréséhez.
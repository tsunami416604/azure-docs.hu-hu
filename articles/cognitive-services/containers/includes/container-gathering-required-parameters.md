---
title: A szükséges paraméterek összegyűjtése
services: cognitive-services
author: aahill
manager: nitinme
description: Az összes Cognitive Services-tároló paraméterei
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80875041"
---
## <a name="gathering-required-parameters"></a>A szükséges paraméterek összegyűjtése

Három elsődleges paraméter van a szükséges összes Cognitive Services tárolóhoz. A végfelhasználói licencszerződés (EULA) értékének a következőnek kell lennie: `accept`. Emellett a végponti URL-cím és az API-kulcs is szükséges.

### <a name="endpoint-uri-endpoint_uri"></a>Végpont URI-ja`{ENDPOINT_URI}`

A **végpont** URI-értéke a megfelelő kognitív szolgáltatás erőforrásának Azure Portal *Áttekintés* lapján érhető el. Navigáljon az *Áttekintés* lapra, vigye a kurzort a végpont fölé `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> , és egy ikon jelenik meg. Szükség esetén másolja és használja.

![A végpont URI-ja összegyűjtése későbbi használatra](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Kulcsok`{API_KEY}`

Ez a kulcs a tároló elindítására szolgál, és a megfelelő kognitív szolgáltatási erőforrás Azure Portal kulcsok lapján érhető el. Navigáljon a *kulcsok* oldalra, és kattintson az `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ikonra.

![A két kulcs egyikének beolvasása későbbi használatra](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Ezek az előfizetési kulcsok hozzáférnek a kognitív szolgáltatás API-hoz. Ne ossza meg a kulcsokat. Biztonságos tárolás, például Azure Key Vault használata. Javasoljuk továbbá, hogy rendszeresen újragenerálja ezeket a kulcsokat. API-hívások létrehozásához csak egy kulcs szükséges. Az első kulcs újragenerálásakor a második kulcsot használhatja a szolgáltatás folyamatos eléréséhez.
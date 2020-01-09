---
title: A szükséges paraméterek összegyűjtése
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Az összes Cognitive Services-tároló paraméterei
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2019
ms.author: dapine
ms.openlocfilehash: bf4e8f06642a4a764794a75e2b49f15958796c61
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "73465951"
---
## <a name="gathering-required-parameters"></a>A szükséges paraméterek összegyűjtése

Három elsődleges paraméter van a szükséges összes Cognitive Services tárolóhoz. A végfelhasználói licencszerződésnek (EULA) `accept`értékkel kell rendelkeznie. Emellett a végponti URL-cím és az API-kulcs is szükséges.

### <a name="endpoint-uri-endpoint_uri"></a>Végpont URI-ja `{ENDPOINT_URI}`

A **végpont** URI-értéke a megfelelő kognitív szolgáltatás erőforrásának Azure Portal *Áttekintés* lapján érhető el. Navigáljon az *Áttekintés* lapra, vigye a kurzort a végpont fölé, <span class="docon docon-edit-copy x-hidden-focus"></span> és megjelenik egy `Copy to clipboard` ikon. Szükség esetén másolja és használja.

![A végpont URI-ja összegyűjtése későbbi használatra](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Kulcsok `{API_KEY}`

Ez a kulcs a tároló elindítására szolgál, és a megfelelő kognitív szolgáltatási erőforrás Azure Portal kulcsok lapján érhető el. Navigáljon a *kulcsok* oldalra, és kattintson a `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ikonra.

![A két kulcs egyikének beolvasása későbbi használatra](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Ezek az előfizetési kulcsok hozzáférnek a kognitív szolgáltatás API-hoz. Ne ossza meg a kulcsokat. Biztonságos tárolás, például Azure Key Vault használata. Javasoljuk továbbá, hogy rendszeresen újragenerálja ezeket a kulcsokat. API-hívások létrehozásához csak egy kulcs szükséges. Az első kulcs újragenerálásakor a második kulcsot használhatja a szolgáltatás folyamatos eléréséhez.
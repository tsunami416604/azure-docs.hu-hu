---
title: Azure app Configuration REST API – HMAC-hitelesítés
description: A HMAC használata az Azure-alkalmazások konfigurálásához a REST API használatával
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 8d1f8a17f51711cc5c10567797e1224f96eb7630
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424161"
---
# <a name="hmac-authorization---rest-api-reference"></a>HMAC-hitelesítés – REST API referenciája

A HMAC-hitelesítés használatakor a műveletek a két kategória egyikére, olvasásra vagy írásra esnek. Az olvasási és írási hozzáférési kulcsok engedélyt adnak az összes művelet meghívására. A csak olvasási jogosultsággal rendelkező hozzáférési kulcsok csak olvasási műveletek hívására adnak engedélyt. Azt határozza meg, hogy egy hozzáférési kulcs írásvédett-e, vagy a tulajdonsága meghatározza-e az írási-olvasási állapotot `readOnly` . A csak olvasási jogosultsággal rendelkező írási kérések elvégzésére irányuló kísérlet a kérést jogosulatlanul fogja eredményezni.

## <a name="obtaining-access-keys"></a>Hozzáférési kulcsok beszerzése

A hozzáférési kulcsokat és az azok beszerzéséhez használt API-t [ismertető specifikáció](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json)részletesen szerepel az Azure app Configuration erőforrás-szolgáltatói specifikációban. A hozzáférési kulcsok a "ConfigurationStores_ListKeys" művelettel szerezhetők be.

## <a name="errors"></a>Hibák

```http
HTTP/1.1 403 Forbidden
```

**OK:** A kérelem hitelesítéséhez használt hozzáférési kulcs nem biztosítja a szükséges engedélyeket a kért művelet végrehajtásához.
**Megoldás:** Szerezzen be egy olyan hozzáférési kulcsot, amely engedélyt biztosít a kért művelet végrehajtásához, és használja azt a kérelem hitelesítéséhez.

---
title: Azure-alkalmazás konfigurációs REST API – zárolások
description: A kulcs-érték zárolások Azure app Configuration REST API használatával való használatához kapcsolódó lapok
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4949db646c54d75f60d29d3c631d0f4ee8d7c26e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424308"
---
# <a name="locks"></a>Zárolások

API-Version: 1,0

Ez az API a kulcs-érték erőforrás zárolási/feloldási szemantikai funkcióját biztosítja. A következő műveleteket támogatja:

- Hely zárolása
- Zárolás eltávolítása

Ha van, `label` explicit címke értéknek kell lennie ( **nem** helyettesítő karakter). Az összes művelethez ez egy opcionális paraméter. Ha nincs megadva, az azt jelenti, hogy nincs címke.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Zárolás Key-Value

- **Kötelező:** ``{key}`` , ``{api-version}``  
- Nem *kötelező:*``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Válaszok**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Ha a kulcs-érték nem létezik, a rendszer a következő választ adja vissza:

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>Key-Value zárolásának feloldása

- **Kötelező:** ``{key}`` , ``{api-version}``  
- Nem *kötelező:*``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**Válaszok**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

Ha a kulcs-érték nem létezik, a rendszer a következő választ adja vissza:

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lockunlock"></a>Feltételes zárolás/zárolás feloldása

A verseny feltételeinek megelőzése érdekében használjon `If-Match` vagy `If-None-Match` kérjen fejléceket. Az `etag` argumentum a kulcs ábrázolásának részét képezi. Ha `If-Match` vagy ki `If-None-Match` van hagyva, a művelet feltétel nélkül jelenik meg.

A következő kérelem csak akkor alkalmazza a műveletet, ha az aktuális kulcs-érték ábrázolás megegyezik a megadott értékkel `etag` :

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

A következő kérelem csak akkor alkalmazza a műveletet, ha az aktuális kulcs-érték megjelenítés létezik, de nem felel meg a megadott feltételeknek `etag` :

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

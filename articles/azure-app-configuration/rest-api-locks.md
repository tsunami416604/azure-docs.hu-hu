---
title: Azure-alkalmazás konfigurációs REST API – zárolások
description: A kulcs-érték zárolások az Azure app Configuration REST API használatával történő használatához kapcsolódó lapok
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 7e63b48f2119c48cd43717acee7b13b1701e0032
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2020
ms.locfileid: "95241267"
---
# <a name="locks"></a>Zárolások

Ez az API (1,0-es verzió) a kulcs-érték erőforrás zárolását és zárolásának feloldását teszi lehetővé. A következő műveleteket támogatja:

- Hely zárolása
- Zárolás eltávolítása

Ha van, `label` explicit címke értéknek kell lennie (nem helyettesítő karakter). Minden művelet esetén ez egy opcionális paraméter. Ha nincs megadva, az azt jelenti, hogy nincs címke.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>Kulcs zárolása – érték

- Kötelező: ``{key}`` , ``{api-version}``  
- Választható ``label``

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

## <a name="unlock-key-value"></a>Kulcs-érték feloldása

- Kötelező: ``{key}`` , ``{api-version}``  
- Választható ``label``

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

## <a name="conditional-lock-and-unlock"></a>Feltételes zárolás és feloldás

A verseny feltételeinek megelőzése érdekében használjon `If-Match` vagy `If-None-Match` kérjen fejléceket. Az `etag` argumentum a kulcs ábrázolásának részét képezi. Ha `If-Match` vagy `If-None-Match` nincs megadva, a művelet feltétel nélküli.

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

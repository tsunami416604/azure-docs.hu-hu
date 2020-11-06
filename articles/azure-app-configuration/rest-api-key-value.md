---
title: Azure app Configuration REST API – Key-Value
description: A kulcs-értékekkel való munkavégzésre szolgáló lapok az Azure app Configuration REST API használatával
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 50d97a330507e9361674776acf29d1007ee5bf58
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424141"
---
# <a name="key-values"></a>Key-Values

API-Version: 1,0

A kulcs-érték a egyedi kombinációjával azonosított erőforrás `key`  +  `label` . A(z) `label` nem kötelező. Ha explicit módon szeretne hivatkozni egy olyan kulcs-értékre, amely nélkül a címke "\ 0" értéket használ (az URL-címet a rendszer kódolja ``%00`` ). Tekintse meg az egyes műveletek részleteit.

## <a name="operations"></a>Üzemeltetés

- Lekérés
- Több lista
- Beállítás
- Törlés

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>Syntax

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>Key-Value beolvasása

**Kötelező:** ``{key}`` , ``{api-version}``  
Nem *kötelező:* ``label`` -Ha nincs megadva, a címke nélküli kulcs-értéknek kell lennie

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**Válaszok**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

Ha a kulcs nem létezik, a rendszer a következő választ adja vissza:

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>Beolvasás (feltétel nélkül)

Az ügyfél gyorsítótárazásának javítása érdekében használjon `If-Match` vagy `If-None-Match` kérjen fejléceket. Az `etag` argumentum a kulcs ábrázolásának részét képezi. Lásd: [14,24-es és 14,26-es szakasz](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

A következő kérelem csak akkor kérdezi le a kulcs-értéket, ha az aktuális ábrázolás nem egyezik a megadott értékkel `etag` :

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**Válaszok**

```http
HTTP/1.1 304 NotModified
```

vagy

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>Key-Values listázása

További beállítások **szűrése**

Nem *kötelező:* ``key`` – Ha nincs megadva, az azt jelenti, hogy **az egyik** kulcs.
Nem *kötelező:* ``label`` – Ha nincs megadva, az azt jelenti, hogy bármilyen címkét **tartalmaz** .

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**Válasz**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

## <a name="pagination"></a>Oldalra tördelés

Ha a visszaadott elemek száma meghaladja a válasz korlátot, a rendszer oldalszámozást jelenít meg. Kövesse a választható `Link` válaszok fejléceit, és használja `rel="next"` a navigáláshoz.
Azt is megteheti, hogy a tartalom egy következő hivatkozást tartalmaz a `@nextLink` tulajdonság formájában. A csatolt URI tartalmazza az `api-version` argumentumot.

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**Válasz**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="filtering"></a>Szűrés

A és a `key` szűrés kombinációja `label` támogatott.
Használja az opcionális `key` és a `label` lekérdezési karakterlánc paramétereit.

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Támogatott szűrők

|Kulcs szűrője|Hatás|
|--|--|
|`key` nincs megadva vagy `key=*`|Megfelel **bármely** kulcsnak|
|`key=abc`|Az **ABC** nevű kulcsra illeszkedik.|
|`key=abc*`|Az **ABC** -vel kezdődő kulcsok neveinek felel meg|
|`key=abc,xyz`|Az **ABC** vagy az **XYZ** kulcsok neveinek felel meg (legfeljebb 5 CSV)|

|Címke szűrője|Hatás|
|--|--|
|`label` nincs megadva vagy `label=*`|**Bármely** címkére illeszkedik|
|`label=%00`|Megfelel a KV címke nélküli|
|`label=prod`|Megfelel a címkének ( **Prod** )|
|`label=prod*`|A **Prod** kifejezéssel kezdődő címkékre illeszkedik|
|`label=prod,test`|A következő **címkékkel** egyezik **meg (legfeljebb** 5 CSV)|

**_Fenntartott karakterek_* _

`_`, `\`, `,`

Ha egy foglalt karakter az érték része, akkor azt a használatával kell megmenekülnie `\{Reserved Character}` . A nem fenntartott karaktereket is el lehet menekülni.

***Szűrő érvényesítése** _

Szűrő-ellenőrzési hiba esetén a válasz HTTP- `400` hiba részletei:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

_ *Példák**

- Mind

    ```http
    GET /kv?api-version={api-version}
    ```

- A kulcs neve **ABC** -vel kezdődik, és tartalmazza az összes címkét

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- A kulcsnév az **ABC** és a Label értéke **v1** vagy **v2**

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Kérelmek adott mezői

Használja az opcionális `$select` lekérdezési karakterlánc paramétert, és adja meg a kért mezők vesszővel tagolt listáját. Ha a `$select` paraméter nincs megadva, a válasz tartalmazza az alapértelmezett készletet.

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Time-Based hozzáférés

Az eredmény megjelenítésének beszerzése az elmúlt időpontban. Lásd: [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1). szakasz. A tördelés továbbra is támogatott a fent megadott módon.

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Válasz**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```

## <a name="set-key"></a>Kulcs beállítása

- **Kötelező:**``{key}``
- Nem *kötelező:* ``label`` -Ha nincs megadva, vagy felirat = %00, azt jelenti, hogy a KV címke nélkül van.

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**Válaszok**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

Ha az elem zárolva van, a következő választ kapja:

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>Kulcs beállítása (feltételesen)

A verseny feltételeinek megelőzése érdekében használjon `If-Match` vagy `If-None-Match` kérjen fejléceket. Az `etag` argumentum a kulcs ábrázolásának részét képezi.
Ha `If-Match` vagy ki `If-None-Match` van hagyva, a művelet feltétel nélkül jelenik meg.

A következő válasz csak akkor frissíti az értéket, ha az aktuális ábrázolás megfelel a megadott értéknek. `etag`

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

A következő válasz csak akkor frissíti az értéket, ha az aktuális ábrázolás *nem* egyezik a megadott értékkel. `etag`

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

A következő kérelem csak akkor adja meg az értéket, ha már létezik egy ábrázolás:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

A következő kérelem csak akkor adja meg az értéket, ha egy ábrázolás még *nem* létezik:

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**Válaszok**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

vagy

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>Törlés

- **Kötelező:** `{key}` , `{api-version}`
- Nem *kötelező:* `{label}` -Ha nincs megadva, vagy felirat = %00, azt jelenti, hogy a KV címke nélkül van.

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**Válasz:** Visszaküldi a Deleted Key-Value vagy none értéket, ha a kulcs-érték nem létezik.

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

vagy

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>Kulcs törlése (feltétel nélkül)

A **set key (feltétel nélkül) értékhez** hasonlóan

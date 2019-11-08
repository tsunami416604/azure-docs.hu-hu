---
title: Marketplace-mérési szolgáltatás API-k | Azure piactér
description: Az SaaS-ajánlatok használati eseménye az Azure piactéren.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 36ca95191e0e6422bd93360b98243393acad8147
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825479"
---
# <a name="marketplace-metering-service-apis"></a>Marketplace metering service API-k

A használati esemény API lehetővé teszi használati események kibocsátását egy adott megvásárolt entitás számára. A használati eseményre vonatkozó kérelem a közzétevő által az ajánlat közzétételekor megadott mérési szolgáltatási dimenzióra hivatkozik.

## <a name="usage-event"></a>Használati esemény

**Post**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | A kérelemhez használni kívánt művelet verziója. A legújabb API-verzió a 2018-08-31. |

*Kérelem fejlécei:*

| Content-Type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Egyedi karakterlánc-érték az ügyféltől érkező kérelem nyomon követéséhez, lehetőleg egy GUID-azonosító. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `x-ms-correlationid` | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `authorization`   | [A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatának beolvasása.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Megjegyzés: a HTTP-kérelem elkészítésekor az előtag `Bearer` a hivatkozott hivatkozásból beszerzett jogkivonatra. |

*Kérelem*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Válaszok

Kód: 200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Kód: 400 <br>
Hibás kérés, hiányzó vagy érvénytelen az adott vagy lejárt adatmennyiség

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Kód: 403<br>
Hibás kérés, hiányzó vagy érvénytelen az adott vagy lejárt adatmennyiség

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Kód: 409<br>
Ütközés, ha a használati erőforrás-azonosító használati hívását, valamint a már létező érvényes használati adatokat fogadjuk. A válasz `additionalInfo` mezőt tartalmaz, amely az elfogadott üzenet információit tartalmazza.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Batch-használati esemény

A Batch-használati esemény API lehetővé teszi, hogy egyszerre több megvásárolt entitás használati eseményeit is kibocsátsa. A Batch-használati eseményre vonatkozó kérelem a közzétevő által az ajánlat közzétételekor megadott mérési szolgáltatási dimenzióra hivatkozik.

>[!Note]
>Több SaaS-ajánlatot is regisztrálhat a Microsoft kereskedelmi piactéren. Minden regisztrált SaaS-ajánlathoz tartozik egy egyedi Azure AD-alkalmazás, amely hitelesítési és engedélyezési célból van regisztrálva. A Batch szolgáltatásban kibocsátott eseményeknek ugyanahhoz az Azure AD-alkalmazáshoz tartozó ajánlatokhoz kell tartozniuk az ajánlat regisztrálásakor.

**Post:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | A kérelemhez használni kívánt művelet verziója. A legújabb API-verzió a 2018-08-31. |

*Kérelem fejlécei:*

| Content-Type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Egyedi karakterlánc-érték az ügyféltől érkező kérelem nyomon követéséhez, lehetőleg egy GUID-azonosító. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `x-ms-correlationid` | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `authorization`      | [A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatának beolvasása.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Megjegyzés: a HTTP-kérelem elkészítésekor az előtag `Bearer` a hivatkozott hivatkozásból beszerzett jogkivonatra.  |

*Kérelem*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Válaszok

Kód: 200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

`BatchUsageEvent` API-válaszban hivatkozott állapotkód leírása:

| Állapotkód  | Leírás |
| ---------- | -------------------- |
| `Accepted` | Elfogadott kód. |
| `Expired` | Lejárt használat. |
| `Duplicate` | Ismétlődő használat van megadva. |
| `Error` | Hibakód. |
| `ResourceNotFound` | A megadott használati erőforrás érvénytelen. |
| `ResourceNotAuthorized` | Ön nem jogosult az erőforrás használatának megadására. |
| `InvalidDimension` | Ebben az ajánlatban vagy tervben érvénytelen a dimenzió, amelynek a kihasználtsága átadásra kerül. |
| `InvalidQuantity` | Az átadott mennyiség < 0. |
| `BadArgument` | A bemenet hiányzik vagy helytelen formátumú. |

Kód: 400<br>
Hibás kérés, hiányzó vagy érvénytelen az adott vagy lejárt adatmennyiség

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Kód: 403<br>
A felhasználó jogosulatlanul teszi ezt a hívást

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>További lépések

További információ: Saas- [mért számlázás](./saas-metered-billing.md).

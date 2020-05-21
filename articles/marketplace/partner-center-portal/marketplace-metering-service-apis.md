---
title: Mérési szolgáltatás API-k – Microsoft kereskedelmi piactér
description: A használati esemény API lehetővé teszi az SaaS-ajánlatok használati eseményeinek kibocsátását Microsoft AppSource és az Azure Marketplace-en.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 95eba648219413923ce27d433a5236877c4953f3
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725465"
---
# <a name="marketplace-metering-service-apis"></a>Marketplace metering service API-k

A használati esemény API lehetővé teszi használati események kibocsátását egy adott megvásárolt entitás számára. A használati eseményre vonatkozó kérelem a közzétevő által az ajánlat közzétételekor megadott mérési szolgáltatási dimenzióra hivatkozik.

## <a name="usage-event"></a>Használati esemény

**Post**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | A kérelemhez használni kívánt művelet verziója. A legújabb API-verzió a 2018-08-31. |

*Kérelem fejlécei:*

| Content-Type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Egyedi karakterlánc-érték az ügyféltől érkező kérelem nyomon követéséhez, lehetőleg egy GUID-azonosító. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `x-ms-correlationid` | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `authorization`   | [A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatának beolvasása.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Megjegyzés: a HTTP-kérelem elkészítésekor a `Bearer` hivatkozott hivatkozástól kapott jogkivonat előtagját. |

>[!Note]
>Az Azure Application Managed apps-csomagok esetében a a `resourceId` `resourceUsageId` `billingDetails` felügyelt alkalmazás metaadatainak objektuma alatt található.  Az [Azure által felügyelt identitások jogkivonat használatával](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)a beolvasáshoz példaként használható parancsfájl található.  SaaS-ajánlatok esetén a a `resourceId` SaaS-előfizetés azonosítója.  Az SaaS-előfizetésekkel kapcsolatos további információkért lásd: [előfizetések listázása](./pc-saas-fulfillment-api-v2.md#list-subscriptions).

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
Ütközés, ha a használati erőforrás-azonosító használati hívását, valamint a már létező érvényes használati adatokat fogadjuk. A válasz tartalmazni fog `additionalInfo` egy mezőt, amely az elfogadott üzenettel kapcsolatos információkat tartalmaz.

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

**Post:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | A kérelemhez használni kívánt művelet verziója. A legújabb API-verzió a 2018-08-31. |

*Kérelem fejlécei:*

| Content-Type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Egyedi karakterlánc-érték az ügyféltől érkező kérelem nyomon követéséhez, lehetőleg egy GUID-azonosító. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `x-ms-correlationid` | Egyedi karakterlánc-érték a művelethez az ügyfélen. Ez a paraméter a kiszolgálói oldalon található eseményekkel összekapcsolja az ügyfél-művelet összes eseményét. Ha ez az érték nincs megadva, a rendszer létrehoz egy értéket, és megadja a válasz fejléceit. |
| `authorization`      | [A JSON webes jogkivonat (JWT) tulajdonosi jogkivonatának beolvasása.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Megjegyzés: a HTTP-kérelem elkészítésekor a `Bearer` hivatkozott hivatkozástól kapott jogkivonat előtagját.  |

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

Az API-válaszban hivatkozott állapotkód leírása `BatchUsageEvent` :

| Állapotkód  | Description |
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

## <a name="next-steps"></a>Következő lépések

További információ: Saas- [mért számlázás](./saas-metered-billing.md).

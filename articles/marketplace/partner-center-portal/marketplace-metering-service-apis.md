---
title: Piactér-mérési szolgáltatás API-jai | Azure Piactér
description: A SaaS-ajánlatok használati eseménye az Azure Marketplace-en.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 315f36e5aed9dee0a89e1f9f504b18a6bed806e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275747"
---
# <a name="marketplace-metering-service-apis"></a>Marketplace metering service API-k

A használati esemény API lehetővé teszi, hogy egy adott megvásárolt entitás használati eseményeit bocsáthatja ki. A használati eseménykérelem a közzétevő által az ajánlat közzétételekor meghatározott mérési szolgáltatások dimenziójára hivatkozik.

## <a name="usage-event"></a>Használati esemény

**POST**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | A kérelemhez használandó művelet verziója. A legújabb API-verzió: 2018-08-31. |

*Fejlécek kérése:*

| Tartalomtípus       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és megadja a válaszfejlécekben. |
| `x-ms-correlationid` | Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a paraméter korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja. |
| `authorization`   | [JSON webes jogkivonat (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Megjegyzés: A HTTP-kérelem esetén `Bearer` a hivatkozott hivatkozásból beszerzett jogkivonat előtagja. |

*Kérés:*

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
Hibás kérés, hiányzó vagy érvénytelen adatok vagy lejárt

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
Hibás kérés, hiányzó vagy érvénytelen adatok vagy lejárt

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Kód: 409<br>
Ütközés, amikor megkapjuk a használati felhívást a használati erőforrás azonosítója, és a tényleges használat, amely már létezik. A válasz `additionalInfo` olyan mezőt tartalmaz, amely az elfogadott üzenetre tartalmaz információt.

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

## <a name="batch-usage-event"></a>Köteghasználati esemény

A kötegelt használati esemény API lehetővé teszi, hogy egyszerre több megvásárolt entitás használati eseményeit is kibocsáthatja. A köteghasználati eseménykérelem a közzétevő által az ajánlat közzétételekor meghatározott mérési szolgáltatások dimenziójára hivatkozik.

>[!Note]
>Több SaaS-ajánlatot is regisztrálhat a Microsoft kereskedelmi piacán. Minden regisztrált SaaS-ajánlat rendelkezik egy egyedi Azure AD-alkalmazás, amely hitelesítési és engedélyezési célokra van regisztrálva. A kötegben kibocsátott eseményeknek ugyanazzal az Azure AD-alkalmazással rendelkező ajánlatokhoz kell tartozniuk az ajánlat regisztrálásakor.

**HOZZÁSZÓLÁS:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Lekérdezési paraméterek:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | A kérelemhez használandó művelet verziója. A legújabb API-verzió: 2018-08-31. |

*Fejlécek kérése:*

| Tartalomtípus       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Egyedi karakterlánc-érték az ügyféltől érkező kérés nyomon követéséhez, lehetőleg GUID. Ha ez az érték nincs megadva, a függvény létrehoz egyet, és a válaszfejlécekben megadja. |
| `x-ms-correlationid` | Az ügyfélen végzett művelet egyedi karakterlánc-értéke. Ez a paraméter korrelálja az ügyfélműveletből származó összes eseményt a kiszolgálói oldalon lévő eseményekkel. Ha ez az érték nincs megadva, egy jön létre, és a válasz fejlécekben. |
| `authorization`      | [JSON webes jogkivonat (JWT) tulajdonosi token beszerezni.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Megjegyzés: A HTTP-kérelem esetén `Bearer` a hivatkozott hivatkozásból beszerzett jogkivonat előtagja.  |

*Kérés:*
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

Az API-válaszban `BatchUsageEvent` hivatkozott állapotkód leírása:

| Állapotkód  | Leírás |
| ---------- | -------------------- |
| `Accepted` | Elfogadott kód. |
| `Expired` | Lejárt használat. |
| `Duplicate` | Duplikált használat biztosított. |
| `Error` | Hibakód. |
| `ResourceNotFound` | A megadott használati erőforrás érvénytelen. |
| `ResourceNotAuthorized` | Nincs jogosultsága az erőforrás használatának biztosításához. |
| `InvalidDimension` | A dimenzió, amelyhez a használat átlett adták, érvénytelen ehhez az ajánlathoz/tervhez. |
| `InvalidQuantity` | Az átadott mennyiség < 0. |
| `BadArgument` | A bemenet hiányzik vagy hibásan formázott. |

Kód: 400<br>
Hibás kérés, hiányzó vagy érvénytelen adatok vagy lejárt

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
A felhasználó nem kezdeményezi a hívást

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>További lépések

További információ: [SaaS-díjas számlázás.](./saas-metered-billing.md)

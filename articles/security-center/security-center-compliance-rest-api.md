---
title: Az Azure Security Center megfelelőség figyelése a REST API használatával |} A Microsoft Docs
description: Felülvizsgálati eredmények az automatizált megfelelőség az Azure Security Center – REST API-hoz.
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 651d7737258f1b1b17c8392a09882388ddf95635
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819976"
---
# <a name="review-security-center-compliance-results-using-the-azure-rest-apis"></a>Tekintse át a Security Center a megfelelőségi eredményeket az Azure REST API-k használatával

Ez a cikk bemutatja a biztonsági megfelelőségi információinak lekérése egy listát az előfizetések az Azure REST API-k használatával.

## <a name="review-compliance-for-each-subscription"></a>Az egyes előfizetésekhez megfelelőség ellenőrzése

Az alábbi példa lekéri egy adott megfelelőségi és előfizetés használatával biztonsági rendszerfelmérő adatait a [első megfelelőségre](/rest/api/securitycenter/compliances/get) műveletet.

```http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/compliances/{complianceName}?api-version=2017-08-01-preview
```

A `{complianceName}` paraméter megadása kötelező, és tartalmaznia kell a nevét, a értékelt megfelelőségi `{subscription-id}`. Kimeneti például az értékelések eredményeinek fogja tartalmazni:

```json
{
...
  "properties": {
    "assessmentResult": [
      {
        "segmentType": "Compliant",
        "percentage": 77.777777777777786
      }
    ],
    "resourceCount": 18,
    "assessmentTimestampUtcDate": "2018-01-01T00:00:00Z"
  }
}
```

## <a name="review-compliance-for-multiple-subscriptions"></a>Több előfizetés is a megfelelőség ellenőrzése

Több előfizetés adatok lekéréséhez a hívást az alábbi, először a használatával előfizetések listájának lekérése a [lista előfizetések](/rest/api/resources/subscriptions/list) műveletet. A fenti invoke [első megfelelőségre](/rest/api/securitycenter/compliances/get) minden, a visszaadott előfizetés azonosítókat.

Az API-hívás a következő:

```http
GET https://management.azure.com/subscriptions?api-version=2016-06-01
```

Amely például a következő értékekkel tömbjét adja vissza. Előfizetés-azonosító értéket használja a fenti hívásban minden előfizetés esetén a megfelelőségi információk áttekintéséhez.

```json
"value": [
    {
      "id": "/subscriptions/{subscription-id}",
      "subscriptionId": "{subscription-id}",
      "displayName": "Demo subscription",
      ...
    }
```







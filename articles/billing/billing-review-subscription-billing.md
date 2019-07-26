---
title: Az Azure-előfizetés számlázási adatainak áttekintése a REST APIkal | Microsoft Docs
description: Ismerje meg, hogyan tekintheti át az előfizetés számlázási adatait az Azure REST API-k használatával.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: banders
ms.openlocfilehash: 8cfa429b18fb282f5c1f85d2fd1637704653b855
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443052"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Előfizetés számlázásának áttekintése REST API-k használatával

Az Azure Reporting API-k segítik az Azure-költségek áttekintését és kezelését.

A szűrők segítségével testre szabhatja az eredményeket, hogy megfeleljen az igényeinek.

Itt megtudhatja, hogy egy REST API használatával adja vissza az előfizetés számlázási adatait egy adott Dátumtartomány alapján.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>A kérelem létrehozása

A `{subscriptionID}` paraméter megadása kötelező, és azonosítja a célként megadott előfizetést.

A `{billingPeriod}` paraméter megadása kötelező, és az aktuális [Számlázási időszakot](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)határozza meg.

Ehhez `${startDate}` a `${endDate}` példához a és a paraméterek szükségesek, de a végponthoz nem kötelező megadni. A dátumtartományt az éééé-hh-nn (például: `'20180501'` és `'20180615'`) formátumú karakterláncok formájában határozzák meg.

A következő fejlécek szükségesek:

|Kérelem fejléce|Leírás|
|--------------------|-----------------|
|*Content-Type:*|Kötelező. Állítsa a `application/json`következőre:.|
|*Authorization:*|Kötelező. Érvényes `Bearer` [hozzáférési](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients)jogkivonatra van beállítva. |

## <a name="response"></a>Válasz

Sikeres válasz esetén a 200-as (OK) állapotkódot adja vissza a rendszer, amely a fiók részletes költségeinek listáját tartalmazza.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Az **érték** minden eleme a szolgáltatás használatára vonatkozó részleteket jelöl:

|Válasz tulajdonság|Leírás|
|----------------|----------|
|**subscriptionGuid** | Az előfizetés globálisan egyedi azonosítója. |
|**startDate** | A használat megkezdésének dátuma. |
|**endDate** | A használat befejezésének dátuma. |
|**useageQuantity** | Felhasznált mennyiség. |
|**billableQuantity** | Ténylegesen számlázott mennyiség. |
|**pretaxCost** | A számlázott költségeket a vonatkozó adók előtt kell fizetni. |
|**meterDetails** | Részletes információk a használatáról. |
|**nextLink**| Ha be van állítva, a részletek a következő "oldal" URL-címét adja meg. Üres, ha a lap az utolsó. |

Ez a példa rövidítve van; a válasz mezőinek teljes leírását lásd: a [használati adatok listázása](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod) .

Más állapotkódok a hibák feltételeit jelölik. Ezekben az esetekben a válasz objektum megmagyarázza, miért nem sikerült a kérelem végrehajtása.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>További lépések
- A [vállalati jelentéskészítés áttekintésének áttekintése](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- [Vállalati számlázási REST API](https://docs.microsoft.com/rest/api/billing/) vizsgálata
- [Ismerkedés az Azure REST API](https://docs.microsoft.com/rest/api/azure/)

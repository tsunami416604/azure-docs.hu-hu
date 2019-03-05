---
title: Tekintse át az Azure-előfizetés elszámolási adatok REST API-val |} A Microsoft Docs
description: Ismerje meg, tekintse át az előfizetés számlázási adatait az Azure REST API-k használatával.
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
ms.author: erikre
ms.openlocfilehash: 3a487b56c3ce81f3a13add767a9bf7ad59cf79cd
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315945"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Tekintse át az előfizetés számlázási REST API-k használatával

Az Azure Reporting API-k segítségével tekintse át és az Azure-költségek kezeléséhez.

Szűrők segítségével testre szabhatja az eredményeket, hogy az igényeinek.

Itt megtudhatja, egy REST API használatával adja vissza az előfizetés számlázási adatait a megadott dátumtartományban.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>A kérelem létrehozása

A `{subscriptionID}` paraméter megadása kötelező, és azonosítja a cél előfizetésben.

A `{billingPeriod}` paraméter megadása kötelező, és adja meg az aktuális [számlázási időszak](https://docs.microsoft.com/rest/api/billing/billingperiods/get#billingperiod).

A `${startDate}` és `${endDate}` paraméterei ebben a példában megadása kötelező, de a végpont nem kötelező. A dátumtartomány karakterláncként, éééé-hh-nn formátumban kell adnia (példák: `'20180501'` és `'20180615'`).

A következő fejléceket szükség:

|Kérelem fejléce|Leírás|
|--------------------|-----------------|
|*A Content-Type:*|Kötelező. Állítsa be `application/json`.|
|*Hitelesítés:*|Kötelező. Egy érvényes értékre `Bearer` [hozzáférési jogkivonat](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |

## <a name="response"></a>Válasz

Állapotkód: 200 (OK) adja vissza a sikeres válasz, amely a fiók költségeinek részletes listáját tartalmazza.

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

Egyes elemeiről **érték** egy szolgáltatás használatával kapcsolatos részletes adatait jelenti:

|Válasz tulajdonság|Leírás|
|----------------|----------|
|**subscriptionGuid** | Az előfizetés globálisan egyedi azonosítója. |
|**Kezdődátum** | Dátum lépések használatát. |
|**endDate** | Dátum ért használatát. |
|**useageQuantity** | Felhasznált mennyiség. |
|**billableQuantity** | Mennyiség ténylegesen számlázzuk. |
|**pretaxCost** | Számlázott, mielőtt adót költsége. |
|**meterDetails** | Részletes információk a használatát. |
|**nextLink**| Ha a beállítás, a részletek a következő "lap" URL-címet. Ha az oldal az utolsót üres. |

Ebben a példában a rendszer rövidítéseket tartalmaz; Lásd: [listázása a használat részleteiről](https://docs.microsoft.com/rest/api/consumption/usagedetails/listbybillingperiod#usagedetailslistresult) minden válasz mező teljes leírását.

Más állapotkódok hibaállapotok jelzik. Ezekben az esetekben a Válaszobjektum azt ismerteti, miért érdemes a kérelem sikertelen volt.

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
- Felülvizsgálat [Enterprise reporting áttekintése](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Vizsgálja meg [vállalati számlázási REST API](https://docs.microsoft.com/rest/api/billing/)
- [Azure REST API használatának első lépései](https://docs.microsoft.com/rest/api/azure/)

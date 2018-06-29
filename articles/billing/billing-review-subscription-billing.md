---
title: Tekintse át az Azure-előfizetés elszámolási adatok REST API-val |} Microsoft Docs
description: 'Útmutató: Azure REST API-k használata az előfizetés számlázási részletes adatok áttekintésére.'
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: cc29d1f613af67604d50654be794cc90080098bb
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063852"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Tekintse át az előfizetés számlázási REST API-k használatával

Az Azure API-k Reporting súgó tekintse át és kezelése az Azure költségeit.  

Szűrők segítségével testre szabhatja az igényeknek megfelelő eredményeket.

Itt megismerheti, térjen vissza az előfizetés számlázási részleteit egy adott dátumtartományon belül a REST API használatával.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>A kérelem létrehozása  

A `{subscriptionID}` paraméter szükséges, és a célként megadott előfizetés azonosítja.

A `{billingPeriod}` paraméter szükséges, és adja meg az aktuális [számlázási időszakban](https://docs.microsoft.com/rest/api/billing/billingperiods/get#billingperiod).

A `${startDate}` és `${endDate}` paraméterei, szükséges ehhez a példához, de a végpont nem kötelező.  Azok a dátumtartományt karakterláncok éééé-hh-nn formájában adja meg (Példa: `'20180501'` és `'20180615'`). 

A következő fejléc szükség: 

|Fejléc|Leírás|  
|--------------------|-----------------|  
|*Content-Type:*|Kötelező. Beállítása `application/json`.|  
|*Engedélyezési:*|Kötelező. Egy érvényes értékre `Bearer` [hozzáférési jogkivonat](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |  

## <a name="response"></a>Válasz  

Állapotkód 200 (OK) visszaküldött sikeres választ, a fiókjához részletes költségek listáját tartalmazza.

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
        "usageQuantity": ${usageQuantity},
        "billableQuantity": ${billableQuantity},
        "pretaxCost": ${cost},
        "meterId": "${meterID}",
        "meterDetails": ${meterDetails}
      }
    }
    ],
    "nextLink": "${nextLinkURL}"
} 
```  

Az egyes elemek **érték** jelenti. a szolgáltatás használatára vonatkozó részleteket:

|Válasz tulajdonság|Leírás|
|----------------|----------|
|**subscriptionGuid** | Az előfizetés globálisan egyedi azonosítója. | 
|**a startDate** | Dátum lépések használatát. |
|**endDate** | Dátum befejeződött használatát. |
|**useageQuantity** | A mennyiség használt. | 
|**billableQuantity** | A mennyiség ténylegesen terhelve. |
|**pretaxCost** | Számlázott, mielőtt adót költség. | 
|**meterDetails** | Használatával kapcsolatos részletes információt. |
|**nextLink**| Ha a beállítás, a részletek a következő "lap" URL-címet. Legutóbb elem üres. |  
||
  
Ez a példa rövidítése; Lásd: [listában a használat részleteiről](https://docs.microsoft.com/rest/api/consumption/usagedetails/listbybillingperiod#usagedetailslistresult) minden válasz mező teljes leírását. 

Más állapotkódok hibaállapotok jelzi. Ezekben az esetekben a válasz objektum elmagyarázza, a kérelem sikertelenségének okát.

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>További lépések 
- Felülvizsgálati [vállalati jelentéskészítés – áttekintés](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Vizsgálja meg [vállalati számlázási REST API-n](https://docs.microsoft.com/rest/api/billing/)   
- [Ismerkedés az Azure REST API](https://docs.microsoft.com/rest/api/azure/)   

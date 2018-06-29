---
title: Tekintse át az Azure a nagyvállalati beléptetés számlázási adatok REST API-val |} Microsoft Docs
description: 'Útmutató: Azure REST API-k segítségével tekintse át a vállalati beléptetési számlázási adatokat.'
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
ms.openlocfilehash: 046b2e31aaefa5916a42b3652f9e6a8fdceff367
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063997"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Tekintse át a vállalati beléptetési számlázási REST API-k használatával

Az Azure API-k Reporting súgó tekintse át és kezelése az Azure költségeit.

Itt megismerheti a vállalati fiók igénylésére társított aktuális számlázási beolvasása.

Az aktuális számlázási beolvasása:
``` http
GET https://consumption.azure.com/v2/enrollments/{enrollmentID}/usagedetails
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>A kérelem létrehozása  

A `{enrollmentID}` paraméter szükséges, és tartalmaznia kell a regisztrációs azonosítója a(z) a vállalati fiók (EA).

A következő fejléc szükség: 

|Fejléc|Leírás|  
|--------------------|-----------------|  
|*Content-Type:*|Kötelező. Beállítása `application/json`.|  
|*Engedélyezési:*|Kötelező. Egy érvényes értékre `Bearer` [API-kulcs](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ez a példa bemutatja a szinkron hívás, amely az az aktuális elszámolási időszak adatait adja vissza. A szinkron hívások teljesítményének javítására szolgál, térjen vissza a adatai az elmúlt hónapban.  Ön is meghívhatja a [API aszinkron módon](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) 36 hónapja elküldeni.


## <a name="response"></a>Válasz  

Állapotkód 200 (OK) visszaküldött sikeres választ, a fiókjához részletes költségek listáját tartalmazza.

``` json
{
    "id": "${id}",
    "data": [
        {
            "cost": ${cost}, 
            "departmentId": ${departmentID},
            "subscriptionGuid" : ${subscriptionGuid} 
            "date": "${date}",
            "tags": "${tags}",
            "resourceGroup": "${resourceGroup}"
        } // ...
    ],
    "nextLink": "${nextLinkURL}"
}
```  

Az egyes elemek **adatok** díj jelöli:

|Válasz tulajdonság|Leírás|
|----------------|----------|
|**Költség** | Az összeget terhére, a pénznem megfelelő datacenter helyét. |
|**subscriptionGuid** | Az előfizetés globálisan egyedi azonosítója. | 
|**departmentId** | A részleg, ha van ilyen Azonosítójú. |
|**Dátum** | A dátum az elsők között volt terhelve. |
|**címkét** | Az előfizetéshez tartozó címkék tartalmazó JSON-karakterlánc. |
|**resourceGroup**|Az objektum, amely a költségeket tartalmazó erőforráscsoport nevét. |
|**nextLink**| Ha a beállítás, a részletek a következő "lap" URL-címet. Legutóbb elem üres. |  
||
  
A Nagyvállalati felügyeleti részleg azonosítók, az erőforráscsoportok, a címkék és a kapcsolódó mezők vannak definiálva.  

Ez a példa rövidítése; Lásd: [első használat részletei](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) minden válasz mező teljes leírását. 

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

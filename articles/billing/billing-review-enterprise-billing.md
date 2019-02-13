---
title: Tekintse át az Azure nagyvállalati beléptetés számlázási adatok REST API-val |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure REST API-k a vállalati beléptetési számlázási információk áttekintéséhez.
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
ms.openlocfilehash: 5cb26b98f5969032bcff95e4408fcf685399d6da
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114501"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Tekintse át a vállalati beléptetési számlázási REST API-k használatával

Az Azure Reporting API-k segítségével tekintse át és az Azure-költségek kezeléséhez.

Ez a cikk bemutatja a számlázási fiókok, részleg vagy az Azure REST API-k használatával a nagyvállalati szerződés (EA) regisztrációs fiókok tartozó számlázási információk lekéréséhez. 

## <a name="individual-account-billing"></a>Egyéni fiók számlázás

A felhasználói fiókok beolvasása a használat részleteiről:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

A `{billingAccountId}` paraméter megadása kötelező, és tartalmaznia kell a fiók azonosítója.

A következő fejléceket szükség: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*A Content-Type:*|Kötelező. Állítsa be `application/json`.|  
|*Hitelesítés:*|Kötelező. Egy érvényes értékre `Bearer` [API-kulcs](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ez a példa bemutatja egy szinkron hívás, amely az aktuális elszámolási időszakban az adatait adja vissza. Teljesítménybeli megfontolások miatt a szinkron hívások számára az elmúlt hónapban információkat ad vissza.  Ön is meghívhatja a [API aszinkron módon](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) való 36 hónapos adatokat adja vissza.


## <a name="response"></a>Válasz  

Állapotkód: 200 (OK) adja vissza a sikeres válasz, amely a fiók költségeinek részletes listáját tartalmazza.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Ebben a példában a rendszer rövidítéseket tartalmaz; Lásd: [számlázási fiók részletes használati adatok lekérése](/rest/api/consumption/usagedetails/listbybillingaccount) minden válasz mező és a hibakezelés részletes ismertetése.

## <a name="department-billing"></a>Részleg számlázás 

Egy osztály összes fiók összesített használati részleteinek beolvasása. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

A `{departmentId}` paraméter megadása kötelező, és tartalmaznia kell az osztály a regisztrációs fiók azonosítója.

A következő fejléceket szükség: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*A Content-Type:*|Kötelező. Állítsa be `application/json`.|  
|*Hitelesítés:*|Kötelező. Egy érvényes értékre `Bearer` [API-kulcs](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ez a példa bemutatja egy szinkron hívás, amely az aktuális elszámolási időszakban az adatait adja vissza. Teljesítménybeli megfontolások miatt a szinkron hívások számára az elmúlt hónapban információkat ad vissza.  Ön is meghívhatja a [API aszinkron módon](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) való 36 hónapos adatokat adja vissza.

### <a name="response"></a>Válasz  

Állapotkód: 200 (OK) adja vissza a sikeres válasz, amely egy adott számlázási időszak és a számla Azonosítót az osztály a részletes használati adatok és a költségek listáját tartalmazza.


Az alábbi példa bemutatja a REST API-val részleg kimenete `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Ebben a példában a rendszer rövidítéseket tartalmaz; Lásd: [felhasználási részletek beolvasása Intézet](/rest/api/consumption/usagedetails/listbydepartment) minden válasz mező és a hibakezelés részletes ismertetése.

## <a name="enrollment-account-billing"></a>Regisztrációs fiók számlázás

A regisztrációs fiók összesített használati részleteinek beolvasása.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

A `{enrollmentAccountId}` paraméter megadása kötelező, és tartalmaznia kell az eszközregisztráció-fiók azonosítója.

A következő fejléceket szükség: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*A Content-Type:*|Kötelező. Állítsa be `application/json`.|  
|*Hitelesítés:*|Kötelező. Egy érvényes értékre `Bearer` [API-kulcs](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ez a példa bemutatja egy szinkron hívás, amely az aktuális elszámolási időszakban az adatait adja vissza. Teljesítménybeli megfontolások miatt a szinkron hívások számára az elmúlt hónapban információkat ad vissza.  Ön is meghívhatja a [API aszinkron módon](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) való 36 hónapos adatokat adja vissza.

### <a name="response"></a>Válasz  

Állapotkód: 200 (OK) adja vissza a sikeres válasz, amely egy adott számlázási időszak és a számla Azonosítót az osztály a részletes használati adatok és a költségek listáját tartalmazza.

Az alábbi példa bemutatja a REST API, a nagyvállalati beléptetés kimenete `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

Ebben a példában a rendszer rövidítéseket tartalmaz; Lásd: [regisztrációs fiók részletes használati adatok lekérése](/rest/api/consumption/usagedetails/listbyenrollmentaccount) minden válasz mező és a hibakezelés részletes ismertetése.

## <a name="next-steps"></a>További lépések 
- Felülvizsgálat [Enterprise reporting áttekintése](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Vizsgálja meg [vállalati számlázási REST API](https://docs.microsoft.com/rest/api/billing/)   
- [Azure REST API használatának első lépései](https://docs.microsoft.com/rest/api/azure/)   

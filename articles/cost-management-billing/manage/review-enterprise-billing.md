---
title: Azure-beli nagyvállalati regisztráció számlázási adatainak áttekintése REST API-val | Microsoft Docs
description: Megtudhatja, hogyan tekintheti át a nagyvállalati regisztrációk számlázási adatait Azure REST API-kkal.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: cost-management-billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 222b0358f027e0a6687ca0710e3cf5b80f292c4e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "75993465"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Nagyvállalati regisztráció számlázásának áttekintése REST API-kkal

Az Azure Reporting API-k az Azure-költségek áttekintésében és kezelésében segítenek.

Ebből a cikkből megtudhatja, hogyan kérheti le a számlázási fiókokkal, részlegekkel vagy Nagyvállalati Szerződéssel (EA) rendelkező regisztrációs fiókokkal kapcsolatos számlázási adatokat az Azure REST API-k használatával. 

## <a name="individual-account-billing"></a>Egyéni fiókok számlázása

Egy részlegben lévő fiókok használati adatainak lekérése:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

A `{billingAccountId}` paraméter megadása kötelező, és tartalmaznia kell a fiók azonosítóját.

A következő fejlécek megadása kötelező: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):|Kötelező. Állítsa `application/json` értékre.|  
|*Authorization* (Engedélyezés):|Kötelező. Állítsa egy érvényes `Bearer` [API-kulcsra](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ez a példa egy szinkron hívást mutat be, amely az aktuális számlázási ciklus részleteit adja vissza. A teljesítmény érdekében a szinkron hívások a múlt hónapra vonatkozó adatokat adják vissza.  Az elmúlt 36 hónap adataiért [aszinkron módon is meghívhatja az API-t](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based).


## <a name="response"></a>Válasz  

Sikeres válasz esetén a rendszer a 200-as (OK) állapotkódot adja vissza, amely a fiók részletes költségeinek listáját tartalmazza.

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

Ez a példa rövidítve van; a válaszmezők és a hibakezelés teljes leírását a [számlázási fiókok használati adatainak lekérésével](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist-legacy) kapcsolatos szakaszban találja.

## <a name="department-billing"></a>Részleg számlázása 

Lekérheti egy részleg összes fiókjának összesített használati adatait. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

A `{departmentId}` paraméter megadása kötelező, és tartalmaznia kell a regisztrációs fiók részlegének azonosítóját.

A következő fejlécek megadása kötelező: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):|Kötelező. Állítsa `application/json` értékre.|  
|*Authorization* (Engedélyezés):|Kötelező. Állítsa egy érvényes `Bearer` [API-kulcsra](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ez a példa egy szinkron hívást mutat be, amely az aktuális számlázási ciklus részleteit adja vissza. A teljesítmény érdekében a szinkron hívások a múlt hónapra vonatkozó adatokat adják vissza.  Az elmúlt 36 hónap adataiért [aszinkron módon is meghívhatja az API-t](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based).

### <a name="response"></a>Válasz  

Sikeres válasz esetén a rendszer a 200 (OK) állapotkódot adja vissza, amely tartalmazza egy adott számlázási időszak részletes használati adatainak és költségeinek listáját, valamint a részleg számlájának azonosítóját.


Az alábbi példa az `1234` részleg REST API-jának kimenetét mutatja be.

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

Ez a példa rövidítve van; a válaszmezők és a hibakezelés teljes leírását a [részlegek használati adatainak lekérésével](/rest/api/consumption/usagedetails/list#departmentusagedetailslist-legacy) kapcsolatos szakaszban találja.

## <a name="enrollment-account-billing"></a>Regisztrációs fiók számlázása

Lekérheti a regisztrációs fiók összesített használati adatait.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Az `{enrollmentAccountId}` paraméter megadása kötelező, és tartalmaznia kell a regisztrációs fiók azonosítóját.

A következő fejlécek megadása kötelező: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type* (Tartalomtípus):|Kötelező. Állítsa `application/json` értékre.|  
|*Authorization* (Engedélyezés):|Kötelező. Állítsa egy érvényes `Bearer` [API-kulcsra](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Ez a példa egy szinkron hívást mutat be, amely az aktuális számlázási ciklus részleteit adja vissza. A teljesítmény érdekében a szinkron hívások a múlt hónapra vonatkozó adatokat adják vissza.  Az elmúlt 36 hónap adataiért [aszinkron módon is meghívhatja az API-t](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based).

### <a name="response"></a>Válasz  

Sikeres válasz esetén a rendszer a 200 (OK) állapotkódot adja vissza, amely tartalmazza egy adott számlázási időszak részletes használati adatainak és költségeinek listáját, valamint a részleg számlájának azonosítóját.

Az alábbi példa az `1234` vállalati regisztráció REST API-jának kimenetét mutatja be.

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

Ez a példa rövidítve van; a válaszmezők és a hibakezelés teljes leírását a [regisztrációs fiókok használati adatainak lekérésével](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist-legacy) kapcsolatos szakaszban találja.

## <a name="next-steps"></a>További lépések 
- Tekintse meg [a vállalati jelentéskészítés áttekintését](https://docs.microsoft.com/azure/billing/billing-enterprise-api) ismertető szakaszt
- Vizsgálja meg az [Enterprise Billing REST API](https://docs.microsoft.com/rest/api/billing/) használatának lehetőségét   
- [Bevezetés az Azure REST API használatába](https://docs.microsoft.com/rest/api/azure/)   

---
title: Az Azure Enterprise beléptetési számlázási adatai áttekinthető a REST APIkal | Microsoft Docs
description: Ismerje meg, hogyan tekintheti át a vállalati regisztráció számlázási adatait az Azure REST API-k használatával.
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
ms.openlocfilehash: 25d9b48696dc2a83ea0ba77c1be2c7aad7627fff
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443149"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Vállalati beléptetési számlázás áttekintése REST API-k használatával

Az Azure Reporting API-k segítik az Azure-költségek áttekintését és kezelését.

Ebből a cikkből megtudhatja, hogyan kérheti le a számlázási fiókokkal, részleggel vagy nagyvállalati szerződéssel (EA) rendelkező beléptetési fiókokkal kapcsolatos számlázási adatokat az Azure REST API-k használatával. 

## <a name="individual-account-billing"></a>Egyéni fiók számlázása

A részleg fiókjainak használati részleteinek beszerzése:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

A `{billingAccountId}` paraméter megadása kötelező, és tartalmaznia kell a fiók azonosítóját.

A következő fejlécek szükségesek: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type:*|Kötelező. Állítsa a `application/json`következőre:.|  
|*Authorization:*|Kötelező. Érvényes `Bearer` [API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)-kulcsra van beállítva. |  

Ez a példa egy szinkron hívást mutat be, amely az aktuális számlázási ciklus részleteit adja vissza. A szinkron hívások a múlt hónapra vonatkozó adatokat adják vissza.  Az API-t [aszinkron módon](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) is meghívhatja, hogy 36 hónapig adják vissza az adatkérést.


## <a name="response"></a>Válasz  

Sikeres válasz esetén a 200 (OK) állapotkódot adja vissza a rendszer, amely a fiók részletes költségeinek listáját tartalmazza.

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

Ez a példa rövidítve van; a válaszadási mezők és a hibakezelés teljes leírását a [Számlázási fiók használatának részletes](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist) ismertetése című témakörben olvashatja.

## <a name="department-billing"></a>Részleg számlázása 

A részleg összes fiókja számára összesített használati adatok beolvasása. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

A `{departmentId}` paraméter megadása kötelező, és tartalmaznia kell a részleg azonosítóját a beléptetési fiókban.

A következő fejlécek szükségesek: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type:*|Kötelező. Állítsa a `application/json`következőre:.|  
|*Authorization:*|Kötelező. Érvényes `Bearer` [API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)-kulcsra van beállítva. |  

Ez a példa egy szinkron hívást mutat be, amely az aktuális számlázási ciklus részleteit adja vissza. A szinkron hívások a múlt hónapra vonatkozó adatokat adják vissza.  Az API-t [aszinkron módon](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) is meghívhatja, hogy 36 hónapig adják vissza az adatkérést.

### <a name="response"></a>Válasz  

Sikeres válasz esetén a 200 (OK) állapotkódot adja vissza, amely tartalmazza a részletes használati adatok listáját, valamint az adott számlázási időszakra és a részleg Számlázási AZONOSÍTÓJÁHOZ tartozó költségeket.


Az alábbi példa a részleg `1234`REST API kimenetét mutatja be.

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

Ez a példa rövidítve van; Tekintse meg a [használati adatok](/rest/api/consumption/usagedetails/list#departmentusagedetailslist) lekérése a részleg számára című témakört az egyes válaszok mezőinek és hibakezelés teljes leírását illetően.

## <a name="enrollment-account-billing"></a>Regisztrációs fiók számlázása

A beléptetési fiók összesített használati adatainak beolvasása.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

A `{enrollmentAccountId}` paraméter megadása kötelező, és tartalmaznia kell a beléptetési fiók azonosítóját.

A következő fejlécek szükségesek: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*Content-Type:*|Kötelező. Állítsa a `application/json`következőre:.|  
|*Authorization:*|Kötelező. Érvényes `Bearer` [API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)-kulcsra van beállítva. |  

Ez a példa egy szinkron hívást mutat be, amely az aktuális számlázási ciklus részleteit adja vissza. A szinkron hívások a múlt hónapra vonatkozó adatokat adják vissza.  Az API-t [aszinkron módon](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) is meghívhatja, hogy 36 hónapig adják vissza az adatkérést.

### <a name="response"></a>Válasz  

Sikeres válasz esetén a 200 (OK) állapotkódot adja vissza, amely tartalmazza a részletes használati adatok listáját, valamint az adott számlázási időszakra és a részleg Számlázási AZONOSÍTÓJÁHOZ tartozó költségeket.

A következő példa a vállalati beléptetés `1234`REST API kimenetét mutatja be.

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

Ez a példa rövidítve van; a válaszadási mezők és a hibakezelés teljes leírását a beléptetési [fiók használatának részletes](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist) ismertetése című témakörben olvashatja.

## <a name="next-steps"></a>További lépések 
- A [vállalati jelentéskészítés áttekintésének áttekintése](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- [Vállalati számlázási REST API](https://docs.microsoft.com/rest/api/billing/) vizsgálata   
- [Ismerkedés az Azure REST API](https://docs.microsoft.com/rest/api/azure/)   

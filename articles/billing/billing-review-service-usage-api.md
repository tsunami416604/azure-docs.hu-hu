---
title: Tekintse át az erőforrás-használat az Azure szolgáltatás REST API-val |} A Microsoft Docs
description: Ismerje meg, tekintse át az Azure service erőforrás-használat az Azure REST API-k használatával.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alleonar
ms.openlocfilehash: 1b7b1455413fb4886b317d468e6d278111c094b1
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40225924"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Tekintse át az Azure erőforrás-használati REST API használatával


Azure [szolgáltatáshasználati API-jai](https://docs.microsoft.com/rest/api/consumption/) súgó, tekintse át a költség- és használati adatokat az Azure-erőforrások számára.

Ebben a cikkben megismerheti, hogyan kérhető le, és összesített erőforrás-használati adatai az Azure-erőforráscsoport erőforrásainak is, hogy hogyan alapján történő szűréshez ezekkel az eredményekkel [az Azure resource manager-címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags).

## <a name="get-usage-for-a-resource-group"></a>Egy erőforráscsoport használatának megtekintése

A számítási, adatbázis és egyéb erőforrásokat egy erőforráscsoportba tartozó erőforrás-használat lekéréséhez használja a `usageDetails` REST-műveletet, és szűrheti az eredményeket erőforráscsoportot.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30&filter=properties/resourceGroup eq '{resource-group}]
Content-Type: application/json   
Authorization: Bearer
```

A `{subscription-id}` paraméter megadása kötelező, és tartalmaznia kell egy előfizetés-azonosító eléréséhez az olvasói szerepkörrel rendelkező {erőforráscsoport} erőforráscsoport. 

A következő fejléceket szükség: 

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
|*A Content-Type:*| Kötelező. Állítsa be `application/json`. |  
|*Hitelesítés:*| Kötelező. Egy érvényes értékre `Bearer` token. |

### <a name="response"></a>Válasz  

200 (OK) állapotkódot ad vissza, amely tartalmazza az egyes Azure-erőforrások az erőforráscsoportban subscriptipon azonosítójú vonatkozó lemezhasználati statisztikákat listáját sikeres válasz `00000000-0000-0000-0000-000000000000`.

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        "meterId": "00000000-0000-0000-0000-000000000000",
        "partNumber": "Part Number 1",
        "resourceGuid": "00000000-0000-0000-0000-000000000000",
        "offerId": "Offer Id 1",
        "chargesBilledSeparately": true,
        "location": "EU West"
      }
    } ] }
```

## <a name="get-usage-for-tagged-resources"></a>Címkézett erőforrások használatának megtekintése

Erőforrás-használat beolvasni az erőforrásokat használja, a címkék szerint vannak rendezve a `usageDetails` REST-műveletet, és szűrheti az eredményeket a címke neve használatával a `$filter` lekérdezési paraméter.

```http
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Consumption/usageDetails?$filter=tags eq 'tag1'&api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

A `{subscription-id}` paraméter megadása kötelező, és tartalmaznia kell egy előfizetés-azonosító a címkézett erőforrások eléréséhez.


### <a name="response"></a>Válasz  

200 (OK) állapotkódot ad vissza, amely tartalmazza az egyes Azure-erőforrások az erőforráscsoportban subscriptipon azonosítójú vonatkozó lemezhasználati statisztikákat listáját sikeres válasz `00000000-0000-0000-0000-000000000000` és a címke nevét a key vault pár `dev` és `tools`. 

Mintaválasz:

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "tags": {
        "dev": "tools"
      },
      "properties": {
        "billingPeriodId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        "meterId": "00000000-0000-0000-0000-000000000000",
        "partNumber": "Part Number 1",
        "resourceGuid": "00000000-0000-0000-0000-000000000000",
        "offerId": "Offer Id 1",
        "chargesBilledSeparately": true,
        "location": "EU West"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések
- [Azure REST API használatának első lépései](https://docs.microsoft.com/rest/api/azure/)   

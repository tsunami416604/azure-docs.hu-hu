---
title: Ajánlat közzététele | Azure Piactér
description: API-t a megadott ajánlat közzétételéhez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255940"
---
# <a name="publish-an-offer"></a>Ajánlat közzététele

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a Partnerközponttal, és az ajánlatok partnerközpontba való áttelepítése után is működni fognak. Az integráció kis változtatásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) felsorolt módosításokat, és győződjön meg arról, hogy a kód továbbra is működik a Partnerközpontba való áttelepítés után.

Elindítja a megadott ajánlat közzétételi folyamatát. Ez a hívás egy hosszú ideig futó művelet.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Név**      |    **Leírás**                               |  **Adattípus** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Közzétevő azonosítója, például`contoso`      |   Sztring       |
|  offerId       | Ajánlat azonosítója                                 |   Sztring       |
|  api-verzió   | Az API legújabb verziója                        |   Dátum         |
|  |  |

## <a name="header"></a>Fejléc
------

|  **Név**        |    **Érték**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Engedélyezés   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Példa törzsre
------------

### <a name="request"></a>Kérés

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Törzstulajdonságainak kérése

|  **Név**               |   **Leírás**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  értesítési e-mailek    | A közzétételi művelet előrehaladásáról értesítendő e-mail címek vesszővel tagolt listája. |
|  |  |


### <a name="response"></a>Válasz

#### <a name="migrated-offers"></a>Áttelepített ajánlatok

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nem áttelepített ajánlatok

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Válaszfejléc

|  **Név**             |    **Érték**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Hely    | A művelet állapotának beolvasásához vezető relatív elérési út     |
|  |  |


### <a name="response-status-codes"></a>Válasz állapotkódjai

| **Kód** |  **Leírás**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`- A kérés sikeresen elfogadásra került. A válasz tartalmaz egy helyet, amely az elindított művelet nyomon követésére használható. |
| 400   | `Bad/Malformed request`- A hibaelhárítási testület további információkat nyújthat.                                                               |
| 422   | `Un-processable entity`- Azt jelzi, hogy a közzétenni kívánt entitás nem sikerült az érvényesítés.                                                        |
| 404   | `Not found`- Az ügyfél által megadott entitás nem létezik.                                                                              |
|  |  |

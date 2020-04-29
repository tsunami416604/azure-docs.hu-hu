---
title: Ajánlat közzététele | Azure piactér
description: API a megadott ajánlat közzétételéhez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255940"
---
# <a name="publish-an-offer"></a>Ajánlat közzététele

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a partneri központtal, és továbbra is működni fognak, miután az ajánlatokat áttelepítik a partner központba. Az integráció kis változásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) felsorolt módosításokat, hogy a kód továbbra is működni fog a partneri központba való Migrálás után.

Elindítja a közzétételi folyamatot a megadott ajánlathoz. Ez a hívás hosszú ideig futó művelet.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Név**      |    **Leírás**                               |  **Adattípus** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Közzétevő azonosítója, például:`contoso`      |   Sztring       |
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


## <a name="body-example"></a>Példa szövegtörzsre
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

### <a name="request-body-properties"></a>Kérelem törzsének tulajdonságai

|  **Név**               |   **Leírás**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  értesítés – e-mailek    | Az e-mail-címek vesszővel tagolt listája, amelyekről értesíteni kell a közzétételi művelet állapotát. |
|  |  |


### <a name="response"></a>Válasz

#### <a name="migrated-offers"></a>Áttelepített ajánlatok

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nem áttelepített ajánlatok

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Válaszfejléc

|  **Név**             |    **Érték**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Hely    | A művelet állapotának lekéréséhez szükséges relatív elérési út     |
|  |  |


### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód** |  **Leírás**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`– A kérés elfogadása sikeresen megtörtént. A válasz olyan helyet tartalmaz, amely az elindított művelet nyomon követésére használható. |
| 400   | `Bad/Malformed request`– A hiba-válasz törzse további információkat is biztosíthat.                                                               |
| 422   | `Un-processable entity`– Azt jelzi, hogy a közzétenni kívánt entitás ellenőrzése sikertelen volt.                                                        |
| 404   | `Not found`– Az ügyfél által megadott entitás nem létezik.                                                                              |
|  |  |

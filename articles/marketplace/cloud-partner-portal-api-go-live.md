---
title: Go Live API – Azure Marketplace
description: A Go Live API elindítja az ajánlat élő listázási folyamatát.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 5d550f576108447a88660321899f2f55ffeb3c77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516270"
---
# <a name="go-live"></a>Élő indítás

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a partneri központtal, és továbbra is működni fognak, miután az ajánlatokat áttelepítik a partner központba. Az integráció kis változásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működni fog a partneri központba való Migrálás után.

Ez az API elindítja az alkalmazás éles környezetben való továbbításának folyamatát. Ez a művelet általában hosszú ideig fut. Ez a hívás az értesítési e-mailek listáját használja a [közzétételi](./cloud-partner-portal-api-publish-offer.md) API-műveletből.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**      |   **Leírás**                                                           | **Adattípus** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | A lekérdezni kívánt ajánlat közzétevő-azonosítója, például:`contoso`       |  Sztring       |
| offerId        | A lekérdezni kívánt ajánlat azonosítója                                   |  Sztring       |
| api-verzió    | Az API legújabb verziója                                                   |  Dátum         |
|  |  |  |

## <a name="header"></a>Fejléc
------

|  **Name (Név)**       |     **Érték**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Engedélyezés   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Példa szövegtörzsre

### <a name="response"></a>Válasz

#### <a name="migrated-offers"></a>Áttelepített ajánlatok

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nem áttelepített ajánlatok

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Válaszfejléc

|  **Name (Név)**             |      **Érték**                                                            |
|  --------             |      ----------                                                           |
| Hely    |  A művelet állapotának lekéréséhez szükséges relatív elérési út            |
|  |  |

### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Code** |  **Leírás**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`– A kérés elfogadása sikeresen megtörtént. A válasz a művelet állapotának nyomon követésére szolgáló helyet tartalmaz. |
|  400     | `Bad/Malformed request`– További hibaüzenetek találhatók a válasz törzsében. |
|  404     |  `Not found`– A megadott entitás nem létezik.                                       |
|  |  |

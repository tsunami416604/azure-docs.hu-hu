---
title: Élő adás | Azure Piactér
description: A Go Live API elindítja az ajánlat élő tőzsdei eljárását.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ef22f7720a4af2239c55d1a01f9d3f11c878d66e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256314"
---
# <a name="go-live"></a>Élő adás

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a Partnerközponttal, és az ajánlatok partnerközpontba való áttelepítése után is működni fognak. Az integráció kis változtatásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) felsorolt módosításokat, és győződjön meg arról, hogy a kód továbbra is működik a Partnerközpontba való áttelepítés után.

Ez az API elindítja az alkalmazás éles környezetbe való lenyomásának folyamatát. Ez a művelet általában hosszú ideig fut. Ez a hívás az [API-közzétételi](./cloud-partner-portal-api-publish-offer.md) műveletértesítési e-mail listáját használja.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Név**      |   **Leírás**                                                           | **Adattípus** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | A lekérni ajánlott közzétevői azonosítója, például`contoso`       |  Sztring       |
| offerId        | A lekérni ajánlott ajánlat azonosítója                                   |  Sztring       |
| api-verzió    | Az API legújabb verziója                                                   |  Dátum         |
|  |  |  |

## <a name="header"></a>Fejléc
------

|  **Név**       |     **Érték**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Engedélyezés   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Példa törzsre

### <a name="response"></a>Válasz

#### <a name="migrated-offers"></a>Áttelepített ajánlatok

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nem áttelepített ajánlatok

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Válaszfejléc

|  **Név**             |      **Érték**                                                            |
|  --------             |      ----------                                                           |
| Hely    |  A művelet állapotának beolvasásához vezető relatív elérési út            |
|  |  |

### <a name="response-status-codes"></a>Válasz állapotkódjai

| **Kód** |  **Leírás**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted`- A kérés sikeresen elfogadásra került. A válasz tartalmaz egy helyet a művelet állapotának nyomon követéséhez. |
|  400     | `Bad/Malformed request`- További hibainformáció található a válaszszervezetben. |
|  404     |  `Not found`- A megadott entitás nem létezik.                                       |
|  |  |

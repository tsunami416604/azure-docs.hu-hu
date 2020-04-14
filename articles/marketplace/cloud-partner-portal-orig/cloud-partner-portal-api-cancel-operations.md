---
title: Művelet megszakítása API | Azure Piactér
description: Műveletek megszakítása .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: f9e55ff2c581f9392a125f6dc3ec8d903e9876a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256433"
---
# <a name="cancel-operation"></a>Művelet megszakítása

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a Partnerközponttal, és az ajánlatok partnerközpontba való áttelepítése után is működni fognak. Az integráció kis változtatásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) felsorolt módosításokat, és győződjön meg arról, hogy a kód továbbra is működik a Partnerközpontba való áttelepítés után.

Ez az API megszakítja az ajánlatjelenleg folyamatban lévő műveletet. A [Beolvasási műveletek API-t,](./cloud-partner-portal-api-retrieve-operations.md) hogy egy `operationId` átad az API-t. A megszakítás általában szinkron művelet, azonban néhány összetett esetben új műveletre lehet szükség egy meglévő megszakításához. Ebben az esetben a HTTP-válasz törzs tartalmazza a művelet helyét, amelyet a lekérdezési állapothoz kell használni.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-paraméterek

--------------

|  **Név**    |      **Leírás**                                  |    **Adattípus**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  A közzétevő azonosítója, például:`contoso`         |   Sztring          |
| offerId      |  Ajánlat azonosítója                                     |   Sztring          |
| api-verzió  |  Az API jelenlegi verziója                               |    Dátum           |
|  |  |  |

## <a name="header"></a>Fejléc
------

|  **Név**              |  **Érték**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Engedélyezés         |  Hordozója A TOKEN |
|  |  |

## <a name="body-example"></a>Példa törzsre
------------

### <a name="request"></a>Kérés

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Törzstulajdonságainak kérése

|  **Név**                |  **Leírás**                                               |
|  --------                |  ---------------                                               |
|  értesítési e-mailek     | Vesszővel elválasztott e-mail azonosítók listáját kell értesíteni a folyamatban lévő közzétételi művelet. |
|  |  |

### <a name="response"></a>Válasz

#### <a name="migrated-offers"></a>Áttelepített ajánlatok

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nem áttelepített ajánlatok

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Válaszfejléc

|  **Név**             |    **Érték**                       |
|  ---------            |    ----------                      |
| Hely    | A művelet állapotának beolvasásához vezető relatív elérési út. |
|  |  |

### <a name="response-status-codes"></a>Válasz állapotkódjai

| **Kód**  |  **Leírás**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | oké. A kérés feldolgozása sikeresen megtörtént, és a művelet szinkron módon megszakítható. |
|  202      | Elfogadott. A kérés feldolgozása sikeresen megtörtént, és a művelet visszavonása folyamatban van. A lemondási művelet helye a válaszfejlécben kerül visszaadásra. |
|  400      | Hibás/hibásan formázott kérelem. A hibaválasz törzse további információkat nyújthat.  |
|  403      | Belépés tiltott. Az ügyfél nem rendelkezik hozzáféréssel a kérelemben megadott névtérhez. |
|  404      | Nem található. A megadott entitás nem létezik. |
|  |  |

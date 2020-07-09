---
title: Operation API megszakítása – Microsoft kereskedelmi piactér
description: Az ajánlaton jelenleg folyamatban lévő művelet megszakítására szolgáló API
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: anbene
ms.author: mingshen
ms.date: 06/16/2020
ms.openlocfilehash: e65f0a8ee0a5dfafab681010006fe190cb5bad70
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102752"
---
# <a name="cancel-operation"></a>Művelet megszakítása

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a partneri központtal, és továbbra is működni fognak, miután az ajánlatokat áttelepítik a partner központba. Az integráció kis változásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működni fog a partneri központba való Migrálás után.

Ez az API megszakítja az ajánlaton jelenleg folyamatban lévő műveletet. Az [Operations API beolvasása](./cloud-partner-portal-api-retrieve-operations.md) paranccsal továbbíthatja ezt az API-t `operationId` . A megszakítás általában egy szinkron művelet, azonban bizonyos összetett forgatókönyvek esetén új műveletre lehet szükség a meglévő megszakítása érdekében. Ebben az esetben a HTTP-válasz törzse tartalmazza a művelet azon helyét, amelyet az állapot lekérdezéséhez kell használni.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-paraméterek

--------------

|  **Name (Név)**    |      **Leírás**                                  |    **Adattípus**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Közzétevő azonosítója, például:`contoso`         |   Sztring          |
| offerId      |  Ajánlat azonosítója                                     |   Sztring          |
| api-verzió  |  Az API jelenlegi verziója                               |    Dátum           |
|  |  |  |

## <a name="header"></a>Fejléc
------

|  **Name (Név)**              |  **Érték**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Engedélyezés         |  A TOKEN birtokosa |
|  |  |

## <a name="body-example"></a>Példa szövegtörzsre
------------

### <a name="request"></a>Kérés

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Kérelem törzsének tulajdonságai

|  **Name (Név)**                |  **Leírás**                                               |
|  --------                |  ---------------                                               |
|  értesítés – e-mailek     | Az e-mail-azonosítók vesszővel tagolt listája, amelyekről értesíteni kell a közzétételi művelet állapotát. |
|  |  |

### <a name="response"></a>Válasz

#### <a name="migrated-offers"></a>Áttelepített ajánlatok

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Nem áttelepített ajánlatok

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Válaszfejléc

|  **Name (Név)**             |    **Érték**                       |
|  ---------            |    ----------                      |
| Hely    | A művelet állapotának lekéréséhez szükséges relatív elérési út. |
|  |  |

### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Code**  |  **Leírás**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | oké. A rendszer sikeresen feldolgozta a kérést, és a művelet szinkron módon megszakítva. |
|  202      | Elfogadott. A rendszer sikeresen feldolgozta a kérést, és a művelet megszakítása folyamatban van. A visszavonási művelet helye a válasz fejlécében lesz visszaadva. |
|  400      | Helytelen vagy helytelenül formázott kérelem. A hiba-válasz törzse további információkat biztosíthat.  |
|  403      | Hozzáférés megtiltva. Az ügyfél nem fér hozzá a kérelemben megadott névtérhez. |
|  404      | Nem található. A megadott entitás nem létezik. |
|  |  |

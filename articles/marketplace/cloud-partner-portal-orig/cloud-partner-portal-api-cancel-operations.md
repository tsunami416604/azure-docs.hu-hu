---
title: Művelet megszakítása API | Azure Piactér
description: Műveletek megszakítása .
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6d4c1f52f0f3b1e05ec06f5a66a36323f346d4eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280541"
---
# <a name="cancel-operation"></a>Művelet megszakítása 

Ez az API megszakítja az ajánlatjelenleg folyamatban lévő műveletet. A [Beolvasási műveletek API-t,](./cloud-partner-portal-api-retrieve-operations.md) hogy egy `operationId` átad az API-t. A megszakítás általában szinkron művelet, azonban néhány összetett esetben új műveletre lehet szükség egy meglévő megszakításához. Ebben az esetben a HTTP-válasz törzs tartalmazza a művelet helyét, amelyet a lekérdezési állapothoz kell használni.

A kérelemmel megadhat egy vesszővel tagolt e-mail címlistát, és az API értesíti ezeket a címeket a művelet előrehaladásáról.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Név**    |      **Leírás**                                  |    **Adattípus**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  A közzétevő azonosítója, például:`contoso`         |   Sztring          |
| offerId      |  Ajánlat azonosítója                                     |   Sztring          |
| api-verzió  |  Az API jelenlegi verziója                               |    Dátum           |
|  |  |  |


<a name="header"></a>Fejléc
------

|  **Név**              |  **Érték**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Engedélyezés         |  Hordozója A TOKEN |
|  |  |


<a name="body-example"></a>Példa törzsre
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

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Válaszfejléc

|  **Név**             |    **Érték**                       |
|  ---------            |    ----------                      |
| Művelet-hely    | URL-cím, amely lekérdezhető a művelet aktuális állapotának meghatározásához. |
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

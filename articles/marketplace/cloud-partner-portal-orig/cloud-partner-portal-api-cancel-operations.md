---
title: Műveleti API megszakítása | Azure piactér
description: Művelet megszakítása.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 374425dbd2abacb2114b5792d7476bc341fa353a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819787"
---
# <a name="cancel-operation"></a>Művelet megszakítása 

Ez az API megszakítja az ajánlaton jelenleg folyamatban lévő műveletet. A [lekérési műveletek API](./cloud-partner-portal-api-retrieve-operations.md) -val beolvashatja az API-nak átadandó `operationId`. A megszakítás általában egy szinkron művelet, azonban bizonyos összetett forgatókönyvek esetén új műveletre lehet szükség a meglévő megszakítása érdekében. Ebben az esetben a HTTP-válasz törzse tartalmazza a művelet azon helyét, amelyet az állapot lekérdezéséhez kell használni.

Megadhatja a kérelemmel ellátott e-mail-címek vesszővel tagolt listáját, és az API értesíti ezeket a címeket a művelet előrehaladásáról.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**    |      **Leírás**                                  |    **Adattípus**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Közzétevő azonosítója, például `contoso`         |   Sztring          |
| OfferId      |  Ajánlat azonosítója                                     |   Sztring          |
| API-Version  |  Az API jelenlegi verziója                               |    Dátum           |
|  |  |  |


<a name="header"></a>Fejléc
------

|  **Name (Név)**              |  **Érték**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Engedélyezés         |  A TOKEN birtokosa |
|  |  |


<a name="body-example"></a>Példa szövegtörzsre
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

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Válaszfejléc

|  **Name (Név)**             |    **Érték**                       |
|  ---------            |    ----------                      |
| Művelet – hely    | URL-cím, amely a művelet aktuális állapotának meghatározására kérdezhető le. |
|  |  |


### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód**  |  **Leírás**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | oké. A rendszer sikeresen feldolgozta a kérést, és a művelet szinkron módon megszakítva. |
|  202      | Elfogadott. A rendszer sikeresen feldolgozta a kérést, és a művelet megszakítása folyamatban van. A visszavonási művelet helye a válasz fejlécében lesz visszaadva. |
|  400      | Helytelen vagy helytelenül formázott kérelem. A hiba-válasz törzse további információkat biztosíthat.  |
|  403      | Hozzáférés megtiltva. Az ügyfél nem fér hozzá a kérelemben megadott névtérhez. |
|  404      | Nem található. A megadott entitás nem létezik. |
|  |  |

---
title: Szakítsa meg a műveletet API |} Az Azure Marketplace-en
description: Művelet megszakítása
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 70ffd13be4ba934b423e3bb5344eea0a9c36886c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935551"
---
# <a name="cancel-operation"></a>A művelet megszakítása 

Ez az API megszakítja az ajánlatot a folyamatban lévő művelet. Használja a [beolvasni a műveleti API](./cloud-partner-portal-api-retrieve-operations.md) beolvasni egy `operationId` átadása az API-t. Megszakítás általában a egy szinkronizált művelet, azonban néhány összetett helyzetekben egy új művelet szükség lehet egy meglévő megszakítása. Ebben az esetben a HTTP-válasz törzsében tartalmaz állapotának lekérdezéséhez használt helyet a műveletet.

A kérelem hozzá lehet adni e-mail címeket vesszővel tagolt listáját, és az API-t értesíteni fogjuk ezeket a címeket, a művelet haladásával kapcsolatos.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**    |      **Leírás**                                  |    **Adattípus**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Közzétevő azonosítója, például `contoso`         |   String          |
| offerId      |  Ajánlat azonosítója                                     |   String          |
| api-version  |  API jelenlegi verziója                               |    Dátum           |
|  |  |  |


<a name="header"></a>Fejléc
------

|  **Name (Név)**              |  **Érték**         |
|  ---------             |  ----------        |
|  Content-Type          |  application/json  |
|  Engedélyezés         |  Tulajdonosi a JOGKIVONAT |
|  |  |


<a name="body-example"></a>Törzs példa
------------

### <a name="request"></a>Kérés

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Kérelem törzse tulajdonságai

|  **Name (Név)**                |  **Leírás**                                               |
|  --------                |  ---------------                                               |
|  Értesítés-e-mailek     | Vesszővel tagolt e-mailben értesíti a közzétételi művelet előrehaladását a azonosítóinak listája. |
|  |  |


### <a name="response"></a>Válasz

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Válaszfejléc

|  **Name (Név)**             |    **Érték**                       |
|  ---------            |    ----------                      |
| A művelet-helye    | URL-cím, amely lekérdezhetők, a művelet aktuális állapotának meghatározásához. |
|  |  |


### <a name="response-status-codes"></a>Állapotkódok

| **Kód**  |  **Leírás**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | oké. A kérés feldolgozása sikeresen megtörtént, és a művelet megszakadt, szinkron módon történik. |
|  202      | Elfogadva. A kérés feldolgozása sikeresen megtörtént, és a művelet folyamatban megszakítása folyamatban van. A válasz fejléce a megszakítási művelet helyét adja vissza. |
|  400      | Rossz vagy Excel-hibásan létrehozott kérelem. A hiba-válasz törzsében sikerült adhat meg további információt.  |
|  403      | Access Forbidden. Az ügyfél nem rendelkezik hozzáféréssel a kérésben megadott névtérrel. |
|  404      | Nem található. A megadott entitás nem létezik. |
|  |  |

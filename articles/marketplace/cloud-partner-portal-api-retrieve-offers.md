---
title: Ajánlatok beolvasása API – Azure Marketplace
description: API az ajánlatok összefoglaló listájának lekéréséhez a közzétevői névtérben.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: ee8d0c773a5ec4d362eae66e289838b9646247e6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85516239"
---
<a name="retrieve-offers"></a>Ajánlatok lekérése
===============

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a partneri központtal, és továbbra is működni fognak, miután az ajánlatokat áttelepítik a partner központba. Az integráció kis változásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](./cloud-partner-portal-api-overview.md) felsorolt módosításokat, hogy a kód továbbra is működni fog a partneri központba való Migrálás után.

Az ajánlatok összefoglaló listájának beolvasása a közzétevői névtérben.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-paraméterek
--------------

| **Name (Név)**         |  **Leírás**                         |  **Adattípus** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Közzétevő azonosítója, például:`contoso` |   Sztring    |
|  api-verzió     | Az API legújabb verziója                    |    Dátum        |
|  |  |


<a name="header"></a>Fejléc
------

|  **Name (Név)**        |         **Érték**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Engedélyezés   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Példa szövegtörzsre
------------

### <a name="response"></a>Válasz

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Válasz törzsének tulajdonságai

|  **Name (Név)**       |       **Leírás**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Az ajánlat típusát azonosítja.                                                                                           |
|  publisherId    | A közzétevőt egyedileg azonosító azonosító                                                                      |
|  status         | Az ajánlat állapota. A lehetséges értékek listájáért lásd az [ajánlat állapota](#offer-status) alább.                         |
|  id             | GUID, amely egyedileg azonosítja az ajánlatot a közzétevő névterében.                                                    |
|  version        | Az ajánlat aktuális verziója. Az ügyfél nem tudja módosítani a Version tulajdonságot. Minden közzététel után növekszik. |
|  definíció     | A munkaterhelés tényleges definíciójának összefoglaló nézetét tartalmazza. A részletes definíciók beszerzéséhez használja az [adott ajánlat beolvasása](./cloud-partner-portal-api-retrieve-specific-offer.md) API-t. |
|  changedTime    | Az ajánlat utolsó módosításának UTC-ideje                                                                              |
|  |  |


### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Code**  |  **Leírás**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`– A kérés feldolgozása sikeres volt, és a közzétevő alatt lévő összes ajánlat vissza lett küldve az ügyfélnek.  |
|  400      | `Bad/Malformed request`– A hiba-válasz törzse több információt is tartalmazhat.                                    |
|  403      | `Forbidden`– Az ügyfél nem fér hozzá a megadott névtérhez.                                          |
|  404      | `Not found`– A megadott entitás nem létezik.                                                                 |
|  |  |


### <a name="offer-status"></a>Ajánlat állapota

|  **Name (Név)**                    | **Leírás**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Az ajánlatot még soha nem tették közzé.                  |
|  NotStarted                  | Az ajánlat új, de nem indult el.                 |
|  WaitingForPublisherReview   | Az ajánlat a közzétevő jóváhagyására vár.         |
|  Fut                     | Az ajánlatok beküldése folyamatban van.             |
|  Sikeres                   | Az ajánlat küldése befejeződött a feldolgozás során.       |
|  Megszakítva                    | Az ajánlat beküldését megszakították.                   |
|  Sikertelen                      | Az ajánlat küldése nem sikerült.                         |
|  |  |

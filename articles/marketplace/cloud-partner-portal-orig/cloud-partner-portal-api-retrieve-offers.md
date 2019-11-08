---
title: Ajánlatok beolvasása API | Azure piactér
description: Az API lekérdezi az ajánlatok összegzett listáját a közzétevői névtérben.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5c94c03a63936be2b086085a1e52064dedf214b0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819614"
---
<a name="retrieve-offers"></a>Ajánlatok lekérése
===============

Az ajánlatok összefoglaló listájának beolvasása a közzétevői névtérben.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>URI-paraméterek
--------------

| **Name (Név)**         |  **Leírás**                         |  **Adattípus** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Közzétevő azonosítója, például `contoso` |   Sztring    |
|  API-Version     | Az API legújabb verziója                    |    Dátum        |
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
|  Definition     | A munkaterhelés tényleges definíciójának összefoglaló nézetét tartalmazza. A részletes definíciók beszerzéséhez használja az [adott ajánlat beolvasása](./cloud-partner-portal-api-retrieve-specific-offer.md) API-t. |
|  changedTime    | Az ajánlat utolsó módosításának UTC-ideje                                                                              |
|  |  |


### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód**  |  **Leírás**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` – a kérés feldolgozása sikeres volt, és a közzétevő alatt lévő összes ajánlat vissza lett küldve az ügyfélnek.  |
|  400      | `Bad/Malformed request` – a hiba-válasz törzse több információt is tartalmazhat.                                    |
|  403      | `Forbidden` – az ügyfél nem fér hozzá a megadott névtérhez.                                          |
|  404      | `Not found` – a megadott entitás nem létezik.                                                                 |
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
|  Meghiúsult                      | Az ajánlat küldése nem sikerült.                         |
|  |  |

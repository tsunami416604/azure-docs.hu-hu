---
title: Ajánlatok lekérése API | Azure Piactér
description: Az API lekéri a közzétevőnévtérben található ajánlatok összesített listáját.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 0413bc71c113bf1eef9f761dbeed4c0d0afe735c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255957"
---
<a name="retrieve-offers"></a>Ajánlatok lekérése
===============

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a Partnerközponttal, és az ajánlatok partnerközpontba való áttelepítése után is működni fognak. Az integráció kis változtatásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) felsorolt módosításokat, és győződjön meg arról, hogy a kód továbbra is működik a Partnerközpontba való áttelepítés után.

Lekéri az ajánlatok összesített listáját a közzétevő névterében.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-paraméterek
--------------

| **Név**         |  **Leírás**                         |  **Adattípus** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Közzétevő azonosítója, például`contoso` |   Sztring    |
|  api-verzió     | Az API legújabb verziója                    |    Dátum        |
|  |  |


<a name="header"></a>Fejléc
------

|  **Név**        |         **Érték**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Engedélyezés   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Példa törzsre
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

### <a name="response-body-properties"></a>Választörzs tulajdonságai

|  **Név**       |       **Leírás**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Az ajánlat típusát azonosítja                                                                                           |
|  publisherId    | A közzétevőt egyedileg azonosító azonosító                                                                      |
|  status         | Az ajánlat állapota. A lehetséges értékek listáját lásd alább [Az Ajánlat állapota.](#offer-status)                         |
|  id             | GUID, amely egyedileg azonosítja az ajánlatot a közzétevő névterében.                                                    |
|  version        | Az ajánlat aktuális verziója. A verziótulajdonságot az ügyfél nem tudja módosítani. Minden kiadás után növekszik. |
|  definíció     | A munkaterhelés tényleges definíciójának összesített nézetét tartalmazza. Részletes definíció beolvasásához használja az [adott ajánlat lekérése](./cloud-partner-portal-api-retrieve-specific-offer.md) API-t. |
|  módosítottIdő    | UTC-idő, amikor az ajánlatot utoljára módosították                                                                              |
|  |  |


### <a name="response-status-codes"></a>Válasz állapotkódjai

| **Kód**  |  **Leírás**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK`- A kérés feldolgozása sikeresen megtörtént, és a közzétevő alatt lévő összes ajánlat visszakerült az ügyfélhez.  |
|  400      | `Bad/Malformed request`- A hibaválasz törzse további információkat tartalmazhat.                                    |
|  403      | `Forbidden`- Az ügyfél nem fér hozzá a megadott névtérhez.                                          |
|  404      | `Not found`- A megadott entitás nem létezik.                                                                 |
|  |  |


### <a name="offer-status"></a>Ajánlat állapota

|  **Név**                    | **Leírás**                                  |
|  ------------------------    | -----------------------------------------------  |
|  Soha nem lett közzétéve              | Az ajánlatot soha nem tették közzé.                  |
|  Nincs beindítva                  | Az ajánlat új, de nem indult el.                 |
|  VárakozásA PublisherReview-ra   | Az ajánlat a közzétevő jóváhagyására vár.         |
|  Fut                     | Az ajánlat beküldése feldolgozása folyamatban van.             |
|  Sikeres                   | Az ajánlat beküldése befejeződött a feldolgozás.       |
|  Megszakítva                    | Az ajánlat beküldését törölték.                   |
|  Sikertelen                      | Az ajánlat beküldése nem sikerült.                         |
|  |  |

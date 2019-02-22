---
title: Lekérése ajánlatok API |} A Microsoft Docs
description: API összefoglaló listája alapján közzétevő névtér kérdezi le.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: de9261548ec79e206b0db87caabc1fa4c9ad6771
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56591550"
---
<a name="retrieve-offers"></a>Ajánlatok lekérése
===============

A közzétevő névtérben ajánlatok összesített listájának beolvasása.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>URI-paraméterek
--------------

| **Name (Név)**         |  **Leírás**                         |  **Adattípus** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Például a közzétevő azonosítója `contoso` |   String    |
|  API-verzió     | API legújabb verziója                    |    Dátum        |
|  |  |


<a name="header"></a>Fejléc
------

|  **Name (Név)**        |         **Érték**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Engedélyezés   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Törzs példa
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

### <a name="response-body-properties"></a>Válasz törzsében tulajdonságai

|  **Name (Név)**       |       **Leírás**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Az ajánlat típusát jelöli                                                                                           |
|  publisherId    | Amely egyedileg azonosítja a közzétevő azonosítója                                                                      |
|  status         | Az ajánlat állapotát. A lehetséges értékek listáját lásd: [stav Nabídky](#offer-status) alatt.                         |
|  id             | GUID azonosítója, amely egyedileg azonosítja az ajánlatot a kiadó névtérben.                                                    |
|  version        | Az ajánlat jelenlegi verziójával. A verzió tulajdonság nem lehet módosítani az ügyfél által. Azt minden egyes közzététel után. |
|  definíció     | A számítási feladatok tényleges definíciója összefoglaló nézetét tartalmazza. A részletes definíció lekéréséhez használja a [olvashatók be az adott ajánlat](./cloud-partner-portal-api-retrieve-specific-offer.md) API-t. |
|  changedTime    | Az ajánlat utolsó módosításának időpontja (UTC)                                                                              |
|  |  |


### <a name="response-status-codes"></a>Állapotkódok

| **Kód**  |  **Leírás**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -A kérés feldolgozása sikeresen megtörtént, és a közzétevő alapján az ajánlatok az ügyfélnek visszaadott.  |
|  400      | `Bad/Malformed request` – A hiba választörzs tartalmazhat további információkat.                                    |
|  403      | `Forbidden` – Az ügyfél nem rendelkezik hozzáféréssel a megadott névtér.                                          |
|  404      | `Not found` – A megadott entitás nem létezik.                                                                 |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Name (Név)**                    | **Leírás**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Az ajánlat soha nem lett közzétéve.                  |
|  Nincs elindítva                  | Az ajánlat új, de nem indult el.                 |
|  WaitingForPublisherReview   | Ajánlat közzétevője jóváhagyásra vár.         |
|  Fut                     | Az ajánlat küldésének feldolgozása folyamatban van.             |
|  Sikeres                   | Az ajánlat küldésének feldolgozása befejeződött.       |
|  Megszakítva                    | Az ajánlat küldésének meg lett szakítva.                   |
|  Meghiúsult                      | Az ajánlat elküldés meghiúsult.                         |
|  |  |

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
ms.openlocfilehash: cf6e2fa296d3d9f152edf22685b930586b4de524
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024812"
---
<a name="retrieve-offers"></a>Ajánlatok lekérése
===============

A közzétevő névtérben ajánlatok összesített listájának beolvasása.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>URI-paraméterek
--------------

| ** Neve **        |  **Leírás**                         |  **Adattípus** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Például a közzétevő azonosítója `contoso` |   Sztring    |
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

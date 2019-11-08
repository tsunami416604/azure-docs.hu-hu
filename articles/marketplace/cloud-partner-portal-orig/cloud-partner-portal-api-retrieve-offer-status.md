---
title: Ajánlat állapotának beolvasása | Azure piactér
description: Az API lekérdezi az ajánlat aktuális állapotát.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 5ce546d79497f462f6c262de738036d7e3a30226
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819660"
---
<a name="retrieve-offer-status"></a>Ajánlat állapotának lekérése 
=====================

Az ajánlat aktuális állapotának beolvasása.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**       |   **Leírás**                            |  **Adattípus** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Közzétevő azonosítója, például `Contoso`  |     Sztring     |
|  OfferId        | Az ajánlatot egyedileg azonosító GUID      |     Sztring     |
|  API-Version    | Az API legújabb verziója                        |     Dátum       |
|  |  |


<a name="header"></a>Fejléc
------

|  Név           |  Érték               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Engedélyezés  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Példa szövegtörzsre
------------

### <a name="response"></a>Válasz

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
      "notificationEmails": "jdoe@contoso.com"
  } 
```


### <a name="response-body-properties"></a>Válasz törzsének tulajdonságai

|  **Name (Név)**             |    **Leírás**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Az ajánlat állapota. A lehetséges értékek listájáért lásd az [ajánlat állapota](#offer-status) alább. |
|  üzenet             | Az ajánlattal társított üzenetek tömbje                                                    |
|  lépések                | Azon lépések tömbje, amelyek az ajánlat közzétételekor haladnak                      |
|  estimatedTimeFrame   | A lépés végrehajtásához szükséges idő becslése rövid formátumban                       |
|  id                   | A lépés azonosítója                                                                         |
|  stepName             | A lépés neve                                                                               |
|  leírás          | A lépés leírása                                                                        |
|  status               | A lépés állapota. A lehetséges értékek listájáért lásd az alábbi [lépések állapotát](#step-status) .    |
|  üzenet             | A lépéshez kapcsolódó üzenetek tömbje                                                          |
|  processPercentage    | A lépés százalékos készültsége                                                              |
|  previewLinks         | *Jelenleg nincs implementálva*                                                                    |
|  liveLinks            | *Jelenleg nincs implementálva*                                                                    |
|  notificationEmails   | Az e-mail-címek vesszővel tagolt listája, amelyekről értesíteni kell a művelet állapotát        |
|  |  |


### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód** |   **Leírás**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` – a kérés feldolgozása sikeres volt, és az ajánlat aktuális állapota vissza lett állítva. |
|  400     | `Bad/Malformed request` – a hiba-válasz törzse több információt is tartalmazhat.                 |
|  404     | `Not found` – a megadott entitás nem létezik.                                                |
|  |  |


### <a name="offer-status"></a>Ajánlat állapota

|  **Name (Név)**                    |    **Leírás**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Az ajánlatot még soha nem tették közzé.                          |
|  NotStarted                  | Az ajánlat új, és nem indult el.                            |
|  WaitingForPublisherReview   | Az ajánlat a közzétevő jóváhagyására vár.                 |
|  Fut                     | Az ajánlatok beküldése folyamatban van.                     |
|  Sikeres                   | Az ajánlat küldése befejeződött a feldolgozás során.               |
|  Megszakítva                    | Az ajánlat beküldését megszakították.                           |
|  Meghiúsult                      | Az ajánlat küldése nem sikerült.                                 |
|  |  |


### <a name="step-status"></a>Lépés állapota

|  **Name (Név)**                    |    **Leírás**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | A lépés nem indult el.                        |
|  Folyamatban                  | A lépés fut.                             |
|  WaitingForPublisherReview   | A lépés a közzétevő jóváhagyására vár.      |
|  WaitingForApproval          | A lépés a folyamat jóváhagyására vár.        |
|  Blokkolt                     | A lépés le van tiltva.                             |
|  Elutasította                    | A lépés elutasításra kerül.                            |
|  Befejezve                    | A lépés befejeződött.                            |
|  Megszakítva                    | A lépést megszakították.                           |
|  |  |

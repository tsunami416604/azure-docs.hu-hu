---
title: Ajánlat állapotának lekéréséhez |} A Microsoft Docs
description: API-t az ajánlat aktuális állapotát kérdezi le.
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
ms.openlocfilehash: 9233a5919ad86adcbb7947cd095945654ed015a7
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48810773"
---
<a name="retrieve-offer-status"></a>Ajánlat állapotának lekérése 
=====================

Az ajánlat aktuális állapotát kérdezi le.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**       |   **Leírás**                            |  **Adattípus** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Például a közzétevő azonosítója `Contoso`  |     Sztring     |
|  OfferId        | GUID, amely egyedileg azonosítja az ajánlatot      |     Sztring     |
|  API-verzió    | API legújabb verziója                        |     Dátum       |
|  |  |


<a name="header"></a>Fejléc
------

|  Name (Név)           |  Érték               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Engedélyezés  | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Törzs példa
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


### <a name="response-body-properties"></a>Válasz törzsében tulajdonságai

|  **Name (Név)**             |    **Leírás**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Az ajánlat állapota. A lehetséges értékek listáját lásd: [stav Nabídky](#offer-status) alatt. |
|  üzenet             | Az ajánlat társított üzenetek tömbje                                                    |
|  lépések                | A lépéseket, amelyek az ajánlat az ajánlat közzététele során halad végig tömbje                      |
|  estimatedTimeFrame   | Mennyi időt vesz igénybe, rövid formátumban lépés végrehajtásához becslése                       |
|  id                   | A lépés azonosítója                                                                         |
|  stepName             | A lépés neve                                                                               |
|  leírás          | A lépés leírása                                                                        |
|  status               | A lépés állapotát. A lehetséges értékek listáját lásd: [. lépés: állapot](#step-status) alatt.    |
|  üzenet             | A lépés kapcsolatos üzenetek tömbje                                                          |
|  processPercentage    | A lépés befejezése után készítsen százalékos aránya                                                              |
|  previewLinks         | *Jelenleg nincs implementálva*                                                                    |
|  liveLinks            | *Jelenleg nincs implementálva*                                                                    |
|  notificationEmails   | A műveletnek az előrehaladását értesítést e-mail-címek vesszővel elválasztott listája        |
|  |  |


### <a name="response-status-codes"></a>Állapotkódok

| **Kód** |   **Leírás**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` -A kérés feldolgozása sikeresen megtörtént, és az aktuális állapotát az ajánlat adott vissza. |
|  400     | `Bad/Malformed request` – A hiba választörzs tartalmazhat további információkat.                 |
|  404     | `Not found` – A megadott entitás nem létezik.                                                |
|  |  |


### <a name="offer-status"></a>Stav nabídky

|  **Name (Név)**                    |    **Leírás**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Az ajánlat soha nem lett közzétéve.                          |
|  Nincs elindítva                  | Ajánlatot csak az új, és nem indult el.                            |
|  WaitingForPublisherReview   | Ajánlat közzétevője jóváhagyásra vár.                 |
|  Fut                     | Az ajánlat küldésének feldolgozása folyamatban van.                     |
|  Sikeres                   | Az ajánlat küldésének feldolgozása befejeződött.               |
|  Megszakítva                    | Az ajánlat küldésének meg lett szakítva.                           |
|  Meghiúsult                      | Az ajánlat elküldés meghiúsult.                                 |
|  |  |


### <a name="step-status"></a>Lépés állapota

|  **Name (Név)**                    |    **Leírás**                           |
|  -------------------------   |  ------------------------------------------  |
|  Nincs elindítva                  | Lépés nem indult el.                        |
|  Folyamatban                  | Lépés futtatja.                             |
|  WaitingForPublisherReview   | Lépést közzétevő jóváhagyásra vár.      |
|  WaitingForApproval          | Lépés folyamat jóváhagyásra vár.        |
|  Letiltva                     | Lépés le van tiltva.                             |
|  Elutasítva                    | A lépésben a rendszer elutasítja.                            |
|  Befejezve                    | Lépés befejeződött.                            |
|  Megszakítva                    | Lépés meg lett szakítva.                           |
|  |  |

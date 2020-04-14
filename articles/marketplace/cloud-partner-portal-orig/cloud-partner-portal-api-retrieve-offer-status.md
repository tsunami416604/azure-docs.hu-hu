---
title: Ajánlat állapotának lekérése | Azure Piactér
description: Az API lekéri az ajánlat aktuális állapotát.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 9cf6ca27101a08ff58f32dcd31413256762490a2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255913"
---
# <a name="retrieve-offer-status"></a>Ajánlat állapotának lekérése

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a Partnerközponttal, és az ajánlatok partnerközpontba való áttelepítése után is működni fognak. Az integráció kis változtatásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) felsorolt módosításokat, és győződjön meg arról, hogy a kód továbbra is működik a Partnerközpontba való áttelepítés után.

Az ajánlat aktuális állapotának lekérése.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-paraméterek

|  **Név**       |   **Leírás**                            |  **Adattípus** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Közzétevő azonosítója, például`Contoso`  |     Sztring     |
|  offerId        | Az ajánlatot egyedileg azonosító GUID      |     Sztring     |
|  api-verzió    | Az API legújabb verziója                        |     Dátum       |
|  |  |


## <a name="header"></a>Fejléc


|  Név           |  Érték               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Engedélyezés  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Példa törzsre


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
  }
```


### <a name="response-body-properties"></a>Választörzs tulajdonságai

|  **Név**             |    **Leírás**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Az ajánlat állapota. A lehetséges értékek listáját lásd alább [Az Ajánlat állapota.](#offer-status) |
|  üzenet             | Az ajánlathoz társított üzenetek tömbje                                                    |
|  lépések                | Az ajánlat közzététele során megtett lépések tömbje                      |
|  becsültTimeFrame   | Becsült idő lenne igénybe, hogy ezt a lépést, barátságos formában                       |
|  id                   | A lépés azonosítója                                                                         |
|  lépésneve             | A lépés neve                                                                               |
|  leírás          | A lépés leírása                                                                        |
|  status               | A lépés állapota. A lehetséges értékek listáját lásd alább a [Lépés állapota](#step-status) című témakörben.    |
|  üzenet             | A lépéshez kapcsolódó üzenetek tömbje                                                          |
|  processPercentage    | A lépés százalékos készültsége                                                              |
|  previewLinks         | *Jelenleg nincs megvalósítva*                                                                    |
|  élő linkek            | *Jelenleg nincs megvalósítva*                                                                    |
|  értesítésEmailek   | A Partnerközpontba áttelepített ajánlatok elavultak. Az áttelepített ajánlatokra vonatkozó értesítési e-maileket a Fiókbeállítások Ban található Eladó elérhetőségi adatai alatt megadott e-mail-címre küldjük.<br><br>Nem áttelepített ajánlatok esetén a művelet előrehaladásáról értesítendő e-mail címek vesszővel elválasztott listája        |
|  |  |

### <a name="response-status-codes"></a>Válasz állapotkódjai

| **Kód** |   **Leírás**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK`- A kérés feldolgozása sikeresen megtörtént, és az ajánlat aktuális állapotát visszaadta. |
|  400     | `Bad/Malformed request`- A hibaválasz törzse további információkat tartalmazhat.                 |
|  404     | `Not found`- A megadott entitás nem létezik.                                                |
|  |  |

### <a name="offer-status"></a>Ajánlat állapota

|  **Név**                    |    **Leírás**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  Soha nem lett közzétéve              | Az ajánlatot soha nem tették közzé.                          |
|  Nincs beindítva                  | Az ajánlat új, és nem indult el.                            |
|  VárakozásA PublisherReview-ra   | Az ajánlat a közzétevő jóváhagyására vár.                 |
|  Fut                     | Az ajánlat beküldése feldolgozása folyamatban van.                     |
|  Sikeres                   | Az ajánlat beküldése befejeződött a feldolgozás.               |
|  Megszakítva                    | Az ajánlat beküldését törölték.                           |
|  Sikertelen                      | Az ajánlat beküldése nem sikerült.                                 |
|  |  |

### <a name="step-status"></a>Lépés állapota

|  **Név**                    |    **Leírás**                           |
|  -------------------------   |  ------------------------------------------  |
|  Nincs beindítva                  | A lépés még nem kezdődött el.                        |
|  Folyamatban                  | A lépés működik.                             |
|  VárakozásA PublisherReview-ra   | A lépés a közzétevő jóváhagyására vár.      |
|  WaitingForApproval (Jóváhagyásra vár)          | A lépés a folyamat jóváhagyására vár.        |
|  Blokkolva                     | A lépés blokkolva van.                             |
|  Elutasítva                    | A lépés elutasítva.                            |
|  Befejezve                    | A lépés befejeződött.                            |
|  Megszakítva                    | A lépés megszakadt.                           |
|  |  |

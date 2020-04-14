---
title: Műveletek beolvasása API | Azure Piactér
description: Lekéri az összes műveletet az ajánlatban, vagy egy adott művelet et a megadott operationId lekéri.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 93b2ca700a987b86aedfdae55d58540c8ffe84ed
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255872"
---
# <a name="retrieve-operations"></a>Műveletek lekérése

> [!NOTE]
> A Cloud Partner Portal API-k integrálva vannak a Partnerközponttal, és az ajánlatok partnerközpontba való áttelepítése után is működni fognak. Az integráció kis változtatásokat vezet be. Tekintse át a [Cloud Partner Portal API-hivatkozásban](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) felsorolt módosításokat, és győződjön meg arról, hogy a kód továbbra is működik a Partnerközpontba való áttelepítés után.

Lekéri az összes műveletet az ajánlatban, vagy egy adott művelet et a megadott operationId lekéri. Az ügyfél lekérdezési paramétereket használhat a futó műveletek szűréséhez.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


## <a name="uri-parameters"></a>URI-paraméterek

|  **Név**          |      **Leírás**                                                                                           | **Adattípus** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Közzétevő azonosítója, például`Contoso`                                                                   |  Sztring       |
|  offerId           |  Ajánlat azonosítója                                                                                              |  Sztring       |
|  operationId       |  GUID, amely egyedileg azonosítja a műveletet az ajánlatban. A operationId lehet beolvasni ezzel az API-val, és a válasz HTTP-fejlécében is megjelenik bármely hosszú ideig futó művelet, például a [közzétételi ajánlat](./cloud-partner-portal-api-publish-offer.md) API-t.  |   Guid   |
|  api-verzió       | Az API legújabb verziója |    Dátum      |
|  |  |  |

## <a name="header"></a>Fejléc


|  **Név**          |  **Érték**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Engedélyezés     | `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Példa törzsre

### <a name="response"></a>Válasz

#### <a name="get-operations"></a>GET műveletek

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>GET művelet

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
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
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
            }
        }
    ]
```

### <a name="response-body-properties"></a>Választörzs tulajdonságai

|  **Név**                    |  **Leírás**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | A műveletet egyedileg azonosító GUID                                                       |
|  benyújtástípusa              | Azonosítja az ajánlathoz jelentett művelet típusát, például`Publish/GoLive`      |
|  createdDateTime             | A művelet létrehozásának utc-dátuma                                                       |
|  lastActionDateTime          | UTC dátumidő, amikor az utolsó frissítés történt a művelet                                       |
|  status                      | A művelet állapota `not started` \| `running` \| `failed` \| `completed`vagy . Egyszerre csak egy `running` művelet nek lehet állapota. |
|  error                       | Sikertelen műveletekre vonatkozó hibaüzenet                                                               |
|  |  |

### <a name="response-step-properties"></a>Válaszlépés tulajdonságai

|  **Név**                    |  **Leírás**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| becsültTimeFrame | A művelet becsült időtartama |
| id | A lépésfolyamat egyedi azonosítója |
| leírás | A lépés leírása |
| lépésneve | A lépés rövid neve |
| status | A lépés állapota vagy `notStarted` \| `running` \| `failed` \|`completed` |
| üzenet | A lépés során észlelt értesítések és figyelmeztetések. Karakterláncok tömbje |
| progressSzázalék | 0 és 100 között a lépés előrehaladását jelző egész szám |
| | |

### <a name="response-status-codes"></a>Válasz állapotkódjai

| **Kód**  |   **Leírás**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK`- A kérés feldolgozása sikeresen megtörtént, és a kért művelet(ek) visszalett küldve.        |
|  400      | `Bad/Malformed request`- A hibaválasz törzse további információkat tartalmazhat.                    |
|  403      | `Forbidden`- Az ügyfél nem fér hozzá a megadott névtérhez.                          |
|  404      | `Not found`- A megadott entitás nem létezik.                                                 |
|  |  |

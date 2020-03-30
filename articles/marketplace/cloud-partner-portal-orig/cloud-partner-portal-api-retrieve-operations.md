---
title: Műveletek beolvasása API | Azure Piactér
description: Lekéri az összes műveletet az ajánlatban, vagy egy adott művelet et a megadott operationId lekéri.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 4fc77407ae1c5854d3fe977da5a81f4226bf5305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280473"
---
<a name="retrieve-operations"></a>Műveletek lekérése
===================

Lekéri az összes műveletet az ajánlatban, vagy egy adott művelet et a megadott operationId lekéri. Az ügyfél lekérdezési paramétereket használhat a futó műveletek szűréséhez.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Név**          |      **Leírás**                                                                                           | **Adattípus** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Közzétevő azonosítója, például`Contoso`                                                                   |  Sztring       |
|  offerId           |  Ajánlat azonosítója                                                                                              |  Sztring       |
|  operationId       |  GUID, amely egyedileg azonosítja a műveletet az ajánlatban. A operationId lehet beolvasni ezzel az API-val, és a válasz HTTP-fejlécében is megjelenik bármely hosszú ideig futó művelet, például a [közzétételi ajánlat](./cloud-partner-portal-api-publish-offer.md) API-t.  |   Guid   |
|  filteredStatus    | Nem kötelező lekérdezési paraméter, amely `running`az API által visszaadott gyűjtemény állapot (például ) szerinti szűréshez használható.  |   Sztring |
|  api-verzió       | Az API legújabb verziója                                                                                           |    Dátum      |
|  |  |  |


<a name="header"></a>Fejléc
------

|  **Név**          |  **Érték**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Engedélyezés     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Példa törzsre
------------

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
                "notificationEmails": "jondoe@contoso.com"
            } 
        }
    ]
```


### <a name="response-body-properties"></a>Választörzs tulajdonságai

|  **Név**                    |  **Leírás**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | A műveletet egyedileg azonosító GUID                                                       |
|  benyújtástípusa              | Azonosítja az ajánlathoz jelentett művelet típusát, például`Publish/GGoLive`      |
|  createdDateTime             | A művelet létrehozásának utc-dátuma                                                       |
|  lastActionDateTime          | UTC dátumidő, amikor az utolsó frissítés történt a művelet                                       |
|  status                      | A művelet állapota `not started` \| `running` \| `failed` \| `completed`vagy . Egyszerre csak egy `running` művelet nek lehet állapota. |
|  error                       | Sikertelen műveletekre vonatkozó hibaüzenet                                                               |
|  |  |


### <a name="response-status-codes"></a>Válasz állapotkódjai

| **Kód**  |   **Leírás**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK`- A kérés feldolgozása sikeresen megtörtént, és a kért művelet(ek) visszalett küldve.        |
|  400      | `Bad/Malformed request`- A hibaválasz törzse további információkat tartalmazhat.                    |
|  403      | `Forbidden`- Az ügyfél nem fér hozzá a megadott névtérhez.                          |
|  404      | `Not found`- A megadott entitás nem létezik.                                                 |
|  |  |

---
title: Operations API beolvasása | Azure piactér
description: Az ajánlat összes műveletének lekérése, vagy egy adott művelet beolvasása a megadott operationId.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: c3eb77744d61322ca0aed20bb2b3f486cc02ac70
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819599"
---
<a name="retrieve-operations"></a>Műveletek lekérése
===================

Az ajánlat összes műveletének lekérése, vagy egy adott művelet beolvasása a megadott operationId. Előfordulhat, hogy az ügyfél lekérdezési paramétereket használ a futó műveletek szűréséhez.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**          |      **Leírás**                                                                                           | **Adattípus** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Közzétevő azonosítója, például `Contoso`                                                                   |  Sztring       |
|  OfferId           |  Ajánlat azonosítója                                                                                              |  Sztring       |
|  operationId       |  GUID, amely egyedileg azonosítja a műveletet az ajánlaton. Lehetséges, hogy a operationId ezt az API-t használja, és a válasz HTTP-fejlécében is visszakerül a hosszú ideig futó műveletre, például a [közzétételi ajánlat](./cloud-partner-portal-api-publish-offer.md) API-ra.  |   GUID   |
|  filteredStatus    | Az API által visszaadott gyűjtemény állapot alapján történő szűrésére szolgáló opcionális lekérdezési paraméter (például `running`).  |   Sztring |
|  API-Version       | Az API legújabb verziója                                                                                           |    Dátum      |
|  |  |  |


<a name="header"></a>Fejléc
------

|  **Name (Név)**          |  **Érték**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Engedélyezés     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Példa szövegtörzsre
------------

### <a name="response"></a>Válasz

#### <a name="get-operations"></a>Műveletek beolvasása

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

#### <a name="get-operation"></a>Művelet beolvasása

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


### <a name="response-body-properties"></a>Válasz törzsének tulajdonságai

|  **Name (Név)**                    |  **Leírás**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | A műveletet egyedileg azonosító GUID                                                       |
|  submissionType              | Meghatározza az ajánlathoz jelentett művelet típusát, például `Publish/GGoLive`      |
|  createdDateTime             | UTC datetime a művelet létrehozásakor                                                       |
|  lastActionDateTime          | UTC dátum és idő, amikor a legutóbbi frissítés befejeződött a műveletnél                                       |
|  status                      | A művelet állapota vagy `not started` \| `running` \| `failed` \| `completed`. Egyszerre csak egy művelet rendelkezhet `running` állapottal. |
|  error                       | Sikertelen műveletek hibaüzenete                                                               |
|  |  |


### <a name="response-status-codes"></a>Válasz-állapotkódok

| **Kód**  |   **Leírás**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` – a kérés feldolgozása sikeres volt, és a kért művelet (ek) vissza lettek küldve.        |
|  400      | `Bad/Malformed request` – a hiba-válasz törzse több információt is tartalmazhat.                    |
|  403      | `Forbidden` – az ügyfél nem fér hozzá a megadott névtérhez.                          |
|  404      | `Not found` – a megadott entitás nem létezik.                                                 |
|  |  |

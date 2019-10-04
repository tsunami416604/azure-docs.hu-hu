---
title: Műveleti API beolvasása |} Az Azure Marketplace-en
description: 'Olvassa be az ajánlat vagy lekérni egy adott művelet a megadott műveletazonosító: az összes műveletet.'
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 1fbcc1d50dbc4488c4123be64e85de612233ccc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935779"
---
<a name="retrieve-operations"></a>Műveletek lekérése
===================

Olvassa be az ajánlat vagy lekérni egy adott művelet a megadott műveletazonosító: az összes műveletet. Az ügyfél lekérdezési paraméterek segítségével szűrheti a futó műveletek.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI-paraméterek
--------------

|  **Name (Név)**          |      **Leírás**                                                                                           | **Adattípus** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Például a közzétevő azonosítója `Contoso`                                                                   |  String       |
|  offerId           |  Ajánlat azonosítója                                                                                              |  String       |
|  operationId       |  GUID azonosítója, amely egyedileg azonosítja az ajánlatot a műveletet. Az operationId lehet beolvasni az API-val, és is adja vissza minden olyan hosszú ideig futó művelet esetén a válasz HTTP-fejléchez például a [közzététel ajánlat](./cloud-partner-portal-api-publish-offer.md) API-t.  |   Guid   |
|  filteredStatus    | Szűrés állapot szerint használt opcionális lekérdezési paraméter (például `running`) az API által visszaadott a gyűjteményen.  |   String |
|  api-version       | API legújabb verziója                                                                                           |    Dátum      |
|  |  |  |


<a name="header"></a>Fejléc
------

|  **Name (Név)**          |  **Érték**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Engedélyezés     | `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Törzs példa
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


### <a name="response-body-properties"></a>Válasz törzsében tulajdonságai

|  **Name (Név)**                    |  **Leírás**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | GUID, amely egyedileg azonosítja a művelet                                                       |
|  submissionType              | Az ajánlat például jelentett művelet típusa `Publish/GGoLive`      |
|  createdDateTime             | A művelet létrehozásakor, a dátum-idő (UTC)                                                       |
|  lastActionDateTime          | A művelet végeztével a rendszer az utolsó frissítés, a dátum-idő (UTC)                                       |
|  status                      | A művelet állapotának vagy `not started` \| `running` \| `failed` \| `completed`. Csak egy művelet lehet állapot `running` egyszerre. |
|  error                       | Sikertelen műveletek hibaüzenet                                                               |
|  |  |


### <a name="response-status-codes"></a>Állapotkódok

| **Kód**  |   **Leírás**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` -A kérés feldolgozása sikerült, és a kért lett(ek) adott vissza.        |
|  400      | `Bad/Malformed request` – A hiba választörzs tartalmazhat további információkat.                    |
|  403      | `Forbidden` – Az ügyfél nem rendelkezik hozzáféréssel a megadott névtér.                          |
|  404      | `Not found` – A megadott entitás nem létezik.                                                 |
|  |  |

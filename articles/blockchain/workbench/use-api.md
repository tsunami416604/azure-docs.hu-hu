---
title: Az Azure Blockchain Workbench REST API-jának használata
description: Az Azure Blockchain Workbench REST API-jának használatát bemutató forgatókönyvek
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/14/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: c1a9b526f08f330d62c30dd1d676e95460aee6c2
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51712347"
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Az Azure Blockchain Workbench REST API-jának használata

Az Azure Blockchain Workbench REST API-jával a fejlesztők és az infomunkások gazdag integrációs megoldásokat hozhatnak létre a blokkláncalkalmazásokkal. Ez a dokumentum bemutatja, hogy hogyan használhatja a Workbench REST API-jának számos fő metódusát. Tegyük fel például, egy fejlesztői szeretne létrehozni az egyéni blockchain ügyfelet. A blockchain-ügyfél lehetővé teszi, hogy a bejelentkezett felhasználók megtekinthetik és használhatják a saját hozzárendelt blockchain-alkalmazások. Az ügyféllel a felhasználók megtekinthetik a szerződéspéldányokat, és műveleteket végezhetnek az intelligens szerződéseken. Az ügyfél a bejelentkezett felhasználó környezetében a Workbench REST API használatával a következő műveleteket hajthatja végre:

* Alkalmazások listázása
* Az egyes alkalmazások munkafolyamatainak listázása
* Az egyes munkafolyamatok intelligensszerződés-példányainak listázása
* Az egyes szerződések elérhető műveleteinek listázása
* Az egyes szerződések egyes műveleteinek végrehajtása

A forgatókönyvekben használt példa blockchain-alkalmazások is lehetnek [letölti a githubról](https://github.com/Azure-Samples/blockchain).

## <a name="list-applications"></a>Alkalmazások listázása

Miután a felhasználó már bejelentkezett a blockchain-ügyfél, az első tevékenység lekérni a felhasználó összes Blockchain Workbench alkalmazást. Ebben a példában a felhasználó két alkalmazáshoz tud hozzáférni:

1. [Eszköz átvitele](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Hűtött szállítása](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

Használja az [Applications GET API-t](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

A választ, amelyhez a felhasználónak hozzáférése van a Blockchain Workbenchet összes blockchain-alkalmazásokat sorolja fel. Blockchain Workbench használatával a rendszergazdák minden blockchain-application beolvasása. Nem-munkaterület a rendszergazdák rendelkeznek legalább egy társított alkalmazásban vagy egy társított intelligens szerződés példány szerepkör összes blokkláncok beolvasása.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Az egyes alkalmazások munkafolyamatainak listázása

Miután a felhasználó kiválasztja a megfelelő blockchain-alkalmazások (például **eszköz Transfer**), a blockchain-ügyfél lekéri a blockchain adott alkalmazás összes munkafolyamatot. A felhasználók ezután kiválaszthatják a megfelelő munkafolyamatot, majd láthatják az adott munkafolyamathoz tartozó összes intelligensszerződés-példányt. Minden egyes blokklánc-alkalmazás rendelkezik egy vagy több munkafolyamattal, és minden munkafolyamathoz tartozik nulla vagy több intelligensszerződés-példány. A blockchain ügyfélalkalmazáshoz, amely csak egy munkafolyamat rendelkezik javasoljuk, hogy a rendszer kihagyja a felhasználói élmény folyamatot, amely lehetővé teszi a felhasználóknak, hogy a megfelelő munkafolyamat. Ebben az esetben **eszköz Transfer** csak egy munkafolyamatot, más néven rendelkezik **eszköz Transfer**.

Használja az [Applications Workflows GET API-t](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

A válasz felsorolja az adott blokkláncalkalmazás összes olyan munkafolyamatát, amelyhez a felhasználó hozzáfér a Blockchain Workbenchben. Blockchain Workbench használatával a rendszergazdák minden blockchain munkafolyamat lekérése. Nem-munkaterület a rendszergazdák kaphat minden olyan munkafolyamatot, amelynek van legalább egy társított alkalmazásban szerepkör, vagy egy intelligens szerződés példány szerepkörhöz társítva.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Az egyes munkafolyamatok intelligensszerződés-példányainak listázása

Miután egy felhasználó kijelöli a alkalmazni, ebben az esetben munkafolyamat **eszköz Transfer**, intelligens szerződés valamennyi példánya a megadott munkafolyamat lekéri a blockchain-ügyfél. Ezen információk használatával intelligens szerződés valamennyi példánya a munkafolyamat megjelenítése. Vagy a felhasználók előadásán engedélyezheti azokat a megjelenített intelligens szerződés példányra. A jelen példában tegyük fel, hogy egy felhasználó szeretne műveleteket végezni az intelligensszerződés-példányok egyikével.

Használja a [Contracts GET API-t](/rest/api/azure-blockchain-workbench/contractsv2/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

A válasz felsorolja az adott munkafolyamathoz tartozó összes intelligensszerződés-példányt. Workbench-rendszergazdák példányainak intelligens szerződés beolvasása. Nem-munkaterület a rendszergazdák kaphat minden intelligens szerződés példányt, amelynek van legalább egy társított alkalmazásban szerepkör, vagy egy intelligens szerződés példány szerepkörhöz társítva.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Az egyes szerződések elérhető műveleteinek listázása

Miután egy felhasználó úgy dönt, hogy részletes ismertetése a szerződés, a blockchain-ügyfél ezután megjelenítheti a rendelkezésre álló felhasználói műveleteket a szerződés állapota. Ebben a példában a felhasználó megtekinti egy frissen létrehozott intelligens szerződés összes elérhető műveletét:

* Módosítás: A felhasználó módosíthatja egy objektum leírását és árát.
* Leállítása: Lehetővé teszi, hogy a felhasználó az eszköz a szerződés befejezéséhez.

Használja a [Contract Action GET API-t](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

A válasz felsorolja az összes olyan műveletet, amelyhez a felhasználó hozzáférhet az adott intelligensszerződés-példány aktuális állapotában. A felhasználók megkapják az összes elérhető műveletet, ha rendelkeznek alkalmazás-szerepkörrel vagy intelligensszerződés-példányi szerepkörrel az adott intelligensszerződés-példány aktuális állapotához.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Az egyes szerződések egyes műveleteinek végrehajtása

A felhasználó ezután eldöntheti, hogy milyen műveletet kíván végezni az adott intelligensszerződés-példányon. Ebben az esetben fontolja meg a forgatókönyvet, ahol a felhasználó módosítani szeretné a leírását és árát az eszköz a következő műveletet:

* Leírás: „Az én frissített autóm”
* Ár: 543210

Használja a [Contract Action POST API-t](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost):

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

A felhasználók csak olyan műveleteket tudnak végrehajtani, amelyhez rendelkeznek alkalmazás-szerepkörrel vagy intelligensszerződés-példányi szerepkörrel az adott intelligensszerződés-példány aktuális állapotára vonatkozóan. Ha a POST művelet sikeres, HTTP 200 OK választ fog kapni választörzs nélkül.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain Workbench REST API leírása](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)
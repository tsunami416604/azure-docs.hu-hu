---
title: Az Azure Blockchain Workbench REST API-jának használata
description: Az Azure Blockchain Workbench REST API-jának használatát bemutató forgatókönyvek
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: cec2ab862a34a8753601dfeef3081ae9e9ca9fd9
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Az Azure Blockchain Workbench REST API-jának használata 

Az Azure Blockchain Workbench REST API-jával a fejlesztők és az infomunkások gazdag integrációs megoldásokat hozhatnak létre a blokkláncalkalmazásokkal. Ez a dokumentum bemutatja, hogy hogyan használhatja a Workbench REST API-jának számos fő metódusát. Vizsgáljunk meg egy olyan példát, amelyben egy fejlesztő szeretne egy egyéni blokkláncügyfelet létrehozni, amely lehetővé teszi a bejelentkezett felhasználóknak a hozzájuk rendelt blokklánc-alkalmazások megtekintését és használatát. Az ügyféllel a felhasználók megtekinthetik a szerződéspéldányokat, és műveleteket végezhetnek az intelligens szerződéseken. Az ügyfél a Workbench REST API-ját használja a bejelentkezett felhasználó biztonsági kontextusában az alábbi műveletekhez:

* Alkalmazások listázása
* Az egyes alkalmazások munkafolyamatainak listázása
* Az egyes munkafolyamatok intelligensszerződés-példányainak listázása
* Az egyes szerződések elérhető műveleteinek listázása
* Az egyes szerződések egyes műveleteinek végrehajtása

## <a name="list-applications"></a>Alkalmazások listázása

Miután egy felhasználó bejelentkezett a blokkláncügyfélbe, az első feladat a felhasználóhoz tartozó összes Blockchain Workbenchbeli blokkláncalkalmazás beolvasása. Ebben a példában a felhasználó két alkalmazáshoz tud hozzáférni:

1.  Objektumok átvitele
2.  Hűtött szállítás

Használja az [Applications GET API-t](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications 
Authorization : Bearer {access token}
```

A válasz felsorolja az összes olyan blokkláncalkalmazást, amelyhez a felhasználó hozzáfér a Blockchain Workbenchben. A Blockchain Workbench-rendszergazdák megkapják az összes blokkláncalkalmazást, a nem rendszergazda felhasználók pedig azokat a blokkláncalkalmazásokat kapják meg, amelyekhez legalább egy alkalmazás-szerepkörrel vagy intelligensszerződés-példányi szerepkörrel rendelkeznek.

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

Miután egy felhasználó kiválasztja a megfelelő blokklánc-alkalmazást, jelen esetben az „Objektumok átvitele” alkalmazást, a blokkláncügyfél lekéri az adott blokkláncalkalmazáshoz tartozó összes munkafolyamatot. A felhasználók ezután kiválaszthatják a megfelelő munkafolyamatot, majd láthatják az adott munkafolyamathoz tartozó összes intelligensszerződés-példányt. Minden egyes blokklánc-alkalmazás rendelkezik egy vagy több munkafolyamattal, és minden munkafolyamathoz tartozik nulla vagy több intelligensszerződés-példány. A blokklánc-ügyfélalkalmazások létrehozásakor ajánlott kihagyni azt a lépést, amelynek során a felhasználó kiválaszthatja a munkafolyamatot, ha csak egyetlen munkafolyamat található az adott blokkláncalkalmazásban. Jelen esetben az Objektumok átvitele alkalmazás csak egy munkafolyamattal rendelkezik, melynek neve szintén „Objektumok átvitele”.

Használja az [Applications Workflows GET API-t](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

A válasz felsorolja az adott blokkláncalkalmazás összes olyan munkafolyamatát, amelyhez a felhasználó hozzáfér a Blockchain Workbenchben. A Blockchain Workbench-rendszergazdák megkapják az összes blokklánc-munkafolyamatot, a nem rendszergazda felhasználók pedig azokat a munkafolyamatokat kapják meg, amelyekhez legalább egy alkalmazás-szerepkörrel vagy intelligensszerződés-példányi szerepkörrel rendelkeznek.

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

Miután egy felhasználó kiválasztja a megfelelő munkafolyamatot, jelen esetben az „Objektumok átvitele” munkafolyamatot, a blokkláncügyfél lekéri az adott munkafolyamathoz tartozó összes intelligensszerződés-példányt. Ezen információ segítségével megjelenítheti a munkafolyamat összes intelligensszerződés-példányát, és lehetővé teheti a felhasználónak, hogy lehatoljon a megjelenített intelligensszerződés-példányok bármelyikébe. A jelen példában tegyük fel, hogy egy felhasználó szeretne műveleteket végezni az intelligensszerződés-példányok egyikével.

Használja a [Contracts GET API-t](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

A válasz felsorolja az adott munkafolyamathoz tartozó összes intelligensszerződés-példányt. A Workbench-rendszergazdák megkapják az összes intelligensszerződés-példányt, a nem rendszergazda felhasználók pedig azokat az intelligensszerződés-példányokat kapják meg, amelyekhez legalább egy alkalmazás-szerepkörrel vagy intelligensszerződés-példányi szerepkörrel rendelkeznek.

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

Miután a felhasználó eldöntötte, hogy szeretne lehatolni a szerződések egyikébe, a blokkláncügyfél megjelenítheti a felhasználónak elérhető összes műveletet a szerződés aktuális állapotának függvényében. Ebben a példában a felhasználó megtekinti egy frissen létrehozott intelligens szerződés összes elérhető műveletét:

* Módosítás: A felhasználó módosíthatja egy objektum leírását és árát.
* Befejezés: A felhasználó befejezheti az objektum szerződését.

Használja a [Contract Action GET API-t](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionget):

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

A felhasználó ezután eldöntheti, hogy milyen műveletet kíván végezni az adott intelligensszerződés-példányon. A jelen példában tegyük fel, hogy a felhasználó szeretné módosítani egy objektum leírását és árát a következőre:

* Leírás: „Az én frissített autóm”
* Ár: 543210

Használja a [Contract Action POST API-t](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionpost):

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
---
title: Az Azure Blockchain munkaterület REST API használatával
description: Az Azure Blockchain munkaterület REST API használatával forgatókönyvei
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 27ed94b3ce14c57e369b0c80d4c53b72a5ae40a8
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Az Azure Blockchain munkaterület REST API használatával 

Az Azure Blockchain munkaterület REST API módszert kínál a fejlesztők és az információkkal dolgozó szakemberek blockchain alkalmazások gazdag integrációja felépítéséhez. Ez a dokumentum végigvezeti a munkaterületet üzemeltető REST API fő több különböző módszert. Fontolja meg egy olyan forgatókönyvet, ahol a fejlesztő szeretne létrehozni egy egyéni blockchain ügyfél, amely lehetővé teszi, hogy a bejelentkezett felhasználók megtekintéséhez és interaktív használatához a hozzárendelt blockchain alkalmazásokkal. Az ügyfél lehetővé teszi, hogy a szerződés példányok megtekintése és műveletek végrehajtása intelligens szerződések a felhasználók számára. Az ügyfél a bejelentkezett felhasználó környezetében a munkaterületet üzemeltető REST API használatával tegye a következőket:

* Alkalmazások listája
* Az alkalmazás munkafolyamatok
* Intelligens szerződés kilistázza a munkafolyamat
* Lista elérhető műveletek egy szerződés
* A szerződés egy művelet végrehajtása

## <a name="list-applications"></a>Alkalmazások listája

Ha egy felhasználó be a blockchain ügyfél aláírta, első lépése a program összes Blockchain munkaterület blockchain alkalmazások a felhasználó számára. Ebben a forgatókönyvben a felhasználó rendelkezik a két alkalmazás eléréséhez:

1.  Eszköz átvitele
2.  Hűtött szállítására

Használja a [alkalmazások GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget):

``` http
GET /api/v1/applications 
Authorization : Bearer {access token}
```

Válasza ki a felhasználó számára elérhető Blockchain munkaterület minden blockchain alkalmazások sorolja fel. Blockchain munkaterület a rendszergazdák összes blockchain alkalmazásra, get, nem munkaterület a rendszergazdák legalább egy társított alkalmazásban szerepkör vagy egy társított intelligens szerződés példány szerepkör amelyeknél minden blockchains beolvasása közben.

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

## <a name="list-workflows-for-an-application"></a>Az alkalmazás munkafolyamatok

Miután a felhasználó kiválasztja az alkalmazandó blockchain, ebben az esetben eszköz átvitele, a a blockchain ügyfél letölti az adott blockchain alkalmazás összes munkafolyamat. Felhasználó kiválaszthatja a megfelelő munkafolyamat előtt az összes intelligens szerződés példányát munkafolyamat látható. Összes blockchain alkalmazás rendelkezik egy vagy több munkafolyamatra, és minden munkafolyamat rendelkezik nulla, vagy intelligens szerződés példányok. Fejlesztéskor blockchain ügyfélalkalmazások, javasoljuk, hogy hagyja ki a felhasználói élmény folyamat, így a felhasználók csak egy munkafolyamat blockchain alkalmazása esetén, válassza ki a megfelelő munkafolyamat. Ebben az esetben csak egy munkafolyamat, más néven az eszköz átviteli eszköz átviteli rendelkezik.

Használja a [alkalmazások munkafolyamatok GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget):

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

Válasza ki a felhasználó számára elérhető Blockchain munkaterület megadott blockchain alkalmazás összes munkafolyamat sorolja fel. Blockchain munkaterület a rendszergazdák beolvasása blockchain minden munkafolyamathoz, amíg nem munkaterület a rendszergazdák beolvasása minden olyan munkafolyamatot, amelyeknél az ahhoz kapcsolódó alkalmazás legalább egy szerepkör vagy egy intelligens szerződés példány szerepkörhöz társítva.

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

## <a name="list-smart-contract-instances-for-a-workflow"></a>Intelligens szerződés kilistázza a munkafolyamat

Miután a felhasználó megadja az alkalmazandó munkafolyamat, a nagybetűk eszköz átvitele, a blockchain ügyfél a megadott munkafolyamat példányainak intelligens szerződés kér le. Ezek az információk segítségével a munkafolyamat intelligens szerződés példányainak megjelenítése és engedélyezése a felhasználók számára részletes bemutatója a megjelenített intelligens szerződés példányra való. Ebben a példában fontolja meg, a felhasználó szeretne kommunikálni beavatkozásra intelligens szerződés példányai közül.

Használja a [GET API szerződések](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractsget):

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

Válasz a megadott munkafolyamat minden intelligens szerződés példányát sorolja fel. Munkaterület a rendszergazdák az összes intelligens adategyezmény-példányokat beszerezni, nem munkaterület a rendszergazdák beolvasása közben intelligens szerződés példányok, amelyeknél az ahhoz kapcsolódó alkalmazás legalább egy szerepkör vagy egy intelligens szerződés példány szerepkörhöz társítva.

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

## <a name="list-available-actions-for-a-contract"></a>Lista elérhető műveletek egy szerződés

A felhasználó határozott, hogy részletes bemutatója a szerződések egy, a blockchain ügyfél is majd megjelenni minden elérhető művelet a felhasználó a szerződés állapota. Ebben a példában a felhasználó egy új intelligens szerződés hozza létre őket minden elérhető művelet keresi:

* Módosítsa: Lehetővé teszi, hogy a felhasználó módosíthatja a leírást és az ár egy eszköz számára.
* Leáll: A felhasználó az eszköz a szerződés leáll.

Használja a [szerződést kötöttek művelet GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionget):

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

Válasz felsorolja az összes műveleteinek, amelyhez a felhasználó a megadott intelligens szerződés példány aktuális állapota. A felhasználók megkaphatják összes megfelelő műveleteket, ha a felhasználó egy társított alkalmazásban szerepkör vagy a jelenlegi állapotát a megadott intelligens szerződés példány egy intelligens szerződés példány szerepkörhöz társítva.

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

## <a name="execute-an-action-for-a-contract"></a>A szerződés egy művelet végrehajtása

A felhasználó majd dönt, hogy hajtsa végre a műveletet a megadott intelligens adategyezmény-példányhoz. Ebben az esetben fontolja meg a forgatókönyvet, ahol a felhasználó szeretné módosíthatja a leírást és az ár egy eszköz a következő:

* Leírás: "a frissített autó"
* Árlista: 54321

Használja a [szerződést kötöttek a FELADÁS egy vagy több API művelet](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contracts/contractactionpost):

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

Felhasználók csak képesek végrehajtani a műveletet a megadott intelligens adategyezmény-példány és a szerepkörhöz társított alkalmazást, vagy intelligens szerződés példány szerepkör aktuális állapota. Ha a feladás egy vagy több sikeres, egy HTTP 200 OK válasz választörzs visszaadva.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>További lépések

* [Az Azure Blockchain munkaterület REST API-referencia](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)
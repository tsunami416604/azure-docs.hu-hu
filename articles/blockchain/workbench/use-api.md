---
title: Az Azure Blockchain Workbench REST API-k használata
description: Az Azure Blockchain Workbench Preview REST API-val kapcsolatos forgatókönyvek
ms.date: 03/05/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 3084fcf343bc42fe01bf352b6791916d62f63540
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672747"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Az Azure Blockchain Workbench Preview REST API használata

Az Azure Blockchain Workbench Preview REST API a fejlesztők és az informatikai dolgozók számára lehetővé teszi a blockchain alkalmazások gazdag integrációjának kialakítását. Ez a cikk a Workbench REST API használatával kapcsolatos számos forgatókönyvet ismertet. Tegyük fel például, hogy létre szeretne hozni egy egyéni blokklánc-ügyfelet, amely lehetővé teszi a bejelentkezett felhasználók számára a hozzárendelt blokklánc-alkalmazások megtekintését és interakcióját. Az ügyfél használhatja a Blockchain Workbench API-t a szerződéspéldányok megtekintéséhez és az intelligens szerződések műveletekhez.

## <a name="blockchain-workbench-api-endpoint"></a>Blockchain Workbench API-végpont

A Blockchain Workbench API-k egy végponton keresztül érhetők el a központi telepítéshez. Az API-végpont URL-címének beszereznie a központi telepítéshez:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A bal oldali navigációs ablakban válassza az **Erőforráscsoportok**lehetőséget.
1. Válassza ki az erőforráscsoport nevét a telepített Blockchain Workbench.
1. A **LISTA** betűszerinti rendezéséhez jelölje ki a TÍPUS oszlopfejlécet.
1. Az **App Service**típusú két erőforrás létezik. Válassza ki az **App Service** típusú erőforrást a "-api" utótaggal. *with*
1. Az App Service **áttekintése**, másolja az URL-értéket, amely az **API-végpont** URL-címét jelöli a telepített Blockchain Workbench.

    ![App Service API-végpont URL-címe](media/use-api/app-service-api.png)

## <a name="authentication"></a>Hitelesítés

A Blockchain Workbench REST API-ra irányuló kérelmeket az Azure Active Directory (Azure AD) védi.

Ha hitelesített kérelmet szeretne benyújtani a REST API-khoz, az ügyfélkód hoz hitelesítést érvényes hitelesítő adatokkal, mielőtt meghívhatna az API-t. A hitelesítést az Azure AD koordinálja a különböző szereplők között, és a hitelesítés bizonyítékaként [hozzáférési jogkivonatot](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#access-token) biztosít az ügyfélnek. A jogkivonatezután elküldi a REST API-kérelmek HTTP-engedélyezési fejlécében. Az Azure AD-hitelesítésről az [Azure Active Directory fejlesztőknek című](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)témakörben olvashat bővebben.

Tekintse meg a [REST API-minták](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) at példákat a hitelesítéshez.

## <a name="using-postman"></a>A Postman használata

Ha szeretné tesztelni vagy kísérletezni workbench API-k, a [Postman](https://www.postman.com) segítségével API-hívásokat a központi telepítést. [Töltsön le egy workbench-API-kérelmek postman-gyűjteménye itHubról egy postman-gyűjteményt.](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) Tekintse meg a README fájlt a példa API-kérelmek hitelesítésével és használatával kapcsolatos részletekért.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Két API-hívás segítségével hozzon létre egy Blockchain Workbench-alkalmazást. Ezt a módszert csak workbench-rendszergazdákkal rendelkező felhasználók hajthatják végre.

Az [Applications POST API-val](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationspost) feltöltheti az alkalmazás JSON-fájlját, és lekaphat egy alkalmazásazonosítót.

### <a name="applications-post-request"></a>Jelentkezések POST a kérelem

Az **appFile** paraméterrel küldje el a konfigurációs fájlt a kérelem törzsének részeként.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Alkalmazások POST válasz

A létrehozott alkalmazásazonosító a válaszban kerül visszaadásra. Az alkalmazásazonosítóra van szükség ahhoz, hogy a konfigurációs fájlt a kódfájlhoz társítsa, amikor a következő API-t hívja meg.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Szerződéskód POST kérelem

Használja az [alkalmazások szerződéskód POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/contractcodepost) átadásával az alkalmazás azonosítóját feltölteni az alkalmazás Solidity kódfájl. A hasznos adat lehet egyetlen tömörségi fájl vagy tömörségfájlokat tartalmazó tömörített fájl.

Cserélje le a következő értékeket:

| Paraméter | Érték |
|-----------|-------|
| {applicationId} | Értéket ad vissza a POST API alkalmazásokból. |
| {ledgerId} | A főkönyv indexe. Az érték általában 1. Az érték hez a [Főkönyv táblában](data-sql-management-studio.md) is ellenőrizheti. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Szerződéskód POST válasz

Ha sikeres, a válasz tartalmazza a [Szerződéskód táblából](data-sql-management-studio.md)létrehozott szerződéskódazonosítót.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Szerepkörök hozzárendelése felhasználókhoz

Használja az [Alkalmazások szerepkör-hozzárendelések POST API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) átadásával az alkalmazás azonosítóját, a felhasználói azonosítót és az alkalmazás szerepkör-azonosítót, hogy hozzon létre egy felhasználó közötti leképezés a megadott blockchain alkalmazás. Ezt a módszert csak workbench-rendszergazdákkal rendelkező felhasználók hajthatják végre.

### <a name="role-assignments-post-request"></a>Szerepkör-hozzárendelések POST kérés

Cserélje le a következő értékeket:

| Paraméter | Érték |
|-----------|-------|
| {applicationId} | Adja vissza az értéket az Applications POST API-ból. |
| {userId} | A [Felhasználó tábla](data-sql-management-studio.md)felhasználói azonosítója érték. |
| {applicationRoleId} | Az application id-hez társított alkalmazásszerepkör-azonosító értéke az [ApplicationRole táblából](data-sql-management-studio.md). |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Szerepkör-hozzárendelések POST válasz

Ha sikeres, a válasz tartalmazza a létrehozott szerepkör-hozzárendelés-azonosítót a [RoleAssignment táblából.](data-sql-management-studio.md)

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Alkalmazások listázása

Az [Alkalmazások get API-jával](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) lekérheti a felhasználó számára az összes Blockchain Workbench alkalmazást. Ebben a példában a bejelentkezett felhasználó két alkalmazáshoz fér hozzá:

- [Objektumok átvitele](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Hűtött szállítás](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Alkalmazások beküldési kérelme

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Alkalmazások GET válasza

A válasz felsorolja az összes blockchain alkalmazásokat, amelyekhez a felhasználó hozzáfér a Blockchain Workbench.The response lists all blockchain applications which a user has access in Blockchain Workbench. Blockchain Workbench rendszergazdák kap minden blockchain alkalmazás. A nem Workbench-rendszergazdák minden olyan blokklánc-alkalmazást kapnak, amelyhez legalább egy társított alkalmazásszerepkör vagy egy társított intelligens szerződéspéldány szerepkör tartozik.

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

[Az Alkalmazások munkafolyamatai GET API-val](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) felsorolható egy adott blokklánc-alkalmazás összes munkafolyamata, amelyhez a felhasználó hozzáféréssel rendelkezik a Blockchain Workbench programban. Minden egyes blokklánc-alkalmazás rendelkezik egy vagy több munkafolyamattal, és minden munkafolyamathoz tartozik nulla vagy több intelligensszerződés-példány. Egy blockchain ügyfélalkalmazás, amely csak egy munkafolyamattal rendelkezik, azt javasoljuk, hogy hagyja ki a felhasználói élmény folyamat, amely lehetővé teszi a felhasználók számára, hogy válassza ki a megfelelő munkafolyamatot.

### <a name="application-workflows-request"></a>Alkalmazás-munkafolyamatok kérése

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Alkalmazás-munkafolyamatokra adott válasz

A Blockchain Workbench rendszergazdái minden blokklánc-munkafolyamatot megkapnak. A nem Workbench-rendszergazdák minden olyan munkafolyamatot kapnak, amelyhez legalább egy társított alkalmazásszerepkörrel rendelkeznek, vagy intelligens szerződéspéldány-szerepkörhöz vannak társítva.

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

## <a name="create-a-contract-instance"></a>Szerződéspéldány létrehozása

A [Contracts V2 POST API-val](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/contractsv2/contractpost) új intelligens szerződéspéldányt hozhat létre egy munkafolyamathoz. A felhasználók csak akkor hozhatnak létre új intelligens szerződéspéldányt, ha a felhasználó alkalmazásszerepkörhöz van társítva, amely intelligens szerződéspéldányt kezdeményezhet a munkafolyamathoz.

> [!NOTE]
> Ebben a példában az API 2-es verzióját használja a használat. A 2-es verziójú szerződés API-k részletesebb enciklit biztosítanak a kapcsolódó ProvisioningStatus mezőkhöz.

### <a name="contracts-post-request"></a>Szerződések POSTa kérelem

Cserélje le a következő értékeket:

| Paraméter | Érték |
|-----------|-------|
| {workflowId} | A munkafolyamat-azonosító értéke a szerződés ConstructorID azonosítója a [Munkafolyamat táblából.](data-sql-management-studio.md) |
| {contractCodeId} | Szerződéskód azonosítóértéke a [ContractCode táblából](data-sql-management-studio.md). Korrelálja az alkalmazásazonosítót és a főkönyvi azonosítót a létrehozni kívánt szerződéspéldányhoz. |
| {connectionId} | A Kapcsolat azonosítója érték a [Kapcsolat táblából](data-sql-management-studio.md). |

A kérelem törzséhez állítsa be az értékeket a következő információk használatával:

| Paraméter | Érték |
|-----------|-------|
| workflowFunctionID | Azonosító a [WorkflowFunction táblából](data-sql-management-studio.md). |
| workflowActionParameters | A konstruktornak átadott paraméterek névértékpárjai. Minden paraméterhez használja a [WorkflowFunctionParameter tábla](data-sql-management-studio.md) workflowFunctionParameterID értékét. |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Szerződések POST válasz

Ha sikeres, a szerepkör-hozzárendelések API a ContractActionID azonosítót adja vissza a [ContractActionParameter táblából.](data-sql-management-studio.md)

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Az egyes munkafolyamatok intelligensszerződés-példányainak listázása

[A Contracts GET API használatával](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) megjelenítheti a munkafolyamat összes intelligens szerződéspéldányát. Vagy engedélyezheti a felhasználóknak, hogy mélyen belevetik magukat bármelyik megjelenített intelligens szerződés példányba.

### <a name="contracts-request"></a>Szerződéskérés

A jelen példában tegyük fel, hogy egy felhasználó szeretne műveleteket végezni az intelligensszerződés-példányok egyikével.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Szerződésekre adott válasz

A válasz felsorolja a megadott munkafolyamat összes intelligens szerződéspéldányát. A Workbench-rendszergazdák minden intelligens szerződéspéldányt kapnak. A nem Workbench-rendszergazdák minden olyan intelligens szerződéspéldányt kapnak, amelyhez legalább egy társított alkalmazásszerepkörrel rendelkeznek, vagy intelligens szerződéspéldány-szerepkörhöz vannak társítva.

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

A [Szerződésművelet GET API-jával](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) a szerződés állapota miatt megjelennek a rendelkezésre álló felhasználói műveletek. 

### <a name="contract-action-request"></a>Szerződésművelet-kérelem

Ebben a példában a felhasználó az általuk létrehozott új intelligens szerződéshez elérhető összes műveletet vizsgálja.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Szerződésművelet-válasz

A válasz felsorolja az összes olyan műveletet, amelyhez a felhasználó hozzáférhet az adott intelligensszerződés-példány aktuális állapotában.

* Módosítás: A felhasználó módosíthatja egy objektum leírását és árát.
* Megszüntetés: Lehetővé teszi a felhasználó számára, hogy felbontsa az eszköz szerződését.

A felhasználók megkapják az összes elérhető műveletet, ha rendelkeznek alkalmazás-szerepkörrel vagy intelligensszerződés-példányi szerepkörrel az adott intelligensszerződés-példány aktuális állapotához.

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

A [Contract Action POST API-val](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) műveleteket tehet a megadott intelligens szerződéspéldányhoz.

### <a name="contract-action-post-request"></a>Szerződés művelet POST kérelem

Ebben az esetben vegye figyelembe azt a forgatókönyvet, amelyben a felhasználó módosítani szeretné egy eszköz leírását és árát.

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

A felhasználók csak olyan műveleteket tudnak végrehajtani, amelyhez rendelkeznek alkalmazás-szerepkörrel vagy intelligensszerződés-példányi szerepkörrel az adott intelligensszerződés-példány aktuális állapotára vonatkozóan.

### <a name="contract-action-post-response"></a>Szerződés művelet POST válasz

Ha a POST művelet sikeres, HTTP 200 OK választ fog kapni választörzs nélkül.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>További lépések

A Blockchain Workbench API-król az [Azure Blockchain Workbench REST API-k hivatkozási részében talál további](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)információt.

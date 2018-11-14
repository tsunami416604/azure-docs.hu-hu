---
title: Az Azure Blockchain Workbench üzenetek integráció áttekintése
description: Üzenetek használata az Azure Blockchain Workbench áttekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f8f3584475415cf9ca19458f6da78d34df37f438
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614361"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Az Azure Blockchain Workbench integrációs üzenetkezelés

Amellett, hogy a REST API-val, az Azure Blockchain Workbench is messaging-alapú integrációt biztosít. Workbench Főkönyv-központú események Azure Event Griddel, alsóbb rétegbeli fogyasztói gyűjthet adatokat, vagy be ezen események alapján keresztül teszi közzé. Ezek az ügyfelek megbízható üzenetküldést igénylő, az Azure Blockchain Workbench használatával továbbítja az üzeneteket, valamint egy Azure Service Bus-végponttal.

## <a name="input-apis"></a>A bemeneti API-k

Ha el szeretné indítani a külső rendszerekből a felhasználók létrehozása, szerződések létrehozása és frissítése szerződések tranzakciók, használhatja a bemeneti API-k üzenetkezelési Főkönyv tranzakció végrehajtásához. Lásd: [integrációs minták üzenetkezelési](https://aka.ms/blockchain-workbench-integration-sample) minta azt mutatja be, a bemeneti API-k.

Az alábbi táblázat a jelenleg elérhető bemeneti API-k.

### <a name="create-user"></a>Felhasználó létrehozása

Létrehoz egy új felhasználót.

A kérelemhez szükséges a következő mezőket:

| **Name (Név)**             | **Leírás**                                      |
|----------------------|------------------------------------------------------|
| Kérelemazonosító:            | Az ügyfél által megadott GUID azonosítója                                |
| Keresztnév            | A felhasználó utóneve                              |
| Vezetéknév             | A felhasználó vezetékneve                               |
| E-mail cím         | A felhasználó e-mail címe                           |
| externalId           | A felhasználó Azure AD objektum azonosítója                      |
| ConnectionId         | A blockchain-kapcsolat egyedi azonosítója |
| messageSchemaVersion | Üzenetkezelési sémaverzió                            |
| messageName          | **CreateUserRequest**                               |

Példa:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbenchet választ küld a következő mezőket:

| **Name (Név)**              | **Leírás**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| Kérelemazonosító:             | Az ügyfél által megadott GUID azonosítója |
| userId                | A létrehozott felhasználó azonosítója |
| UserChainIdentifier   | A blockchain-hálózat létrehozó felhasználó címe. A cím Ethereum, a felhasználó **a láncban lévő** címet. |
| ConnectionId          | A blockchain-kapcsolat egyedi azonosítója|
| messageSchemaVersion  | Üzenetkezelési sémaverzió |
| messageName           | **CreateUserUpdate** |
| status                | A felhasználó-létrehozási kérés állapotát.  Ha sikeres, értéke **sikeres**. Hiba esetén, a értéke **hiba**.     |
| AdditionalInformation | További információ a megadott állapota alapján |

Példa a sikeres **felhasználó létrehozása** Blockchain Workbenchet a választ:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Ha a kérelem sikertelen volt, vannak további információkat vegye fel a hiba részleteit.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Szerződés létrehozása

Létrehoz egy új szerződést.

A kérelemhez szükséges a következő mezőket:

| **Name (Név)**             | **Leírás**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| Kérelemazonosító:            | Az ügyfél által megadott GUID azonosítója |
| UserChainIdentifier  | A blockchain-hálózat létrehozó felhasználó címe. Ez a cím Ethereum, a felhasználó **lánc** címet. |
| ApplicationName      | Az alkalmazás neve |
| WorkflowName         | A munkafolyamat neve |
| paraméterek           | Bemeneti paraméterek szerződés létrehozása |
| ConnectionId         | A blockchain-kapcsolat egyedi azonosítója |
| messageSchemaVersion | Üzenetkezelési sémaverzió |
| messageName          | **CreateContractRequest** |

Példa:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer", 
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbenchet választ küld a következő mezőket:

| **Name (Név)**                 | **Leírás**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| Kérelemazonosító:                | Az ügyfél által megadott GUID azonosítója                                                             |
| ContractId               | Az Azure Blockchain Workbench a szerződés egyedi azonosítója |
| ContractLedgerIdentifier | A szerződést a Főkönyv címe                                            |
| ConnectionId             | A blockchain-kapcsolat egyedi azonosítója                               |
| messageSchemaVersion     | Üzenetkezelési sémaverzió                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | A szerződés létrehozási kérés állapotát.  A lehetséges értékek: **elküldött**, **lekötött**, **hiba**.  |
| AdditionalInformation    | További információ a megadott állapota alapján                              |

Példa egy beküldött **szerződés létrehozása** Blockchain Workbenchet a választ:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

Példa egy véglegesített **szerződés létrehozása** Blockchain Workbenchet a választ:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Ha a kérelem sikertelen volt, vannak további információkat vegye fel a hiba részleteit.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Szerződés művelet létrehozása

Létrehoz egy új szerződés műveletet.

A kérelemhez szükséges a következő mezőket:

| **Name (Név)**                 | **Leírás**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Kérelemazonosító:                | Az ügyfél által megadott GUID azonosítója |
| UserChainIdentifier      | A blockchain-hálózat létrehozó felhasználó címe. Az Ethereum, ez az a felhasználó **lánc** címet. |
| ContractLedgerIdentifier | A szerződést a Főkönyv címe |
| WorkflowFunctionName     | A munkafolyamat függvény neve |
| paraméterek               | Bemeneti paraméterek szerződés létrehozása |
| ConnectionId             | A blockchain-kapcsolat egyedi azonosítója |
| messageSchemaVersion     | Üzenetkezelési sémaverzió |
| messageName              | **CreateContractActionRequest** |

Példa:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbenchet választ küld a következő mezőket:

| **Name (Név)**              | **Leírás**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| Kérelemazonosító:             | Az ügyfél által megadott GUID azonosítója|
| ContractId            | Az Azure Blockchain Workbench a szerződés egyedi azonosítója |
| ConnectionId          | A blockchain-kapcsolat egyedi azonosítója |
| messageSchemaVersion  | Üzenetkezelési sémaverzió |
| messageName           | **CreateContractActionUpdate** |
| status                | A szerződés művelet kérés állapotát. A lehetséges értékek: **elküldött**, **lekötött**, **hiba**.                         |
| AdditionalInformation | További információ a megadott állapota alapján |

Példa egy beküldött **szerződés művelet létrehozása** Blockchain Workbenchet a választ:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Példa egy véglegesített **szerződés művelet létrehozása** Blockchain Workbenchet a választ:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

Ha a kérelem sikertelen volt, vannak további információkat vegye fel a hiba részleteit.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>A bemeneti API-hibakódok és üzenetek

**4000-es hibakód: Hibás kérés hiba**
- Érvénytelen connectionId
- CreateUserRequest deszerializálás sikertelen volt
- CreateContractRequest deszerializálás sikertelen volt
- CreateContractActionRequest deszerializálás sikertelen volt
- {Alkalmazásnév szerint meghatározott} alkalmazást nem létezik.
- {Alkalmazásnév által azonosított} alkalmazás nem rendelkezik a munkafolyamat
- UserChainIdentifier nem létezik.
- A szerződés {Főkönyv azonosító által azonosított} nem létezik.
- Nincs kontraktu {Főkönyv azonosító által azonosított} függvény {munkafolyamat függvény neve}
- UserChainIdentifier nem létezik.

**Hibakód: 4090: ütközés lép fel hiba**
- A felhasználó már létezik
- Szerződés már létezik.
- Szerződés művelet már létezik

**5000-es hibakód: belső kiszolgálóhiba**
- Kivétel üzenetek

## <a name="event-notifications"></a>Eseményértesítések

Eseményértesítések értesítse a felhasználókat és a Blockchain Workbenchet, és a blockchain-hálózat csatlakozik az eseményeket generáló rendszerek üzenetkézbesítési igényeit is használható. Eseményértesítések használhatja közvetlenül a kódban vagy használt eszközök, például a Logic Apps és a Flow-folyamat az adatok rendszerek üzenetkézbesítési igényeit.

Lásd: [értesítési üzenet referencia](#notification-message-reference) különböző fogadott üzenetek részleteit.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Fogyasztó Event Grid-események az Azure Functions használatával

Ha a felhasználó használni szeretne értesítést kapni a Blockchain Workbenchet eseményeket az Event Grid használatával, az Azure Functions szolgáltatással felhasználhatja Event Grid-eseményfolyam megtekintéséhez.

1. Hozzon létre egy **Azure-Függvényalkalmazás** az Azure Portalon.
2. Hozzon létre egy új függvényt.
3. Keresse meg a sablont az Event Gridhez. Alapszintű sablon kódot olvasni az üzenetet jelenik meg. Szükség szerint módosítsa a kódot.
4. Mentse a függvényt. 
5. Válassza ki az Event Grid Blockchain Workbenchet erőforráscsoportból.

### <a name="consuming-event-grid-events-with-logic-apps"></a>A Logic Apps szolgáltatással a fogyasztó Event Grid-események

1.  Hozzon létre egy új **Azure Logic Apps** az Azure Portalon.
2.  Az Azure Logic Apps a portál megnyitásakor, a rendszer kéri, válassza ki az eseményindítóként. Válassza ki **Azure Event Grid – erőforrás-esemény bekövetkezésekor**.
3. A munkafolyamat-tervezővel jelenik meg, amikor a rendszer felszólítja, hogy jelentkezzen be.
4. Válassza ki az előfizetést. Erőforrás szerint **Microsoft.EventGrid.Topics**. Válassza ki a **erőforrásnév** a neve, az Azure Blockchain Workbench erőforráscsoport az erőforrásban.
5. Válassza ki az Event Grid Blockchain Workbenchet erőforráscsoportból.

## <a name="using-service-bus-topics-for-notifications"></a>Az értesítések használatával a Service Bus-témakörök

Service Bus-témakörök Blockchain Workbenchet bekövetkező eseményeket a felhasználók értesítése használható. 

1.  Keresse meg a Service Bus, a Workbench-erőforráscsoporton belül.
2.  Válassza ki **témakörök**.
3.  Válassza ki **workbench-külső**.
4.  Hozzon létre egy új előfizetést ehhez a témakörhöz. Amíg egy kulcs beszerzése.
5.  Hozzon létre egy programot, amelyre feliratkozik az eseményekre ebből az előfizetésből.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Service Bus-üzenetek a Logic Apps felhasználása

1. Hozzon létre egy új **Azure Logic Apps** az Azure Portalon.
2. Az Azure Logic Apps a portál megnyitásakor, a rendszer kéri, válassza ki az eseményindítóként. Típus **a Service Bus** a keresőmezőbe, és válassza ki a megfelelő kapcsolati típusú trigger kíván használni a Service Bus szolgáltatással. Ha például **a Service Bus – amikor egy üzenet érkezik egy témakör-előfizetés (automatikus lezárás)**.
3. Ha a munkafolyamat-tervezővel jelenik meg, adja meg a Service Bus kapcsolati adatait.
4. Válassza ki az előfizetését, és adja meg a témakör a **workbench-külső**.
5. A logika, amely a trigger által az üzenet már használja az alkalmazás fejlesztése.

## <a name="notification-message-reference"></a>Értesítési üzenet leírása

Attól függően, a **OperationName**, az értesítési üzenetek, a következő üzenet típusok egyikével rendelkeznek.

### <a name="accountcreated"></a>AccountCreated

Azt jelzi, hogy az új fiók a megadott lánc hozzáadandó kért-e.

| Name (Név)    | Leírás  |
|----------|--------------|
| Felhasználói azonosító  | A létrehozott felhasználó azonosítója. |
| ChainIdentifier | A blockchain-hálózat létrehozó felhasználó címe. Az Ethereum, ez lenne a felhasználó **a láncban lévő** címet. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Azt jelzi, hogy az egy kérelem beszúrása vagy frissítése egy szerződést az elosztott Főkönyv történt-e.

| Name (Név) | Leírás |
|-----|--------------|
| ChainID | A láncban, a kéréshez társított egyedi azonosítója |
| BlockId | A blokkolás a a Főkönyv egyedi azonosítója |
| ContractId | A szerződés egyedi azonosítója |
| ContractAddress |       A szerződést a Főkönyv címe |
| TransactionHash  |     A tranzakció a Főkönyv a kivonata |
| OriginatingAddress |   A tranzakció a feladó címe |
| Műveletnév       |     A művelet neve |
| IsUpdate        |      Annak jelzése, hogy ez a frissítés |
| Paraméterek       |     Az objektum, amely azonosítja a név, érték és adatok művelet küldött paraméterek listáját |
| TopLevelInputParams |  Olyan esetekben, ahol egy szerződés csatlakozik egy vagy több szerződést ezek a paraméterek a legfelső szintű szerződésből. |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

Azt jelzi, hogy egy kérés érkezett a végrehajtás egy műveletet az elosztott Főkönyv egy adott szerződést.

| Name (Név)                     | Leírás                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | A szerződés művelet egyedi azonosítója |
| ChainIdentifier          | A láncban egyedi azonosítója |
| ConnectionId             | A kapcsolat egyedi azonosítója |
| UserChainIdentifier      | A blockchain-hálózat létrehozó felhasználó címe. Ez a cím Ethereum, a felhasználó **lánc** címet. |
| ContractLedgerIdentifier | A szerződést a Főkönyv címe |
| WorkflowFunctionName     | A munkafolyamat függvény neve |
| WorkflowName             | A munkafolyamat neve |
| WorkflowBlobStorageURL   | Az URL-címét a blob Storage-szerződés |
| ContractActionParameters | A szerződés művelet paramétereit |
| TransactionHash          | A tranzakció a Főkönyv a kivonata |
| Kiépítési állapot      | A művelet aktuális kiépítési állapota.</br>0 – létrehozva</br>1 – folyamatban</br>2 – befejezése</br> Teljes azt jelzi, hogy a könyvelési megerősítést, amelyek ezen sikeresen hozzáadva |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

Azt jelzi, hogy egy kérés érkezett-e frissíteni a felhasználó egy adott elosztott Főkönyv egyenlegéről.

> [!NOTE]
> Ez az üzenet csak ezek a fiókok finanszírozási igénylő főkönyvek jön létre.
> 

| Name (Név)    | Leírás                              |
|---------|------------------------------------------|
| Cím | A cím, amelyet a rendszer finanszírozott |
| Egyenleg | A felhasználó egyenleg egyenlege         |
| ChainID | A láncban egyedi azonosítója     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

Az üzenet azt jelzi, hogy egy kérés érkezett-e egy elosztott Főkönyv hozzáadásához.

| Name (Név)           | Leírás                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | Egyedi azonosítója, amelyhez a blokk lett hozzáadva a lánc             |
| BlockId        | Az Azure Blockchain Workbench blokk egyedi azonosítója |
| BlockHash      | A blokk kivonata                                                 |
| BlockTimeStamp | A blokk történő küldés időbélyegzője                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

Üzenet hozzáadása egy tranzakció egy elosztott Főkönyv kérést részletesen.

| Name (Név)            | Leírás                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | Egyedi azonosítója, amelyhez a blokk lett hozzáadva a lánc             |
| BlockId         | Az Azure Blockchain Workbench blokk egyedi azonosítója |
| TransactionHash | A tranzakció kivonata                                           |
| Ettől:            | A tranzakció a feladó címe                      |
| Művelet              | A tranzakció az illetékes címzett címe              |
| Érték           | Az érték a tranzakcióban                                 |
| IsAppBuilderTx  | Annak jelzése, hogy ez az a Blockchain Workbenchet tranzakció                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

Részletes adatokat biztosít a hozzárendelés egy lánc azonosító egy szerződést. Például az Ethereum-beli blockchain a címet a Főkönyv a szerződést.

| Name (Név)            | Leírás                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Az Azure Blockchain Workbench a szerződés egyedi azonosítója |
| ChainIdentifier | A szerződés a lánc azonosítója                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="classes-used-by-message-types"></a>Üzenetek által használt osztályok

### <a name="messagemodelbase"></a>MessageModelBase

Az alapszintű modell pedig az összes üzenetet.

| Name (Név)          | Leírás                          |
|---------------|--------------------------------------|
| OperationName | A művelet neve           |
| Kérelemazonosító:     | A kérelem egyedi azonosítója |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

Tartalmazza a nevét, az érték és a egy paraméter típusa.

| Name (Név)  | Leírás                 |
|-------|-----------------------------|
| Name (Név)  | A paraméter neve  |
| Érték | A paraméter értéke |
| Típus  | A paraméter típusa  |

``` csharp
public class ContractInputParameter
{
    public string Name { get; set; }
    public string Value { get; set; }
    public string Type { get; set; }
}
```

#### <a name="contractproperty"></a>ContractProperty

Tartalmazza az azonosítója, a nevét, a érték és a egy tulajdonság típusát.

| Name (Név)  | Leírás                |
|-------|----------------------------|
| Azonosító    | A tulajdonság azonosítója    |
| Name (Név)  | A tulajdonság neve  |
| Érték | A tulajdonság értéke. |
| Típus  | A tulajdonság típusa  |

``` csharp
public class ContractProperty
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Value { get; set; }
    public string DataType { get; set; }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az intelligens szerződés integrációs minták](integration-patterns.md)
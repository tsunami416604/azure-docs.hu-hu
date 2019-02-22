---
title: Az Azure Blockchain Workbench üzenetek integráció áttekintése
description: Üzenetek használata az Azure Blockchain Workbench áttekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 02/21/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 02a6cbabaeb7d9c6f764eec5dd86226e37aa6c2b
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593522"
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
| requestId            | Az ügyfél által megadott GUID azonosítója                                |
| Keresztnév            | A felhasználó utóneve                              |
| Vezetéknév             | A felhasználó vezetékneve                               |
| emailAddress         | A felhasználó e-mail címe                           |
| externalId           | A felhasználó Azure AD objektum azonosítója                      |
| connectionId         | A blockchain-kapcsolat egyedi azonosítója |
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
| requestId             | Az ügyfél által megadott GUID azonosítója |
| userId                | A létrehozott felhasználó azonosítója |
| userChainIdentifier   | A blockchain-hálózat létrehozó felhasználó címe. A cím Ethereum, a felhasználó **a láncban lévő** címet. |
| connectionId          | A blockchain-kapcsolat egyedi azonosítója|
| messageSchemaVersion  | Üzenetkezelési sémaverzió |
| messageName           | **CreateUserUpdate** |
| status                | A felhasználó-létrehozási kérés állapotát.  Ha sikeres, értéke **sikeres**. Hiba esetén, a értéke **hiba**.     |
| additionalInformation | További információ a megadott állapota alapján |

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

Ha a kérelem sikertelen volt, a hiba részleteit további információk is megtalálhatók.

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
| requestId            | Az ügyfél által megadott GUID azonosítója |
| userChainIdentifier  | A blockchain-hálózat létrehozó felhasználó címe. Ez a cím Ethereum, a felhasználó **lánc** címet. |
| applicationName      | Az alkalmazás neve |
| version              | Az alkalmazás verziója. Van szükség, ha engedélyezve van az alkalmazás több verzióját. Ellenkező esetben a verzió nem kötelező. Alkalmazások verziószámozása további információkért lásd: [Azure Blockchain Workbench alkalmazás verziószámozása](version-app.md). |
| WorkflowName         | A munkafolyamat neve |
| paraméterek           | Bemeneti paraméterek szerződés létrehozása |
| connectionId         | A blockchain-kapcsolat egyedi azonosítója |
| messageSchemaVersion | Üzenetkezelési sémaverzió |
| messageName          | **CreateContractRequest** |

Példa:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
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
| requestId                | Az ügyfél által megadott GUID azonosítója                                                             |
| contractId               | Az Azure Blockchain Workbench a szerződés egyedi azonosítója |
| contractLedgerIdentifier | A szerződést a Főkönyv címe                                            |
| connectionId             | A blockchain-kapcsolat egyedi azonosítója                               |
| messageSchemaVersion     | Üzenetkezelési sémaverzió                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | A szerződés létrehozási kérés állapotát.  Érvényes értékek: **Elküldött**, **véglegesített**, **hiba**.  |
| additionalInformation    | További információ a megadott állapota alapján                              |

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

Ha a kérelem sikertelen volt, a hiba részleteit további információk is megtalálhatók.

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
| requestId                | Az ügyfél által megadott GUID azonosítója |
| userChainIdentifier      | A blockchain-hálózat létrehozó felhasználó címe. Ez a cím Ethereum, a felhasználó **lánc** címet. |
| contractLedgerIdentifier | A szerződést a Főkönyv címe |
| version                  | Az alkalmazás verziója. Van szükség, ha engedélyezve van az alkalmazás több verzióját. Ellenkező esetben a verzió nem kötelező. Alkalmazások verziószámozása további információkért lásd: [Azure Blockchain Workbench alkalmazás verziószámozása](version-app.md). |
| workflowFunctionName     | A munkafolyamat függvény neve |
| paraméterek               | Bemeneti paraméterek szerződés létrehozása |
| connectionId             | A blockchain-kapcsolat egyedi azonosítója |
| messageSchemaVersion     | Üzenetkezelési sémaverzió |
| messageName              | **CreateContractActionRequest** |

Példa:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
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
| requestId             | Az ügyfél által megadott GUID azonosítója|
| contractId            | Az Azure Blockchain Workbench a szerződés egyedi azonosítója |
| connectionId          | A blockchain-kapcsolat egyedi azonosítója |
| messageSchemaVersion  | Üzenetkezelési sémaverzió |
| messageName           | **CreateContractActionUpdate** |
| status                | A szerződés művelet kérés állapotát. Érvényes értékek: **Elküldött**, **véglegesített**, **hiba**.                         |
| additionalInformation | További információ a megadott állapota alapján |

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

Ha a kérelem sikertelen volt, a hiba részleteit további információk is megtalálhatók.

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

**4000. Hibakód: Hibás kérelem hiba**
- Érvénytelen connectionId
- CreateUserRequest deszerializálás sikertelen volt
- CreateContractRequest deszerializálás sikertelen volt
- CreateContractActionRequest deserialization failed
- {Alkalmazásnév szerint meghatározott} alkalmazást nem létezik.
- {Alkalmazásnév által azonosított} alkalmazás nem rendelkezik a munkafolyamat
- UserChainIdentifier nem létezik.
- A szerződés {Főkönyv azonosító által azonosított} nem létezik.
- Nincs kontraktu {Főkönyv azonosító által azonosított} függvény {munkafolyamat függvény neve}
- UserChainIdentifier nem létezik.

**4090. Hibakód: Ütközés hiba**
- A felhasználó már létezik
- Szerződés már létezik.
- Szerződés művelet már létezik

**5000-es hibakód: Belső kiszolgálóhiba**
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

1. Hozzon létre egy új **Azure Logic Apps** az Azure Portalon.
2. Az Azure Logic Apps a portál megnyitásakor, a rendszer kéri, válassza ki az eseményindítóként. Válassza ki **Azure Event Grid – erőforrás-esemény bekövetkezésekor**.
3. A munkafolyamat-tervezővel jelenik meg, amikor a rendszer felszólítja, hogy jelentkezzen be.
4. Válassza ki az előfizetést. Erőforrás szerint **Microsoft.EventGrid.Topics**. Válassza ki a **erőforrásnév** a neve, az Azure Blockchain Workbench erőforráscsoport az erőforrásban.
5. Válassza ki az Event Grid Blockchain Workbenchet erőforráscsoportból.

## <a name="using-service-bus-topics-for-notifications"></a>Az értesítések használatával a Service Bus-témakörök

Service Bus-témakörök Blockchain Workbenchet bekövetkező eseményeket a felhasználók értesítése használható. 

1. Keresse meg a Service Bus, a Workbench-erőforráscsoporton belül.
2. Válassza ki **témakörök**.
3. Válassza ki **kimenő-témakör**.
4. Hozzon létre egy új előfizetést ehhez a témakörhöz. Amíg egy kulcs beszerzése.
5. Hozzon létre egy programot, amelyre feliratkozik az eseményekre ebből az előfizetésből.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Service Bus-üzenetek a Logic Apps felhasználása

1. Hozzon létre egy új **Azure Logic Apps** az Azure Portalon.
2. Az Azure Logic Apps a portál megnyitásakor, a rendszer kéri, válassza ki az eseményindítóként. Típus **a Service Bus** a keresőmezőbe, és válassza ki a megfelelő kapcsolati típusú trigger kíván használni a Service Bus szolgáltatással. Ha például **a Service Bus – amikor egy üzenet érkezik egy témakör-előfizetés (automatikus lezárás)**.
3. Ha a munkafolyamat-tervezővel jelenik meg, adja meg a Service Bus kapcsolati adatait.
4. Válassza ki az előfizetését, és adja meg a témakör a **workbench-külső**.
5. A logika, amely a trigger által az üzenet már használja az alkalmazás fejlesztése.

## <a name="notification-message-reference"></a>Értesítési üzenet leírása

Attól függően, a **messageName**, az értesítési üzenetek, a következő üzenet típusok egyikével rendelkeznek.

### <a name="block-message"></a>Blokkolási üzenet

Az egyes blokkok kapcsolatos információt tartalmazza. A *BlockMessage* tartalmaz egy blokkblob szintű információkat és a tranzakció adatainak egy szakaszban.

| Name (Név) | Leírás |
|------|-------------|
| blokkolás | Tartalmaz [információk letiltása](#block-information) |
| tranzakciónként | A gyűjtemény tartalmaz [tranzakcióinformációi](#transaction-information) az adatblokk számára |
| connectionId | A kapcsolat egyedi azonosítója |
| messageSchemaVersion | Üzenetkezelési sémaverzió |
| messageName | **BlockMessage** |
| additionalInformation | További információk |

#### <a name="block-information"></a>Blokk-információk

| Name (Név)              | Leírás |
|-------------------|-------------|
| BlockId           | Az Azure Blockchain Workbench blokk egyedi azonosítója |
| BlockNumber       | A blokkolás a a Főkönyv egyedi azonosítója |
| BlockHash         | A blokk kivonata |
| previousBlockHash | Az előző blokk kivonata |
| BlockTimestamp    | A blokk történő küldés időbélyegzője |

#### <a name="transaction-information"></a>Tranzakció információi

| Name (Név)               | Leírás |
|--------------------|-------------|
| transactionId      | Az Azure Blockchain Workbench tranzakció egyedi azonosítója |
| TransactionHash    | A tranzakció a Főkönyv a kivonata |
| forrás:               | A tranzakció forrás a Főkönyv az egyedi azonosítója |
| erre:                 | A tranzakció célgépre a Főkönyv egyedi azonosítója |
| provisioningStatus | Azonosítja a tranzakció a kiépítési folyamat az aktuális állapotát. Lehetséges értékek: </br>0 – a tranzakció az adatbázis az API által létrehozva</br>1 – a tranzakció el lett küldve a Főkönyv</br>2 – a tranzakció főkönyvi sikeresen véglegesítve lett</br>3-as vagy 4 – a tranzakció nem sikerült megerősíteni a Főkönyv</br>5 – a tranzakció sikeresen véglegesítve, főkönyvi |

Példa egy *BlockMessage* a Blockchain Workbenchet:

``` json
{
    "block": {
        "blockId": 123
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Szerződés üzenet

A szerződés kapcsolatos információt tartalmazza. Az üzenet tartalmaz egy szerződés tulajdonságokkal és a tranzakció adatainak egy szakaszban. A szerződés az adott blokk módosított összes tranzakció a tranzakció szakaszban szerepelnek.

| Name (Név) | Leírás |
|------|-------------|
| BlockId | Az Azure Blockchain Workbench blokk egyedi azonosítója |
| BlockHash | A blokk-kivonat |
| modifyingTransactions | [Módosított tranzakciók](#modifying-transaction-information) a szerződés |
| contractId | Az Azure Blockchain Workbench a szerződés egyedi azonosítója |
| contractLedgerIdentifier | A szerződést a Főkönyv egyedi azonosítója |
| contractProperties | [A szerződés tulajdonságai](#contract-properties) |
| isNewContract | Azt jelzi-e a jelen szerződés újonnan lett létrehozva. Lehetséges értékek a következők: igaz: a jelen szerződés volt létrehozott új szerződést. FALSE (hamis): a jelen szerződés a szerződés frissítés. |
| connectionId | A kapcsolat egyedi azonosítója |
| messageSchemaVersion | Üzenetkezelési sémaverzió |
| messageName | **ContractMessage** |
| additionalInformation | További információk |

#### <a name="modifying-transaction-information"></a>Tranzakció adatainak módosítása

| Name (Név)               | Leírás |
|--------------------|-------------|
| transactionId | Az Azure Blockchain Workbench tranzakció egyedi azonosítója |
| TransactionHash | A tranzakció a Főkönyv a kivonata |
| forrás: | A tranzakció forrás a Főkönyv az egyedi azonosítója |
| erre: | A tranzakció célgépre a Főkönyv egyedi azonosítója |

#### <a name="contract-properties"></a>Szerződés tulajdonságai

| Name (Név)               | Leírás |
|--------------------|-------------|
| workflowPropertyId | A munkafolyamat-tulajdonság az Azure Blockchain Workbench egyedi azonosítója |
| név | A munkafolyamat-tulajdonság neve |
| érték | A munkafolyamat tulajdonság értéke |

Példa egy *ContractMessage* a Blockchain Workbenchet:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Esemény jelenik meg: Szerződés függvény meghívási

Ha például a függvény nevét, a bemeneti paramétereket és a hívónak a függvény egy szerződés függvény meghívása információkat tartalmaz.

| Name (Név) | Leírás |
|------|-------------|
| EventName                   | **ContractFunctionInvocation** |
| hívó                      | [Hívó adatait](#caller-information) |
| contractId                  | Az Azure Blockchain Workbench a szerződés egyedi azonosítója |
| contractLedgerIdentifier    | A szerződést a Főkönyv egyedi azonosítója |
| Függvénynév                | A függvény neve |
| paraméterek                  | [Informace](#parameter-information) |
| Tranzakció                 | Tranzakció információi |
| inTransactionSequenceNumber | A sorszám a tranzakció a blokk |
| connectionId                | A kapcsolat egyedi azonosítója |
| messageSchemaVersion        | Üzenetkezelési sémaverzió |
| messageName                 | **EventMessage** |
| additionalInformation       | További információk |

#### <a name="caller-information"></a>Hívó adatait

| Name (Név) | Leírás |
|------|-------------|
| type | A hívónak, például egy felhasználó vagy a szerződés típusa |
| id | Az Azure Blockchain Workbench a hívó egyedi azonosítója |
| ledgerIdentifier | A hívónak a a Főkönyv egyedi azonosítója |

#### <a name="parameter-information"></a>Informace

| Name (Név) | Leírás |
|------|-------------|
| név | Paraméter neve |
| érték | Paraméter értéke |

#### <a name="event-message-transaction-information"></a>Esemény üzenet tranzakcióinformációi:

| Name (Név)               | Leírás |
|--------------------|-------------|
| transactionId      | Az Azure Blockchain Workbench tranzakció egyedi azonosítója |
| TransactionHash    | A tranzakció a Főkönyv a kivonata |
| forrás:               | A tranzakció forrás a Főkönyv az egyedi azonosítója |
| erre:                 | A tranzakció célgépre a Főkönyv egyedi azonosítója |

Példa egy *EventMessage ContractFunctionInvocation* a Blockchain Workbenchet:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Esemény jelenik meg: Alkalmazás adatfeldolgozást

Információkat tartalmaz a Workbench alkalmazásban egy alkalmazás feltöltésekor, mint például a nevét és verzióját, az alkalmazás feltöltése.

| Name (Név) | Leírás |
|------|-------------|
| EventName | **ApplicationIngestion** |
| applicationId | Az Azure Blockchain Workbench alkalmazás egyedi azonosítója |
| applicationName | Alkalmazásnév |
| applicationDisplayName | Alkalmazás megjelenített neve |
| applicationVersion | Alkalmazás verziószáma |
| applicationDefinitionLocation | Hol helyezkedik el az alkalmazás konfigurációs fájljának URL-címe |
| contractCodes | A gyűjtemény [szerződést kötöttek a kódok](#contract-code-information) az alkalmazás |
| applicationRoles | A gyűjtemény [alkalmazás-szerepkörök](#application-role-information) az alkalmazáshoz |
| applicationWorkflows | A gyűjtemény [alkalmazás munkafolyamatok](#application-workflow-information) az alkalmazáshoz |
| connectionId | A kapcsolat egyedi azonosítója |
| messageSchemaVersion | Üzenetkezelési sémaverzió |
| messageName | **EventMessage** |
| additionalInformation | Itt további információkat az alkalmazás munkafolyamat-állapotok és átmenet információkat tartalmaz. |

#### <a name="contract-code-information"></a>Kód adatai

| Name (Név) | Leírás |
|------|-------------|
| id | A szerződés kódfájl Azure Blockchain Workbench egyedi azonosítója |
| LedgerId | Az Azure Blockchain Workbench Főkönyv egyedi azonosítója |
| location | URL-címet, ahol a szerződés kódfájl megtalálható |

#### <a name="application-role-information"></a>Alkalmazás szerepkör-információ

| Name (Név) | Leírás |
|------|-------------|
| id | Az Azure Blockchain Workbench alkalmazás-szerepkör egyedi azonosítója |
| név | Az alkalmazás-szerepkör neve |

#### <a name="application-workflow-information"></a>Alkalmazás munkafolyamat-információk

| Name (Név) | Leírás |
|------|-------------|
| id | Az Azure Blockchain Workbench alkalmazás a munkafolyamat egyedi azonosítója |
| név | Alkalmazás a munkafolyamat neve |
| displayName | Alkalmazás a munkafolyamat megjelenített neve |
| functions | A gyűjtemény [függvények a kérelem munkafolyamat ](#workflow-function-information)|
| állapotok | A gyűjtemény [az alkalmazás a munkafolyamat állapotai](#workflow-state-information) |
| properties | Alkalmazás [tulajdonságok munkafolyamat-információk](#workflow-property-information) |

##### <a name="workflow-function-information"></a>Munkafolyamat-funkció információk

| Name (Név) | Leírás |
|------|-------------|
| id | Az alkalmazás munkafolyamat-funkció az Azure Blockchain Workbench egyedi azonosítója |
| név | Függvény neve |
| paraméterek | A függvény paramétereit |

##### <a name="workflow-state-information"></a>Munkafolyamat-állapot adatainak

| Name (Név) | Leírás |
|------|-------------|
| név | Állam neve |
| displayName | Állapot megjelenített neve |
| stílus | Állapot style (sikeres vagy sikertelen) |

##### <a name="workflow-property-information"></a>A munkafolyamat adatai

| Name (Név) | Leírás |
|------|-------------|
| id | Az Azure Blockchain Workbench alkalmazás a munkafolyamat tulajdonság egyedi azonosítója |
| név | Tulajdonság neve |
| type | Typ vlastnosti |

Példa egy *EventMessage ApplicationIngestion* a Blockchain Workbenchet:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationDefinitionLocation": "http://url"
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                                "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                                "name": "int"
                     }
                }
            ]
        }
    ]
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept"
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Esemény jelenik meg: Szerepkör-kijelölés

Információkat tartalmaz, amikor egy felhasználó hozzá van rendelve egy szerepkörhöz, a Workbench alkalmazásban például, hogy ki hajtotta végre a szerepkör-hozzárendelés és a szerepkör és a megfelelő alkalmazás nevét.

| Name (Név) | Leírás |
|------|-------------|
| EventName | **RoleAssignment** |
| applicationId | Az Azure Blockchain Workbench alkalmazás egyedi azonosítója |
| applicationName | Alkalmazásnév |
| applicationDisplayName | Alkalmazás megjelenített neve |
| applicationVersion | Alkalmazás verziószáma |
| applicationRole        | Információ a [alkalmazás-szerepkör](#roleassignment-application-role) |
| hozzárendelő               | Információ a [hozzárendelő](#roleassignment-assigner) |
| megbízott               | Információ a [megbízott](#roleassignment-assignee) |
| connectionId           | A kapcsolat egyedi azonosítója |
| messageSchemaVersion   | Üzenetkezelési sémaverzió |
| messageName            | **EventMessage** |
| additionalInformation  | További információk |

#### <a name="roleassignment-application-role"></a>RoleAssignment alkalmazás-szerepkör

| Name (Név) | Leírás |
|------|-------------|
| id | Az Azure Blockchain Workbench alkalmazás-szerepkör egyedi azonosítója |
| név | Az alkalmazás-szerepkör neve |

#### <a name="roleassignment-assigner"></a>RoleAssignment hozzárendelő

| Name (Név) | Leírás |
|------|-------------|
| id | Az Azure Blockchain Workbench felhasználó egyedi azonosítója |
| type | A hozzárendelő típusa |
| chainIdentifier | A felhasználó a Főkönyv egyedi azonosítója |

#### <a name="roleassignment-assignee"></a>RoleAssignment megbízott

| Name (Név) | Leírás |
|------|-------------|
| id | Az Azure Blockchain Workbench felhasználó egyedi azonosítója |
| type | A megbízott típusa |
| chainIdentifier | A felhasználó a Főkönyv egyedi azonosítója |

Példa egy *EventMessage RoleAssignment* a Blockchain Workbenchet:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>További lépések

- [Az intelligens szerződés integrációs minták](integration-patterns.md)

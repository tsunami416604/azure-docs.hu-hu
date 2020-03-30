---
title: Üzenetek használata az Azure Blockchain Workbench szolgáltatással való integrációhoz
description: Az Üzenetek használatával integrálhatja az Azure Blockchain Workbench Preview-t más rendszerekkel.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324514"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench-üzenetküldésintegráció

A REST API-k biztosítása mellett az Azure Blockchain Workbench üzenetkezelési alapú integrációt is biztosít. A Workbench főkönyvközpontú eseményeket tesz közzé az Azure Event Griden keresztül, lehetővé téve az alsóbb rétegbeli felhasználók számára az adatok betöltését vagy az események alapján történő műveletek megtetéését. A megbízható üzenetküldést igénylő ügyfelek számára az Azure Blockchain Workbench üzeneteket is kézbesít egy Azure Service Bus-végpontra.

## <a name="input-apis"></a>Bemeneti API-k

Ha külső rendszerekből szeretne tranzakciókat kezdeményezni felhasználók létrehozásához, szerződések létrehozásához és szerződések frissítéséhez, üzenetküldési bemeneti API-k at használhat a főkönyvtranzakcióinak végrehajtásához. Tekintse meg [az üzenetkezelési integrációs mintákat](https://aka.ms/blockchain-workbench-integration-sample) egy minta, amely bemutatja a bemeneti API-k.

A jelenleg elérhető bemeneti API-k az alábbiakban találhatók.

### <a name="create-user"></a>Felhasználó létrehozása

Új felhasználót hoz létre.

A kérelemhez a következő mezők szükségesek:

| **Név**             | **Leírás**                                      |
|----------------------|------------------------------------------------------|
| Kérelemazonosító            | Ügyfél által megadott GUID                                |
| firstName            | A felhasználó utóneve                              |
| lastName             | A felhasználó vezetékneve                               |
| e-mailcím         | A felhasználó e-mail címe                           |
| külső azonosító           | A felhasználó Azure AD-objektumazonosítója                      |
| connectionId         | A blokklánc-kapcsolat egyedi azonosítója |
| messageSchemaVersion | Üzenetküldési séma verziója                            |
| üzenetneve          | **CreateUserRequest**                               |

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

A Blockchain Workbench a következő mezőkkel válaszol:

| **Név**              | **Leírás**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| Kérelemazonosító             | Ügyfél által megadott GUID |
| userId                | A létrehozott felhasználó azonosítója |
| userChainIdentifier   | A blokklánc-hálózaton létrehozott felhasználó címe. Az Ethereumban a cím a felhasználó **láncolatú** címe. |
| connectionId          | A blokklánc-kapcsolat egyedi azonosítója|
| messageSchemaVersion  | Üzenetküldési séma verziója |
| üzenetneve           | **CreateUserUpdate (CreateUserUpdate)** |
| status                | A felhasználó létrehozási kérelmének állapota.  Ha sikeres, az érték **a Sikeres**. Hiba esetén az érték **a Hiba**.     |
| további információk | Az állapoton alapuló további információk |

Példa sikeres felhasználói válasz **létrehozására** a Blockchain Workbench-ből:

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

Ha a kérés sikertelen volt, a hiba részletei további információkat tartalmaznak.

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

Új szerződést hoz létre.

A kérelemhez a következő mezők szükségesek:

| **Név**             | **Leírás**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| Kérelemazonosító            | Ügyfél által megadott GUID |
| userChainIdentifier  | A blokklánc-hálózaton létrehozott felhasználó címe. Az Ethereumban ez a cím a felhasználó **lánccímén** van. |
| applicationName      | A kérelem neve |
| version              | Az alkalmazás verziója. Akkor szükséges, ha az alkalmazás több verziója is engedélyezve van. Ellenkező esetben a verzió megadása nem kötelező. Az alkalmazások verziószámozásáról az [Azure Blockchain Workbench-alkalmazás verziószámozása](version-app.md)című témakörben talál további információt. |
| munkafolyamatNeve         | A munkafolyamat neve |
| paraméterek           | Paraméterek bemenet a szerződés létrehozásához |
| connectionId         | A blokklánc-kapcsolat egyedi azonosítója |
| messageSchemaVersion | Üzenetküldési séma verziója |
| üzenetneve          | **CreateContractRequest** |

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

A Blockchain Workbench a következő mezőkkel válaszol:

| **Név**                 | **Leírás**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| Kérelemazonosító                | Ügyfél által megadott GUID                                                             |
| szerződésazonosító               | A szerződés egyedi azonosítója az Azure Blockchain Workbench-en belül |
| contractLedgerIdentifier | A szerződés címe a főkönyvben                                            |
| connectionId             | A blokklánc-kapcsolat egyedi azonosítója                               |
| messageSchemaVersion     | Üzenetküldési séma verziója                                                         |
| üzenetneve              | **CreateContractUpdate**                                                      |
| status                   | A szerződés-létrehozási kérelem állapota.  Lehetséges értékek: **Elküldött**, **Véglegesített**, **Sikertelen**.  |
| további információk    | Az állapoton alapuló további információk                              |

Példa a Blockchain Workbench által küldött **létrehozási szerződésre:**

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Példa a blockchain workbench-ből származó véglegesített **létrehozási** szerződésválaszra:

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

Ha a kérés sikertelen volt, a hiba részletei további információkat tartalmaznak.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Szerződésművelet létrehozása

Új szerződésműveletet hoz létre.

A kérelemhez a következő mezők szükségesek:

| **Név**                 | **Leírás**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Kérelemazonosító                | Ügyfél által megadott GUID |
| userChainIdentifier      | A blokklánc-hálózaton létrehozott felhasználó címe. Az Ethereumban ez a cím a felhasználó **lánccímén** van. |
| contractLedgerIdentifier | A szerződés címe a főkönyvben |
| version                  | Az alkalmazás verziója. Akkor szükséges, ha az alkalmazás több verziója is engedélyezve van. Ellenkező esetben a verzió megadása nem kötelező. Az alkalmazások verziószámozásáról az [Azure Blockchain Workbench-alkalmazás verziószámozása](version-app.md)című témakörben talál további információt. |
| workflowFunctionName     | A munkafolyamat-függvény neve |
| paraméterek               | Paraméterek bemenet a szerződés létrehozásához |
| connectionId             | A blokklánc-kapcsolat egyedi azonosítója |
| messageSchemaVersion     | Üzenetküldési séma verziója |
| üzenetneve              | **CreateContractActionRequest** |

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

A Blockchain Workbench a következő mezőkkel válaszol:

| **Név**              | **Leírás**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| Kérelemazonosító             | Ügyfél által megadott GUID|
| szerződésazonosító            | A szerződés egyedi azonosítója az Azure Blockchain Workbench-en belül |
| connectionId          | A blokklánc-kapcsolat egyedi azonosítója |
| messageSchemaVersion  | Üzenetküldési séma verziója |
| üzenetneve           | **CreateContractActionUpdate** |
| status                | A szerződésművelet-kérelem állapota. Lehetséges értékek: **Elküldött**, **Véglegesített**, **Sikertelen**.                         |
| további információk | Az állapoton alapuló további információk |

Példa a Blockchain Workbench által benyújtott **létrehozási** szerződésművelet-válaszra:

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

Példa a blockchain workbench-ből származó véglegesített **szerződés-műveletre** adott véglegesített létrehozási műveletre:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Ha a kérés sikertelen volt, a hiba részletei további információkat tartalmaznak.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Bemeneti API-hibakódok és üzenetek

**4000-es hibakód: Hibás kéréshiba**
- Érvénytelen kapcsolatazonosító
- A CreateUserRequest deszerializálása nem sikerült
- A CreateContractRequest deszerializálása nem sikerült
- A CreateContractActionRequest deszerializálása nem sikerült
- Az alkalmazás névvel azonosított{} alkalmazás nem létezik.
- Az alkalmazás névvel azonosított{} alkalmazás nem rendelkezik munkafolyamattal
- A UserChainIdentifier nem létezik.
- A főkönyvi azonosítóval azonosított{szerződés} nem létezik.
- A főkönyvi azonosítóval azonosított szerződésnek} nincs függvénye {munkafolyamat-függvény neve}
- A UserChainIdentifier nem létezik.

**4090-es hibakód: Ütközési hiba**
- A felhasználó már létezik
- A szerződés már létezik
- A szerződésművelet már létezik

**5000-es hibakód: Belső kiszolgálóhiba**
- Kivételüzenetek

## <a name="event-notifications"></a>Eseményértesítések

Az eseményértesítések segítségével értesítheti a felhasználókat és az alsóbb rétegbeli rendszereket a Blockchain Workbench-ben és a blokklánc-hálózatban bekövetkező eseményekről. Az eseményértesítések közvetlenül a kódban használhatók fel, vagy olyan eszközökkel használhatók, mint a Logic Apps és a Flow, hogy az adatok áramlását az alsóbb rétegbeli rendszerekbe váltsák.

A különböző üzenetek fogadásáról az [Értesítési üzenet hivatkozása.](#notification-message-reference)

### <a name="consuming-event-grid-events-with-azure-functions"></a>Eseményrács-események fogyasztása az Azure Functions funkcióival

Ha egy felhasználó szeretné használni Event Grid értesítést kell kapnia a blockchain workbench események, az Azure Functions használatával felhasználhatja az eseményeket Event Grid használatával.

1. Hozzon létre egy **Azure-függvényalkalmazást** az Azure Portalon.
2. Hozzon létre egy új függvényt.
3. Keresse meg az Event Grid sablonját. Az üzenet olvasásához szükséges egyszerű sablonkód jelenik meg. Szükség szerint módosítsa a kódot.
4. Mentse a függvényt. 
5. Válassza ki az Eseményrácsot a Blockchain Workbench erőforráscsoportjából.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Eseményrács-események fogyasztása a Logic Apps alkalmazásokkal

1. Hozzon létre egy új **Azure Logic Alkalmazást** az Azure Portalon.
2. Amikor megnyitja az Azure Logic alkalmazást a portálon, a rendszer kérni fogja, hogy válasszon ki egy eseményindítót. Válassza ki **az Azure Event Grid -- Amikor egy erőforrás-esemény történik.**
3. Amikor megjelenik a munkafolyamat-tervező, a rendszer kéri a bejelentkezést.
4. Válassza ki az Előfizetést. Erőforrás **microsoft.EventGrid.Topics néven.** Válassza ki az **erőforrás nevét** az erőforrás nevét az Azure Blockchain Workbench erőforráscsoportból.
5. Válassza ki az Eseményrácsot a Blockchain Workbench erőforráscsoportjából.

## <a name="using-service-bus-topics-for-notifications"></a>A Service Bus-témakörök használata értesítésekhez

A Service Bus-témakörök segítségével értesítheti a felhasználókat a Blockchain Workbench-ben bekövetkező eseményekről. 

1. Tallózással keresse meg a Service Bus a Workbench erőforráscsoporton belül.
2. Válassza **a Témakörök lehetőséget.**
3. Válassza **a kilépési témakör t.**
4. Hozzon létre egy új előfizetést ehhez a témakörhöz. Szerezzen be egy kulcsot hozzá.
5. Hozzon létre egy programot, amely feliratkozik az eseményekre ebből az előfizetésből.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Service Bus-üzenetek fogyasztása logikai alkalmazásokkal

1. Hozzon létre egy új **Azure Logic Alkalmazást** az Azure Portalon.
2. Amikor megnyitja az Azure Logic alkalmazást a portálon, a rendszer kérni fogja, hogy válasszon ki egy eseményindítót. Írja be a **Service Bus** kifejezést a keresőmezőbe, és válassza ki a service bus-szal kapcsolatban kívánt interakciótípusnak megfelelő eseményindítót. Például **a Service Bus – Amikor egy üzenet érkezik egy témakör-előfizetés (automatikus kiegészítés)**.
3. Amikor megjelenik a munkafolyamat-tervező, adja meg a service bus kapcsolati adatait.
4. Válassza ki az előfizetést, és adja meg a **munkaterület-külső**témakört.
5. Az alkalmazás logikájának fejlesztése, amely az eseményindító üzenetét használja.

## <a name="notification-message-reference"></a>Értesítési üzenet hivatkozása

Az **üzenetnév**üzenettől függően az értesítési üzenetek a következő üzenettípusok egyikével rendelkeznek.

### <a name="block-message"></a>Üzenet blokkolása

Az egyes blokkokra vonatkozó információkat tartalmaz. A *BlockMessage* tartalmaz egy blokkszintű információkat tartalmazó szakaszt, valamint egy tranzakciós adatokat tartalmazó szakaszt.

| Név | Leírás |
|------|-------------|
| letiltás | [Blokkadatokat](#block-information) tartalmaz |
| transactions | A blokk beszedési [tranzakciójának adatait](#transaction-information) tartalmazza |
| connectionId | A kapcsolat egyedi azonosítója |
| messageSchemaVersion | Üzenetküldési séma verziója |
| üzenetneve | **BlockMessage (BlokkÜzenet)** |
| további információk | További információk |

#### <a name="block-information"></a>Adatok letiltása

| Név              | Leírás |
|-------------------|-------------|
| blockId           | Az Azure Blockchain Workbench blokkjának egyedi azonosítója |
| blokkszám       | A főkönyv egy blokkjának egyedi azonosítója |
| blokkHash         | A blokk kivonata |
| előzőBlockHash | Az előző blokk kivonata |
| blockTimestamp blokkolás    | A blokk időbélyege |

#### <a name="transaction-information"></a>Tranzakció adatai

| Név               | Leírás |
|--------------------|-------------|
| tranzakcióazonosító      | Az Azure Blockchain Workbench-en belüli tranzakció egyedi azonosítója |
| tranzakcióHash    | A főkönyvben szereplő tranzakció kivonata |
| honnan               | A tranzakció eredetének főkönyvi egyedi azonosítója |
| erre:                 | A tranzakció céljának főkönyvi egyedi azonosítója |
| provisioningStatus | A tranzakció létesítési folyamatának aktuális állapotát határozza meg. Lehetséges értékek: </br>0 – A tranzakciót az API hozta létre az adatbázisban</br>1 – A tranzakció elküldve a főkönyvnek</br>2 – A tranzakció sikeresen véglegesítve lett a főkönyvben</br>3 vagy 4 - A tranzakció nem sikerült a főkönyvnek betartania</br>5 - A tranzakció sikeresen véglegesítve lett a főkönyvben |

Példa egy *BlockMessage-re* a Blockchain Workbench-ből:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
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

### <a name="contract-message"></a>Szerződési üzenet

Szerződéssel kapcsolatos információkat tartalmaz. Az üzenet tartalmaz egy szakaszt a szerződés tulajdonságaival, és egy szakaszt a tranzakcióadataival. Minden olyan tranzakció, amely módosította az adott blokkra vonatkozó szerződést, szerepel a tranzakciószakaszban.

| Név | Leírás |
|------|-------------|
| blockId | Az Azure Blockchain Workbench blokkjának egyedi azonosítója |
| blokkHash | A blokk kivonata |
| módosítás, Tranzakciók | A szerződést [módosító tranzakciók](#modifying-transaction-information) |
| szerződésazonosító | A szerződés egyedi azonosítója az Azure Blockchain Workbench-en belül |
| contractLedgerIdentifier | A főkönyvi szerződés egyedi azonosítója |
| contractProperties (szerződéses tulajdonságok) | [A szerződés tulajdonságai](#contract-properties) |
| isNewContract | Azt jelzi, hogy a szerződést újonnan hozták-e létre. A lehetséges értékek a következők: igaz: ez a szerződés egy új szerződés létrehozása volt. hamis: ez a szerződés a szerződés frissítése. |
| connectionId | A kapcsolat egyedi azonosítója |
| messageSchemaVersion | Üzenetküldési séma verziója |
| üzenetneve | **ContractMessage** |
| további információk | További információk |

#### <a name="modifying-transaction-information"></a>Tranzakcióadatok módosítása

| Név               | Leírás |
|--------------------|-------------|
| tranzakcióazonosító | Az Azure Blockchain Workbench-en belüli tranzakció egyedi azonosítója |
| tranzakcióHash | A főkönyvben szereplő tranzakció kivonata |
| honnan | A tranzakció eredetének főkönyvi egyedi azonosítója |
| erre: | A tranzakció céljának főkönyvi egyedi azonosítója |

#### <a name="contract-properties"></a>Szerződés tulajdonságai

| Név               | Leírás |
|--------------------|-------------|
| workflowPropertyId | Az Azure Blockchain Workbench munkafolyamat-tulajdonságának egyedi azonosítója |
| név | A munkafolyamat-tulajdonság neve |
| érték | A munkafolyamat-tulajdonság értéke |

Példa a Blockchain Workbench *ContractMessage* üzenetére:

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

### <a name="event-message-contract-function-invocation"></a>Eseményüzenet: Szerződés függvény meghívása

Szerződésfüggvény meghívásakor olyan információkat tartalmaz, mint például a függvény neve, a paraméterek bemenete és a függvény hívója.

| Név | Leírás |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| Hívó                      | [Hívó adatai](#caller-information) |
| szerződésazonosító                  | A szerződés egyedi azonosítója az Azure Blockchain Workbench-en belül |
| contractLedgerIdentifier    | A főkönyvi szerződés egyedi azonosítója |
| függvényneve                | A függvény neve |
| paraméterek                  | [Paraméteradatok](#parameter-information) |
| Tranzakció                 | Tranzakció adatai |
| inTransactionSequenceNumber | A blokkban lévő tranzakció sorozatszáma |
| connectionId                | A kapcsolat egyedi azonosítója |
| messageSchemaVersion        | Üzenetküldési séma verziója |
| üzenetneve                 | **EventMessage üzenet** |
| további információk       | További információk |

#### <a name="caller-information"></a>Hívó adatai

| Név | Leírás |
|------|-------------|
| type | A hívó típusa, például felhasználó vagy szerződés |
| id | Az Azure Blockchain Workbench-en belüli hívó egyedi azonosítója |
| ledgerIdentifier | A főkönyvben szereplő hívó egyedi azonosítója |

#### <a name="parameter-information"></a>Paraméteradatok

| Név | Leírás |
|------|-------------|
| név | Paraméter neve |
| érték | Paraméter értéke |

#### <a name="event-message-transaction-information"></a>Eseményüzenet tranzakciójának adatai

| Név               | Leírás |
|--------------------|-------------|
| tranzakcióazonosító      | Az Azure Blockchain Workbench-en belüli tranzakció egyedi azonosítója |
| tranzakcióHash    | A főkönyvben szereplő tranzakció kivonata |
| honnan               | A tranzakció eredetének főkönyvi egyedi azonosítója |
| erre:                 | A tranzakció céljának főkönyvi egyedi azonosítója |

Példa *egy EventMessage ContractFunctionInvocation metódusra* a Blockchain Workbench-ből:

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

### <a name="event-message-application-ingestion"></a>Eseményüzenet: Alkalmazás betöltése

Információkat tartalmaz, amikor egy alkalmazást feltöltanak a Workbench-be, például a feltöltött alkalmazás nevét és verzióját.

| Név | Leírás |
|------|-------------|
| eventName | **Alkalmazás: Alkalmazás** |
| applicationId | Az Azure Blockchain Workbench alkalmazásának egyedi azonosítója |
| applicationName | Alkalmazásnév |
| alkalmazásDisplayName | Alkalmazás megjelenítendő neve |
| alkalmazásverzió | Alkalmazás verziója |
| applicationDefinitionLocation | Az alkalmazáskonfigurációs fájlt azonosító URL-cím |
| szerződéskódok | A kérelem [szerződéskódjainak](#contract-code-information) beszedése |
| applicationRoles | Az [alkalmazásszerepkörök](#application-role-information) gyűjteménye |
| alkalmazásMunkafolyamatok | Az alkalmazás [munkafolyamatainak](#application-workflow-information) gyűjteménye |
| connectionId | A kapcsolat egyedi azonosítója |
| messageSchemaVersion | Üzenetküldési séma verziója |
| üzenetneve | **EventMessage üzenet** |
| további információk | Az itt megadott további információk közé tartoznak az alkalmazás munkafolyamat-állapotai és az átmeneti információk. |

#### <a name="contract-code-information"></a>Szerződéskód adatai

| Név | Leírás |
|------|-------------|
| id | Az Azure Blockchain Workbench-en belüli szerződéskódfájl egyedi azonosítója |
| ledgerId | Az Azure Blockchain Workbench főkönyvének egyedi azonosítója |
| location | A szerződéskódfájlt lakó URL-cím |

#### <a name="application-role-information"></a>Alkalmazásszerepkör-információk

| Név | Leírás |
|------|-------------|
| id | Az Azure Blockchain Workbench alkalmazásszerepkörének egyedi azonosítója |
| név | Az alkalmazásszerepkör neve |

#### <a name="application-workflow-information"></a>Alkalmazásmunkafolyamat-adatok

| Név | Leírás |
|------|-------------|
| id | Az Azure Blockchain Workbench alkalmazásmunkafolyamatának egyedi azonosítója |
| név | Alkalmazásmunkafolyamat neve |
| displayName | Alkalmazásmunkafolyamat megjelenítendő neve |
| Funkciók | Az [alkalmazásmunkafolyamat funkcióinak](#workflow-function-information) gyűjtése|
| Államok | Az [alkalmazás-munkafolyamat állapotainak](#workflow-state-information) gyűjteménye |
| properties | Alkalmazásmunkafolyamat [tulajdonságainak adatai](#workflow-property-information) |

##### <a name="workflow-function-information"></a>Munkafolyamat-függvény adatai

| Név | Leírás |
|------|-------------|
| id | Az Azure Blockchain Workbench alkalmazásmunkafolyamat-függvényének egyedi azonosítója |
| név | Függvény neve |
| paraméterek | A függvény paraméterei |

##### <a name="workflow-state-information"></a>Munkafolyamat állapotadatai

| Név | Leírás |
|------|-------------|
| név | Állam neve |
| displayName | Állapot megjelenítési neve |
| stílus | Állami stílus (sikeres vagy sikertelen) |

##### <a name="workflow-property-information"></a>Munkafolyamat-tulajdonság adatai

| Név | Leírás |
|------|-------------|
| id | Az Azure Blockchain Workbench alkalmazásmunkafolyamat-tulajdonságának egyedi azonosítója |
| név | Tulajdonság neve |
| type | Tulajdonság típusa |

Példa *egy EventMessage ApplicationIngestion-ra* a Blockchain Workbench-ből:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationDefinitionLocation": "http://url",
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
    ],
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
                            "DisplayName": "Accept",
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

### <a name="event-message-role-assignment"></a>Eseményüzenet: Szerepkör-hozzárendelés

Információkat tartalmaz, ha egy felhasználóhoz szerepkör t rendeltek a Workbench-ben, például azt, hogy ki hajtotta végre a szerepkör-hozzárendelést, valamint a szerepkör és a megfelelő alkalmazás nevét.

| Név | Leírás |
|------|-------------|
| eventName | **Szerepkör-hozzárendelés** |
| applicationId | Az Azure Blockchain Workbench alkalmazásának egyedi azonosítója |
| applicationName | Alkalmazásnév |
| alkalmazásDisplayName | Alkalmazás megjelenítendő neve |
| alkalmazásverzió | Alkalmazás verziója |
| applicationRole        | Az [alkalmazásszerepkörre](#roleassignment-application-role) vonatkozó információk |
| hozzárendelő               | Az [engedményezővel](#roleassignment-assigner) kapcsolatos információk |
| Megbízott               | Az [engedményesre](#roleassignment-assignee) vonatkozó információk |
| connectionId           | A kapcsolat egyedi azonosítója |
| messageSchemaVersion   | Üzenetküldési séma verziója |
| üzenetneve            | **EventMessage üzenet** |
| további információk  | További információk |

#### <a name="roleassignment-application-role"></a>Szerepkör-hozzárendelés-alkalmazás szerepkör-szerepkör

| Név | Leírás |
|------|-------------|
| id | Az Azure Blockchain Workbench alkalmazásszerepkörének egyedi azonosítója |
| név | Az alkalmazásszerepkör neve |

#### <a name="roleassignment-assigner"></a>Szerepkör-hozzárendelés-hozzárendelt

| Név | Leírás |
|------|-------------|
| id | A felhasználó egyedi azonosítója az Azure Blockchain Workbench-en belül |
| type | Az engedményező típusa |
| chainIdentifier | A főkönyvben szereplő felhasználó egyedi azonosítója |

#### <a name="roleassignment-assignee"></a>Szerepkör-hozzárendelés hozzárendelt

| Név | Leírás |
|------|-------------|
| id | A felhasználó egyedi azonosítója az Azure Blockchain Workbench-en belül |
| type | A megbízott típusa |
| chainIdentifier | A főkönyvben szereplő felhasználó egyedi azonosítója |

Példa *egy EventMessage roleAssignment-re* a Blockchain Workbench-ből:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
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

- [Intelligens szerződések integrációs mintái](integration-patterns.md)

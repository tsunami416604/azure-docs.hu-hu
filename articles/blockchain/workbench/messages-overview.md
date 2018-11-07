---
title: Az Azure Blockchain Workbench üzenetek integráció áttekintése
description: Üzenetek használata az Azure Blockchain Workbench áttekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: b4a816c887d1cca78ff845858dce29049946b09f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235989"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Az Azure Blockchain Workbench integrációs üzenetkezelés

Amellett, hogy a REST API-val, az Azure Blockchain Workbench is messaging-alapú integrációt biztosít. Workbench Főkönyv-központú események Azure Event Griddel, alsóbb rétegbeli fogyasztói gyűjthet adatokat, vagy be ezen események alapján keresztül teszi közzé. Ezek az ügyfelek megbízható üzenetküldést igénylő, az Azure Blockchain Workbench használatával továbbítja az üzeneteket, valamint egy Azure Service Bus-végponttal.

A fejlesztők is érdeklődést a külső rendszerek kommunikációhoz felhasználók létrehozása, szerződések létrehozása és frissítése a Főkönyv szerződések kezdeményezési tranzakciók vannak. Ez a funkció jelenleg nem érintkező nyilvános előzetes verzióban érhető el, amíg egy mintát, amely ezt a képességet biztosít fürtpéldány [ http://aka.ms/blockchain-workbench-integration-sample ](https://aka.ms/blockchain-workbench-integration-sample).

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
| ChainID | A láncban, a kéréshez társított egyedi azonosítója.|
| BlockId | A blokkolás a a Főkönyv egyedi azonosítója.|
| ContractId | A szerződés egyedi azonosítója.|
| ContractAddress |       A szerződést a Főkönyv címe.|
| TransactionHash  |     A tranzakció a főkönyvi kivonat.|
| OriginatingAddress |   A tranzakció a feladó címe.|
| Műveletnév       |     A művelet neve.|
| IsUpdate        |      Annak jelzése, hogy ez a frissítés.|
| Paraméterek       |     Az objektum, amely azonosítja a név, érték és adatok művelet küldött paraméterek listáját.|
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
| ContractActionId         | A szerződés művelet egyedi azonosítója                                                                                                                                |
| ChainIdentifier          | A láncban egyedi azonosítója                                                                                                                                           |
| ConnectionId             | A kapcsolat egyedi azonosítója                                                                                                                                      |
| UserChainIdentifier      | A blockchain-hálózat létrehozó felhasználó címe. Az Ethereum ez lenne a felhasználó "a lánc" címet.                                                     |
| ContractLedgerIdentifier | A szerződést a Főkönyv címe.                                                                                                                                        |
| WorkflowFunctionName     | A munkafolyamat függvény nevét.                                                                                                                                                |
| WorkflowName             | A munkafolyamat nevét.                                                                                                                                                         |
| WorkflowBlobStorageURL   | Az URL-címe a blob Storage-szerződést.                                                                                                                                      |
| ContractActionParameters | A szerződés művelet paramétereit.                                                                                                                                           |
| TransactionHash          | A tranzakció a főkönyvi kivonat.                                                                                                                                    |
| Kiépítési állapot      | A művelet aktuális kiépítési állapota.</br>0 – létrehozva</br>1 – folyamatban</br>2 – befejezése</br> Teljes azt jelzi, hogy a könyvelési megerősítést, amelyek ezen sikeresen hozzá lett adva.                                               |
|                          |                                                                                                                                                                               |

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
| Cím | A felhasználót, hogy a rendszer finanszírozott címe. |
| Egyenleg | A felhasználó egyenleg egyenleg.         |
| ChainID | A láncban egyedi azonosítója.     |


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
| ChainId        | Egyedi azonosítója, amelyhez a blokk lett hozzáadva a lánc.             |
| BlockId        | Az Azure Blockchain Workbench blokk egyedi azonosítója. |
| BlockHash      | A blokk-kivonat.                                                 |
| BlockTimeStamp | A blokk időbélyegét.                                            |

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
| ChainId         | Egyedi azonosítója, amelyhez a blokk lett hozzáadva a lánc.             |
| BlockId         | Az Azure Blockchain Workbench blokk egyedi azonosítója. |
| TransactionHash | A tranzakció kivonatát.                                           |
| Ettől:            | A tranzakció a feladó címe.                      |
| Művelet              | A tranzakció az illetékes címzett címe.              |
| Érték           | Az érték a tranzakcióban.                                 |
| IsAppBuilderTx  | Ha ez az a Blockchain Workbenchet tranzakció azonosítja.                         |

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
| ContractId      | Ez az Azure Blockchain Workbench a szerződés egyedi azonosítója. |
| ChainIdentifier | Ez az a szerződés a lánc azonosítóját.                             |

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
| OperationName | A művelet neve.           |
| Kérelemazonosító:     | A kérelem egyedi azonosítója. |

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
| Name (Név)  | A paraméter neve.  |
| Érték | A paraméter értéke. |
| Típus  | A paraméter típusát.  |

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
| Azonosító    | A tulajdonság azonosítója.    |
| Name (Név)  | A tulajdonság nevét.  |
| Érték | A tulajdonság értéke. |
| Típus  | A tulajdonság típusa.  |

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
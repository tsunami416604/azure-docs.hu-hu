---
title: Az Azure Blockchain munkaterület üzenetek áttekintése
description: Üzenetek Azure Blockchain munkaterület használatának áttekintése.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 4a2e85cc619d17745be9d8f72af5f99049ce7c6b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="azure-blockchain-workbench-messages-overview"></a>Az Azure Blockchain munkaterület üzenetek áttekintése

Csupán a REST API-t, Azure Blockchain munkaterület messaging-alapú integrációt is biztosít. Munkaterület közzéteszi a főkönyvi-központú események keresztül Azure esemény rács, alsóbb rétegbeli fogyasztói betöltik az adatokat, vagy hajtsa végre a műveletet ezen események alapján. Reliable üzenetküldést igénylő ügyfelek Azure Blockchain munkaterület kézbesíti üzenetek, valamint az Azure Service Bus-végpont.

A fejlesztők is érdeklődést a a lehetővé teszi, hogy a felhasználók létrehozásához, szerződések létrehozása és frissítése a főkönyvi szerződések kezdeményezhet tranzakciók kommunikációhoz külső rendszerekkel. Amíg ez a funkció jelenleg nincs felfedve nyilvános előzetes verziójában, ezt a funkciót nyújt minta található a [ http://aka.ms/blockchain-workbench-integration-sample ](http://aka.ms/blockchain-workbench-integration-sample).


## <a name="event-notifications"></a>Eseményértesítések

Eseményértesítések értesíteni a felhasználókat és alsóbb rétegbeli rendszerét eseményeket munkaterület és a blockchain hálózathoz van csatlakoztatva, használható. Eseményértesítések használhatja közvetlenül a kódban vagy alsóbb rétegbeli rendszerekre adatáramlásra elindítása az eszközöket, például a Logic Apps és a folyamat használatával.

Lásd: [értesítési üzenet hivatkozás](#notification-message-reference) különböző fogadott üzenetek részleteit.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Az Azure Functions rács eseményeinek felhasználása

Ha a felhasználó esemény rács használni szeretne értesítést Blockchain munkaterület eseményeket szeretné, az Azure Functions használatával is használja a esemény rács származó események.

1. Hozzon létre egy **Azure függvény App** az Azure portálon.
2. Hozzon létre egy új függvényt.
3. Keresse meg a sablon esemény rács. Egyszerű sablon az üzenet olvasása kód. Szükség szerint módosítsa a kódot.
4. A függvény mentése. 
5. Válassza ki az esemény rács Blockchain munkaterület erőforráscsoportból.

### <a name="consuming-event-grid-events-with-logic-apps"></a>A Logic Apps rács eseményeinek felhasználása

1.  Hozzon létre egy új **Azure Logic Apps** az Azure portálon.
2.  Az Azure Logic Apps a portál megnyitásakor kérni fogja eseményindító kiválasztásához. Válassza ki **Azure esemény rács--resource-esemény esetén**.
3. Amikor megjelenik a munkafolyamat-tervezőben, kérni fogja a bejelentkezéshez.
4. Válassza ki az előfizetést. Erőforrás **Microsoft.EventGrid.Topics**. Válassza ki a **erőforrásnév** , az erőforrást az Azure Blockchain munkaterület erőforráscsoport nevétől.
5. Válassza ki az esemény rács Blockchain munkaterület erőforráscsoportból.

## <a name="using-service-bus-topics-for-notifications"></a>Az értesítések Service Bus-üzenettémakörök használata

Service Bus-üzenettémakörök segítségével értesítheti a felhasználókat kapcsolatos Blockchain munkaterület eseményeket. 

1.  Tallózással keresse meg a Service Bus a munkaterület-erőforráscsoporton belül.
2.  Válassza ki **témakörök**.
3.  Válassza ki **munkaterület-külső**.
4.  Hozzon létre egy új előfizetés ehhez a témakörhöz. Egy kulcs az beszerzése.
5.  Hozzon létre egy programot, amely események ettől az előfizetéstől számítógépcsoportra fizetett elő.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Service Bus üzenetek a Logic Apps felhasználása

1. Hozzon létre egy új **Azure Logic Apps** az Azure portálon.
2.  Az Azure Logic Apps a portál megnyitásakor kérni fogja eseményindító kiválasztásához. Típus **Service Bus** azokat a keresési mezőbe, majd válassza ki a megfelelő interakció típusú eseményindító kíván használni a Service busszal. Például **– amikor egy üzenet jelenik meg a témakör az előfizetéshez (automatikusan hajthat végre), a Service Bus**.
3. Amikor megjelenik a munkafolyamat-tervezőben, adja meg a Service Bus kapcsolati adatait.
4. Jelölje ki az előfizetését, és adja meg a témakör a **munkaterület-külső**.
5. Fejleszthet, amelyek ehhez az eseményindítóhoz üzenetét használja az alkalmazás logikáját.

## <a name="notification-message-reference"></a>Értesítési üzenet referencia

Attól függően, hogy az OperationName az értesítési üzenetek rendelkezik a következő üzenet típusú.

### <a name="accountcreated"></a>AccountCreated

Azt jelzi, hogy az új fiók a megadott lánc hozzáadandó kért-e.

| Name (Név)    | Leírás  |
|----------|--------------|
| Felhasználói azonosító  | A létrehozott felhasználó azonosítója |
| ChainIdentifier | A felhasználó létrehozásának blockchain hálózati címe. A Ethereum ez lenne a felhasználó "a lánc" címét. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Azt jelzi, hogy a kérelem nem lett végrehajtva beszúrása vagy frissítése egy elosztott főkönyvi egy szerződést.

| Name (Név) | Leírás |
|-----|--------------|
| ChainID | A kérelemhez társított lánc egyedi azonosítója.|
  BlockId | A blokkolás a főkönyvi egyedi azonosítója.|
  ContractId | A szerződés egyedi azonosítója.|
  ContractAddress |       A szerződést a főkönyvi címe.|
  TransactionHash  |     A tranzakció a főkönyvi kivonatát.|
  OriginatingAddress |   A tranzakció a feladó címe.|
  Műveletnév       |     A művelet neve.|
  IsUpdate        |      Azt jelzi, hogy ez a frissítés.|
  Paraméterek       |     Azonosító művelet küldött paraméterek nevét, az érték és az adatok típusú objektumok listája.|
  TopLevelInputParams |  Olyan esetekben, ahol a szerződés csatlakozik egy vagy több szerződést ezek a paraméterek a legfelső szintű szerződésből. |

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

Azt jelzi, hogy a kérelem nem lett végrehajtva a végrehajtás során egy adott szerződés egy elosztott főkönyvi egy műveletet.

| Name (Név)                     | Leírás                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | A szerződés művelet egyedi azonosítója                                                                                                                                |
| ChainIdentifier          | A lánc egyedi azonosítója                                                                                                                                           |
| ConnectionId             | A kapcsolat az egyedi azonosítója                                                                                                                                      |
| UserChainIdentifier      | A felhasználó létrehozásának blockchain hálózati címe. A Ethereum ez lenne a felhasználó "a lánc" címét.                                                     |
| ContractLedgerIdentifier | A szerződést a főkönyvi címe.                                                                                                                                        |
| WorkflowFunctionName     | A munkafolyamat függvény neve.                                                                                                                                                |
| WorkflowName             | A munkafolyamat nevét.                                                                                                                                                         |
| WorkflowBlobStorageURL   | A szerződés a blob Storage tárolóban URL-címét.                                                                                                                                      |
| ContractActionParameters | A szerződés művelet paramétereit.                                                                                                                                           |
| TransactionHash          | A tranzakció a főkönyvi kivonatát.                                                                                                                                    |
| Kiépítési állapot      | A művelet aktuális üzembe helyezési állapota.</br>0 – létrehozása</br>1 – folyamat</br>2 – befejezése</br> Teljes jelzi a könyvelési igazolja, hogy a rendszer sikeresen hozzáadta.                                               |
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

Azt jelzi, hogy a kérelem nem lett végrehajtva egy meghatározott elosztott főkönyvi felhasználói egyenlege frissítése.

> [!NOTE]
> Ez az üzenet csak ezek a fiókok kiválasztását igénylő főkönyvi létrejön.
> 

| Name (Név)    | Leírás                              |
|---------|------------------------------------------|
| Cím | A felhasználót, hogy a rendszer támogatott címe. |
| Egyensúly | A felhasználó egyenleg egyenlege.         |
| ChainID | A lánc egyedi azonosítója.     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

Az üzenet azt jelzi, hogy egy kérelem nem lett végrehajtva egy elosztott főkönyvi hozzáadásához.

| Name (Név)           | Leírás                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | Az egyedi azonosítója, amelyhez hozzá lett adva a blokkot a lánc.             |
| BlockId        | A blokkon belül Azure Blockchain munkaterület egyedi azonosítója. |
| BlockHash      | A blokk kivonatát.                                                 |
| BlockTimeStamp | A blokk időbélyegző.                                            |

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

Üzenet részleteit adja hozzá a tranzakció elosztott főkönyvi a kérést.

| Name (Név)            | Leírás                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | Az egyedi azonosítója, amelyhez hozzá lett adva a blokkot a lánc.             |
| BlockId         | A blokkon belül Azure Blockchain munkaterület egyedi azonosítója. |
| TransactionHash | A tranzakció kivonatát.                                           |
| Ettől:            | A tranzakció a feladó címe.                      |
| Művelet              | A tranzakció az illetékes címzett címe.              |
| Érték           | Az érték a tranzakcióban.                                 |
| IsAppBuilderTx  | Azt jelzi, hogy ez a Blockchain munkaterület tranzakció.                         |

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

Részletes adatokat biztosít a hozzárendelés egy lánc azonosító egy szerződést. Például a Ethereum blockchain, a cím a főkönyvi a szerződés.

| Name (Név)            | Leírás                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Ez az Azure Blockchain munkaterület belül a szerződés egyedi azonosítója. |
| ChainIdentifier | Ez az azonosítóját, a lánc szerződés.                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Intelligens szerződés integrációs minták](blockchain-workbench-integration-patterns.md)
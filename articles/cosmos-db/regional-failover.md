---
title: Az Azure Cosmos Adatbázisba regionális feladatátvétel |} Microsoft Docs
description: Tudnivalók Azure Cosmos DB hogyan manuális és automatikus feladatátvételt együttműködik.
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 977027de0627a6eca5f9eb5d1ab83dea5347c6d4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Az üzletmenet folytonossága érdekében az Azure Cosmos Adatbázisba automatikus regionális feladatátvétel
Azure Cosmos DB egyszerűbbé teszi a globális adatok terjesztési felajánlásával teljes körűen felügyelt, [több területi adatbázis fiókok](distribute-data-globally.md) biztosító egyértelmű mellékhatásokkal konzisztencia, a rendelkezésre állás és a teljesítmény, az összes megfelelő garanciák között. Cosmos DB fiókok kínálnak a magas rendelkezésre állású, egyetlen számjegy ms késések, [jól meghatározott konzisztenciaszintek](consistency-levels.md), többhelyű API-khoz transzparens regionális feladatátvétel és rugalmasan méretezhető átviteli sebesség és tárterület világszerte képességét. 

Cosmos DB egyaránt explicit támogat, és a feladatátvétel vezérelt házirend, amely lehetővé teszi a végpont rendszerműködést ellenőrzési hibák esetén. Ebben a cikkben úgy tekintünk:

* Hogyan működnek a manuális feladatátvételt az Adatbázisba az Cosmos?
* Hogyan tegye Cosmos DB automatikus feladatátvétel munka és mi történik, ha egy data center álljon le?
* Hogyan használhatók kézi feladatátvételt alkalmazási architektúrákban található?

Ez a videó bemutatja a globális terjesztési funkciót, beleértve a regionális feladatátvétel Azure Cosmos DB Programvezető Andrew Liu által regionális feladatátvétel is olvashat.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

## <a id="ConfigureMultiRegionApplications"></a>Több területi alkalmazások konfigurálása
Ahhoz, hogy alaposabban feladatátvételi mód, úgy tekintünk, hogyan konfigurálhat egy alkalmazás több régiónkénti elérhetőség előnyeinek kihasználása és a regionális feladatátvétel állásuk rugalmasak lehetnek.

* Először is telepítse az alkalmazás több régióba
* Ahhoz, hogy kis késésű hozzáférést minden régióban, az alkalmazás központi telepítése, konfigurálása a megfelelő [előnyben részesített régiók listáját](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) keresztül valamelyik támogatott SDK-k mindegyik régióhoz.

Az alábbi részlet egy több területi alkalmazás inicializálása mutatja be. Itt, az Azure Cosmos DB fiók `contoso.documents.azure.com` két régió - USA nyugati régiója, Észak-Európa, és van konfigurálva. 

* Az alkalmazás központi telepítése (például az Azure App Services) régióban USA nyugati régiója 
* A konfigurált `West US` beolvassa az alacsony késleltetés első preferált régió szerint
* A konfigurált `North Europe` , a második preferált régió (magas rendelkezésre állásúként regionális esetén)

Az SQL API-ban Ez a konfiguráció néz ki a következő kódrészletet:

```cs
ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
{ 
    ConnectionMode = ConnectionMode.Direct,
    ConnectionProtocol = Protocol.Tcp
};

usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

DocumentClient usClient = new DocumentClient(
    new Uri("https://contosodb.documents.azure.com"),
    "<Fill your Cosmos DB account's AuthorizationKey>",
    usConnectionPolicy);
```

Észak-Európa régióban előnyben részesített régiók fordított sorrendben is telepítik az alkalmazást. Ez azt jelenti, hogy az Észak-Európa régió van megadva először kis késleltetésű olvasása. Ezt követően az USA nyugati régiója régió van megadva a magas rendelkezésre állású második preferált régió regionális esetén.

Az alábbi architektúra ábrán látható a több területi alkalmazástelepítést, ahol Cosmos DB és az alkalmazás vannak konfigurálva a négy Azure földrajzi régióban elérhető legyen.  

![Azure Cosmos DB globálisan elosztott alkalmazás központi telepítése](./media/regional-failover/app-deployment.png)

Most hogyan kezeli a Cosmos DB szolgáltatás a regionális meghibásodásokkal keresztül automatikus feladatátvétel vizsgáljuk meg. 

## <a id="AutomaticFailovers"></a>Automatikus feladatátvétel
Egy Azure regionális kimaradás vagy az Adatközpont-meghibásodás után a ritka esetben Cosmos DB automatikusan elindítja a feladatátvételeket a Cosmos DB fiókoknak minden érintett régióban jelenlét. 

**Mi történik, ha egy olvasási régió kimaradás?**

Az érintett régiók egyikéhez sem olvasható régió cosmos DB fiókok automatikusan bontotta a kapcsolatot az írási régió és offline megjelölve. A Cosmos DB SDK-k egy regionális felderítési protokoll, amellyel automatikusan észlelés, ha egy régióban elérhetővé válik, és átirányítási, olvassa el a következő rendelkezésre álló terület a preferált régió listában hívásainak valósítja meg. Ha az elsődleges régióban listában régiók nincs ilyen, hívások automatikusan térhet vissza az aktuális írási területen. Nincs szükség módosításra az alkalmazás kódjában regionális feladatátvétel kezelésére. Ez a folyamat konzisztencia biztosítja továbbra is Cosmos DB kell ezt a funkciót.

![Olvassa el az Azure Cosmos DB régió hibái](./media/regional-failover/read-region-failures.png)

Az érintett régió helyreállít a a leállás, miután a rendszer automatikusan visszaállítja az érintett Cosmos DB fiókokhoz régióban a szolgáltatás. Cosmos DB fiókok, amelyekről egy olvasási területet az érintett területen, majd automatikusan aktuális írási terület szinkronizálhat és online kapcsolja. A Cosmos DB SDK-k Fedezze fel az új régió rendelkezésre állását, és mérlegelje, hogy a régióban kell kiválasztani, mint a jelenlegi olvasási terület, az alkalmazás által konfigurált preferált régió listája. További olvasási az alkalmazás kódjának módosítása nélkül a helyreállított régió van átirányítva.

**Mi történik, ha egy írási régió kimaradás?**

Ha az érintett régió az aktuális írási régió, és automatikus feladatátvételt az Azure Cosmos DB fiók engedélyezve van, majd a régió automatikusan jelölése offline állapotúként. Egy másik régióban majd, mint a írási terület, az érintett Azure Cosmos DB fiók van előléptetve. Automatikus feladatátvétel és a teljes irányítása a régió kijelölés ahhoz, hogy az Azure Cosmos DB fiókok az Azure-portálon vagy [programozott módon](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Azure Cosmos DB feladatátvételi prioritásait](./media/regional-failover/failover-priorities.png)

Automatikus feladatátvételek során Azure Cosmos DB automatikusan kiválasztja azt a következő Azure Cosmos DB alapuló a megadott prioritás partner régió írni. Alkalmazások DocumentClient osztály WriteEndpoint tulajdonságának használatával észleli a változást írási régióban.

![Írja be Azure Cosmos DB régió hibák](./media/regional-failover/write-region-failures.png)

Az érintett régió helyreállít a a leállás, miután a rendszer automatikusan visszaállítja az érintett Cosmos DB fiókokhoz régióban a szolgáltatás. 

* Az előző írási régióban, amely a rendszer nem replikálja a rendszer régiók olvasása során a szolgáltatáskimaradás adatok hírcsatorna ütközés van közzé. Alkalmazások olvassa el az ütköző adatcsatorna, hárítsa el a problémákat, alkalmazás-specifikus logika alapján, és a frissített adatokat írhat vissza a Azure Cosmos DB account. 
* Az előző írási régió újból egy olvasási területet, és automatikusan automatikusan visszaáll online állapotba. 
* Olyan módon konfigurálhatja újra lett válik újra online állapotúvá automatikusan az írási régió, egy manuális feladatátvételt az Azure-portálon elvégzésével olvasási régiót vagy [programozott módon](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

A következő kódrészletet szemlélteti, hogyan kell feldolgozni ütközések, az érintett régió helyreállít a a szolgáltatáskimaradás elhárítása után.

```cs
string conflictsFeedContinuationToken = null;
do
{
    FeedResponse<Conflict> conflictsFeed = client.ReadConflictFeedAsync(collectionLink,
        new FeedOptions { RequestContinuation = conflictsFeedContinuationToken }).Result;

    foreach (Conflict conflict in conflictsFeed)
    {
        Document doc = conflict.GetResource<Document>();
        Console.WriteLine("Conflict record ResourceId = {0} ResourceType= {1}", conflict.ResourceId, conflict.ResourceType);

        // Perform application specific logic to process the conflict record / resource
    }

    conflictsFeedContinuationToken = conflictsFeed.ResponseContinuation;
} while (conflictsFeedContinuationToken != null);
```

## <a id="ManualFailovers"></a>Manuális feladatátvétel

Automatikus feladatátvétel mellett az aktuális írási terület a megadott Cosmos DB fiók manuálisan módosítható dinamikusan a meglévő olvasási régiók egyikéhez sem. Kézi feladatátvételt is kezdeményezhető az Azure-portálon vagy [programozott módon](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Manuális feladatátvétel biztosítására **adatvesztés nulla** és **nulla rendelkezésre állási** adatvesztéssel és szabályosan átviteli írási állapotát a régi régió írni a megadott Cosmos adatbázis fiók egy. Például az Automatikus feladatátvétel, a Cosmos DB SDK automatikusan kezeli az írási régió módosítások manuális feladatátvételek során és biztosítja, hogy a rendszer automatikusan átirányítja hívások az új írási terület. Az alkalmazása kezelje a feladatátvételek kód vagy a konfigurációs módosítások nélküli kell megadni. 

![Kézi feladatátvételt az Azure Cosmos-Adatbázisba](./media/regional-failover/manual-failovers.png)

A gyakori forgatókönyvek, ahol a kézi feladatátvételre hasznos lehet a következők:

**Hajtsa végre az óra modell**: Ha az alkalmazások kiszámítható forgalmi minták alapján a időpontját, rendszeres időközönként a írási állapotát módosíthatja a legtöbb aktív terület a mai időpont alapján.

**Szolgáltatásfrissítés**: bizonyos globálisan elosztott alkalmazás központi telepítésének magukban foglalhatják a forgalom átirányítását a traffic manager segítségével különböző régióban a tervezett szolgáltatás frissítése közben. Ilyen alkalmazás központi telepítése most kézi feladatátvételre használhatja arra, hogy az írási állapota a régió, ahol nem lesz aktív forgalom a szolgáltatás frissítési időszakban.

**Az üzletmenet folytonosságának és a vész-helyreállítási (BCDR) és a magas rendelkezésre állású és vész-helyreállítási (HADR) csukja**: a legtöbb vállalati alkalmazások tartalmazzák a üzleti folytonossági a fejlesztési és kiadás folyamat részeként. Gyakran a megfelelőségi tanúsítványokról és garanciavállaló szolgáltatás rendelkezésre állása regionális kimaradások esetén fontos lépés BCDR és a HADR tesztelése. Az alkalmazások, amelyek Cosmos DB használjanak tárhelyként Cosmos DB fiókja manuális feladatátvétel kiváltása és/vagy hozzáadása és eltávolítása dinamikusan terület által BCDR készültségét tesztelheti.

Ez a cikk azt felül Cosmos DB hogyan manuális és automatikus feladatátvételt munka, és hogyan konfigurálhatja a Cosmos DB fiókok és az alkalmazások globálisan elérhető legyen. Globális replikációs támogatási Cosmos DB használatával végpontok közötti késés javítása, és, hogy azok magas rendelkezésre állású még régió hibák esetén. 

## <a id="NextSteps"></a>További lépések
* További tudnivalók arról, hogyan támogatja a Cosmos DB [globális terjesztési](distribute-data-globally.md)
* További tudnivalók [az Azure Cosmos DB globális egységesítése](consistency-levels.md)
* Azure Cosmos DB használatával több régióba fejlesztést [SQL API](tutorial-global-distribution-sql-api.md)
* Ismerje meg, hogyan hozhat létre [több területi író architektúrák](multi-region-writers.md) rendelkező Azure Cosmos DB


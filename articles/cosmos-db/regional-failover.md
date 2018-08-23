---
title: Az Azure Cosmos DB-ben regionális feladatátvétel |} A Microsoft Docs
description: Ismerje meg, hogyan manuális és automatikus feladatátvételi működését az Azure Cosmos DB használatával.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: govindk
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 697be3a1eb07b2f2650f3dd94fd835b9431aec6b
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42058095"
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Az Azure Cosmos DB az üzletmenet folytonosságának automatikus regionális feladatátvétel
Az Azure Cosmos DB leegyszerűsíti az adatok a globális terjesztés, ajánlat teljes körűen felügyelt, [többrégiós adatbázisfiókhoz](distribute-data-globally.md) , adja meg a clear és kompromisszumot kínál a konzisztencia, a rendelkezésre állás és teljesítmény, a megfelelő között garantálja. Cosmos DB-fiókok egyetlen számjegy ms késések, magas rendelkezésre állást kínálnak [jól definiált konzisztenciaszintet](consistency-levels.md), többkiszolgálós API-kkal transzparens regionális feladatátvételt, valamint a rugalmasan méretezhető átviteli sebesség és a tárolás a világon. 

A cosmos DB támogatja mindkét explicit és feladatátvételeket vezérelt házirend, amely lehetővé teszi a teljes körű rendszer ellenőrzési hibák esetén. Ebben a cikkben áttekintjük:

* Hogyan működnek a manuális feladatátvételt Cosmos DB-ben?
* Hogyan hajtsa végre az automatikus feladatátvételek munkahelyi Cosmos DB-ben és mi történik, ha egy data center goes le?
* Hogyan használhatók manuális feladatátvételt az architektúrák?

Ez a videó bemutatja a globális terjesztés funkciókat, beleértve a regionális feladatátvétel az Azure Cosmos DB Programigazgatója Andrew Liu által aktivált regionális feladatátvétel is tudnivalók.

>[!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

## <a id="ConfigureMultiRegionApplications"></a>Többrégiós alkalmazások konfigurálása
Mielőtt nekikezdenénk a feladatátvételi módot, áttekintjük, hogyan konfigurálhat egy alkalmazás több régióban rendelkezésre állás előnyeit, és rugalmas aktivált regionális feladatátvétel esetén.

* Először is telepítse az alkalmazás több régióban
* Ahhoz, hogy kis késésű hozzáférést minden régióban, az alkalmazás központi telepítése, konfigurálása a megfelelő [preferált régiók listája](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) keresztül a támogatott SDK-k közül mindegyik régióhoz.

Az alábbi kódrészlet bemutatja, hogyan egy többrégiós alkalmazás inicializálása. Itt, az Azure Cosmos DB-fiók `contoso.documents.azure.com` két régió – USA nyugati Régiójában és Észak-Európában van konfigurálva. 

* Az alkalmazás van üzembe helyezve az USA nyugati régiója (Azure App Services használatával például) 
* A konfigurált `West US` beolvassa az alacsony késés érdekében először előnyben részesített régió szerint
* A konfigurált `North Europe` , a második elsődleges régióban (a magas rendelkezésre állás érdekében regionális meghibásodások esetén)

Az SQL API-ban Ez a konfiguráció az alábbi kódrészlethez hasonlóan néz ki:

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

Az alkalmazás is telepítve van az elsődleges régióban, fordított sorrendjét észak-európai régióban. Azt jelenti az észak-európai régióban van megadva először a kis késleltetésű olvasásokhoz. Ezt követően az USA nyugati régiója van megadva, a második előnyben részesített régióban a magas rendelkezésre állás érdekében regionális meghibásodások esetén.

Az alábbi Architektúradiagram bemutatja az egy többrégiós alkalmazás központi telepítésének, Cosmos DB és az alkalmazás konfigurálva van négy Azure földrajzi régiókban érhető el.  

![Azure Cosmos DB globálisan elosztott alkalmazás telepítése](./media/regional-failover/app-deployment.png)

Most nézzük, hogyan a Cosmos DB szolgáltatás végzi el az automatikus feladatátvételek keresztül regionális meghibásodások esetén. 

## <a id="AutomaticFailovers"></a>Az Automatikus feladatátvétel
Az esemény ritkán fordul elő az Azure regionális kimaradás vagy az Adatközpont-meghibásodás után, a Cosmos DB automatikusan aktiválja a feladatátvételt, az érintett régióban telephellyel rendelkező összes Cosmos DB-fiókokhoz. 

**Mi történik, ha egy olvasási régióval rendelkezik kimaradás?**

Cosmos DB-fiókok egy olvasási régióban az érintett régiók egyikében automatikusan bontotta a kapcsolatot az írási régió és offline állapotban van megjelölve. A Cosmos DB SDK-k egy regionális felderítési protokoll, amellyel automatikusan észlelni, ha egy régióban érhető el, és átirányítási, olvassa el a következő rendelkezésre álló terület az elsődleges régió listában-hívások végrehajtása. Ha az elsődleges régió lista régiót egyik sem érhető el, hívások automatikusan térhet vissza az aktuális írási régióba. Nincs szükség módosításokra az alkalmazás kódjában aktivált regionális feladatátvétel kezelésére. Ez a folyamat és garantált adatkonzisztenciát biztosítanak továbbra is Cosmos DB által veszi figyelembe.

![Olvasási régió hibák az Azure Cosmos DB-ben](./media/regional-failover/read-region-failures.png)

Ha az érintett régió a meghibásodás után állítja helyre, a rendszer automatikusan visszaállítja összes érintett Cosmos DB-fiókot a régióban a szolgáltatás által. Cosmos DB-fiókok, akinek az olvasási régióban az érintett régióban, majd automatikusan szinkronizálódik az aktuális írási régióba, és kapcsolja be az online. A Cosmos DB SDK Fedezze fel az új régióban rendelkezésre állását, és mérlegelje, hogy a régióban van kiválasztva, mint a jelenlegi olvasási régióban az alkalmazás által konfigurált kívánt régióhoz lista alapján. A helyreállított régió további olvasási megnyílik az alkalmazás kódjának módosítása nélkül.

**Mi történik, ha egy írási régió kimaradás?**

Ha az érintett régió az aktuális írási régióba, és az Automatikus feladatátvétel engedélyezve van az Azure Cosmos DB-fiókot, majd a régió automatikusan jelölésű offline állapotúként. Ezután egy másik régióba hitelesítenie kelljen magát, az írási régió az érintett Azure Cosmos DB-fiók. Engedélyezheti az Automatikus feladatátvétel és teljes mértékben sorrendjét régió kiválasztása az Azure Cosmos DB-fiókok az Azure Portalon vagy [programozott módon](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Az Azure Cosmos DB feladatátvételi prioritások](./media/regional-failover/failover-priorities.png)

Az automatikus feladatátvételek során az Azure Cosmos DB automatikusan kiválasztja azt a következő írási régiót egy adott Azure Cosmos DB-fiókot a megadott prioritási sorrend alapján. Alkalmazások WriteEndpoint osztály tulajdonságához tartozó DocumentClient használatával észleli a változást az írási régió.

![Írási régió hibák az Azure Cosmos DB-ben](./media/regional-failover/write-region-failures.png)

Ha az érintett régió a meghibásodás után állítja helyre, a rendszer automatikusan visszaállítja összes érintett Cosmos DB-fiókot a régióban a szolgáltatás által. 

* Az az előző írási régió, olvasási régió a szolgáltatáskimaradás közben nem replikált adatok, hírcsatorna ütközés van közzétéve. Alkalmazások olvassa el az ütköző hírcsatornában, az alkalmazás-specifikus logika alapján ütközések feloldásához és a frissített adatokat írhat az Azure Cosmos DB-fiók megfelelő vissza. 
* Az előző írási régióba újra létre kell hozni egy olvasási régió, és automatikusan válik újra online. 
* Újrakonfigurálhatja az olvasási régióban, amely került online állapotba kerüléséig automatikusan, az írási régió végre manuális feladatátvételt az Azure Portalon vagy [programozott módon](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

A következő kódrészletet bemutatja ütközések feldolgozása után az érintett régió állítja helyre a meghibásodás után.

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

Mellett automatikus feladatátvételi teszteket egy adott Cosmos DB-fiók az aktuális írási régióba manuálisan módosítható dinamikusan a meglévő olvasási régiók egyikéhez. Kézi feladatátvételt kezdeményezhet az Azure Portalon vagy [programozott módon](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Győződjön meg, hogy a manuális feladatátvételt **nulla adatvesztéssel** és **nulla rendelkezésre állási** adatvesztést, és szabályosan átvitel írási állapota a régi és új a megadott Cosmos DB-fiók írási régióba. Például az Automatikus feladatátvétel, a Cosmos DB SDK automatikusan kezeli az írási régió módosítások manuális feladatátvételek során és biztosítja, hogy hívásokat a rendszer automatikusan átirányítja az új írási régióba. Nincs kódírásra vagy konfigurálásra módosítások szükségesek az alkalmazásban való kezelése a feladatátvételeket. 

![Az Azure Cosmos DB manuális feladatátvétel](./media/regional-failover/manual-failovers.png)

A gyakori forgatókönyvek, ahol lehet hasznos a manuális feladatátvételt a következők:

**Kövesse az óra modell**: Ha az alkalmazások a létrehozás időpontját alapján kiszámítható forgalmi mintázatait, rendszeres időközönként módosíthatja az írási állapota a legaktívabb földrajzi régiót, a nap alapján.

**Szolgáltatás frissítése**: bizonyos globálisan elosztott alkalmazás központi telepítésének átirányítási forgalom a traffic manager segítségével különböző régióban során a tervezett szolgáltatásfrissítés is. Ilyen alkalmazás központi telepítése most már használhatja a manuális feladatátvételt arra, hogy az írási állapota a régió, ha van a későbbiekben aktív forgalom a szolgáltatás frissítési időszakban.

**Üzletmenet-folytonossági és vészhelyreállítási (BCDR) helyreállítási és magas rendelkezésre állású és vész-helyreállítási (HADR) gyakorlatokat**: a legtöbb vállalati alkalmazás üzleti folytonossági tesztek tartalmazzák a fejlesztési és kiadási folyamat részeként. BCDR, és a HADR tesztelési legtöbbször megfelelőségnek és garanciavállaló szolgáltatás rendelkezésre állása esetén a regionális üzemkimaradások utáni helyreállításon fontos lépés. BCDR készen áll-e az alkalmazások, amelyek használják a Cosmos DB Storage manuális feladatátvétel a Cosmos DB-fiók aktiválása és/vagy hozzáadásával és a egy régió eltávolítása dinamikusan tesztelheti.

Ebben a cikkben áttekinthette, hogyan manuális és automatikus feladatátvételi teszteket munkahelyi Cosmos DB-ben, és hogy hogyan konfigurálhat a Cosmos DB-fiókok és a globálisan elérhető alkalmazásokat. Cosmos DB globális replikációt támogatási használatával javíthatja a végpontok közötti késés, és győződjön meg arról, hogy azok régió hibák esetén is magas rendelkezésre állású. 

## <a id="NextSteps"></a>Következő lépések
* Ismerje meg hogyan támogatja a Cosmos DB [globális terjesztés](distribute-data-globally.md)
* Ismerje meg [az Azure Cosmos DB globális összhangot](consistency-levels.md)
* Fejlesztés az Azure Cosmos DB használatával több régióban [SQL API-hoz](tutorial-global-distribution-sql-api.md)
* Ismerje meg, hogyan hozhat létre [író többrégiós architektúrák](multi-region-writers.md) Azure Cosmos DB-vel


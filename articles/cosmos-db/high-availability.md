---
title: Az Azure Cosmos DB magas rendelkezésre állás
description: Ez a cikk bemutatja, hogyan nyújt az Azure Cosmos DB a magas rendelkezésre állás
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 928c943e21e7d00b87ac1e506b98d47107ac4348
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508556"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Az Azure Cosmos DB magas rendelkezésre állás

Az Azure Cosmos DB transzparensen replikálja az adatokat, minden régióban az Azure-Cosmos-fiókjához hozzárendelt. A cosmos DB többrétegű redundanciát biztosít az adatok alkalmaz, az alábbi képen látható módon:

![Fizikai particionálása](./media/high-availability/cosmosdb-data-redundancy.png)

- Cosmos-tárolók belül az adatok [horizontálisan particionált](partitioning-overview.md).

- Minden egyes régióban minden partícióján a replikakészlethez védi az összes írást replikálódnak, és a replikák többségi tartósan véglegesítve lettek. Replikák akár 10 – 20 tartalék tartományok között oszlanak meg.

- Mindegyik partíció minden olyan régióban replikálja a rendszer. Minden egyes régió Cosmos-tároló az összes adat partíciókat tartalmaz is és fogadja el az írási és olvasási szolgálja ki is.  

Ha a Cosmos-fiók között oszlanak meg *N* Azure-régióban lesz legalább *N* minden az adatok másolatát 4 x. Kis késleltetésű adathozzáférés biztosítása és az a Cosmos-fiókjához társított régiók közötti írási/olvasási átviteli sebességet skálázás kívül kellene a további régiók (magasabb *N*) tovább javítja a rendelkezésre állást.  

## <a name="slas-for-availability"></a>A rendelkezésre állási SLA-k

Egy globálisan elosztott adatbázis a Cosmos DB biztosítja a átfogó SLA-k, amely magában foglalja az átviteli sebességet a késés a 99. percentilis, a konzisztencia és a magas rendelkezésre állás. Az alábbi táblázat a magas rendelkezésre állás érdekében egy vagy több régióban fiókok Cosmos DB által nyújtott garanciák. A magas rendelkezésre állás érdekében mindig úgy állítsa be a Cosmos-fiókoknak, az írási több régióban rendelkezik.

|Művelet típusa  | Egyetlen régió |Többrégiós (egyetlen naplórekordjait)|Többrégiós (többrégiós írás) |
|---------|---------|---------|-------|
|Írások    | 99.99    |99.99   |99.999|
|Olvasások     | 99.99    |99.999  |99.999|

> [!NOTE]
> A gyakorlatban korlátozott frissesség, munkamenet, konzisztens előtag és végleges konzisztenciájú modellt a tényleges írási rendelkezésre áll jelentősen nagyobb, mint a közzétett SLA-kat. Az összes konzisztenciaszintek tényleges olvasási rendelkezésre áll jelentősen nagyobb, mint a közzétett SLA-kat.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Regionális üzemkimaradások utáni helyreállításon esetén a Cosmos DB magas rendelkezésre állás

Regionális üzemkimaradások utáni helyreállításon nem ritkák, és az Azure Cosmos DB lehetővé teszi, hogy az adatbázis mindig magas rendelkezésre állású. Az alábbi részletek Cosmos DB-viselkedés szolgáltatáskiesés esetén, attól függően, a Cosmos-fiók konfigurációjának rögzítése:

- A Cosmos DB használatával mielőtt írási művelet arra vonatkozik, az ügyfélnek, az adatok van tartósan véglegesítve lettek szerint a kvórum replikával, amely az írási műveletek a régión belül.

- Többrégiós fiókok több írási régiók konfigurált írások és olvasások magas rendelkezésre állású lesz. Régiónkénti feladatátvétel azonnali és nem igényel módosításokat az alkalmazásból.

- **A single-írási régió (írási régió kimaradásáról) rendelkező fiókok többrégiós:** Egy írási régió kimaradás során ezek a fiókok maradnak magas rendelkezésre állású olvasási. Azonban az írási műveletek kell **"Automatikus feladatátvétel engedélyezése"** a Cosmos a fiókját a feladatátvételi az érintett régió egy másik régióba. A feladatátvétel megadott régióban prioritás szerinti sorrendben történik. Ha az érintett régió újra online állapotba kerül, az érintett írási régióban leállás során árvává adatok keresztül teszik elérhetővé a [hírcsatorna ütközések](how-to-manage-conflicts.md#read-from-conflict-feed). Alkalmazás is olvashat az ütközések hírcsatorna, az alkalmazás-specifikus logika alapján az ütközések feloldásához és a frissített adatokat írhat vissza a Cosmos-tároló megfelelő. A korábban érintett írási régió állítja helyre, ha automatikusan elérhetővé válik, az olvasási régióban. Manuális feladatátvétel meghívása, és konfigurálja az érintett régió az írási régió. Újra tegye a manuális feladatátvételt használatával [Azure CLI-vel vagy az Azure Portalon](how-to-manage-database-account.md#manual-failover). Nincs **adatokat vagy a rendelkezésre állási adatvesztés nélkül** előtt, közben vagy a manuális feladatátvétel után. Az alkalmazás továbbra is magas rendelkezésre állású legyen. 

- **A single-írási régió (olvasási régió kimaradásáról) rendelkező fiókok többrégiós:** Egy olvasási régió kimaradás során ezek a fiókok maradnak magas rendelkezésre állású olvasási és írási. Az érintett terület a rendszer automatikusan leválasztja az írási régió és lesznek megjelölve offline állapotban van. A [Cosmos DB SDK](sql-api-sdk-dotnet.md) átirányítási fogja beolvasni a következő rendelkezésre álló terület az elsődleges régió listában hívásokat. Ha az elsődleges régió lista régiót egyik sem érhető el, hívások automatikusan térhet vissza az aktuális írási régióba. Nincs szükség módosításokra az alkalmazás kódjában olvasási régióban leállás kezelésére. Végül az érintett régió újra online állapotba kerül, ha a korábban érintett olvasási régió automatikusan szinkronizálja az aktuális írási régióba való és újra számára olvasási kérelmek kiszolgálására elérhető lesz. A helyreállított régió további olvasási megnyílik az alkalmazás kódjának módosítása nélkül. Során is feladatátvétel és a egy korábban meghiúsult régió újracsatlakozás olvassa el a konzisztencia garanciák folytassa a Cosmos DB által veszi figyelembe.

- Egyetlen régióban fiókok elveszítheti a rendelkezésre állás a következő regionális kimaradás. Minden esetben javasoljuk, hogy állítsa be a **legalább két régióban** (lehetőleg, legalább két írási régió) magas rendelkezésre állásának biztosításához mindig Cosmos fiókjával.

- Még akkor is, a ritka és kellemetlen esemény véglegesen helyreállíthatatlan, az Azure-régió esetén nincs adatvesztés nélkül Ha többrégiós Cosmos-fiókja van konfigurálva, az alapértelmezett konzisztenciaszint *erős*. A véglegesen helyreállíthatatlan írási régiót, a konfigurált, kötött elavulás konzisztencia, többrégiós Cosmos-fiókok esetén az esetleges adatvesztési időszak korlátozódik a frissesség ablak (*K* vagy *T*); munkamenet, konzisztens előtag és végleges konzisztencia szintek, a potenciális adatvesztési időszak korlátozva öt másodpercenként legfeljebb. 

## <a name="availability-zone-support"></a>Rendelkezésre állási zóna támogatása

Az Azure Cosmos DB egy globálisan elosztott, több főkiszolgálós database szolgáltatás, amely a magas rendelkezésre állást és rugalmasságot biztosít a regionális üzemkimaradások utáni helyreállításon alatt. Emellett keresztszűrést régió rugalmasságot, hogy mostantól engedélyezheti a **redundancia zóna** egy régiót, és rendelje hozzá az Azure Cosmos database kiválasztásakor. 

A rendelkezésre állási zóna támogatása az Azure Cosmos DB biztosítja, hogy replikák zónaszintű meghibásodások magas rendelkezésre állást és rugalmasságot biztosít egy adott régión belül több zónában vannak elhelyezve. Nem változtak, késés és más SLA-k ebben a konfigurációban. Abban az esetben, ha egy zóna hiba, a zone redudancy biztosít teljes adatok megőrzését az RPO-val = 0 és rendelkezésre állása és RTO = 0. 

A Zone redudancy van egy *kiegészítő funkció* , a [több főkiszolgálós replikációs](how-to-multi-master.md) funkció. A Zone redudancy önmagában nem regionális rugalmasság lehet hivatkozni. Például esetén a regionális üzemkimaradások utáni helyreállításon, vagy a régiók közötti közel valós idejű hozzáféréshez, javasolt a zone redudancy mellett több írási régiót rendelkezik. 

Azure Cosmos-fiókja többrégiós írási konfigurálásakor mekkorák a zone redudancy nélkül többletköltség. Ellenkező esetben tekintse a redundancia zónatámogatással díjszabása kapcsolatban az alábbi megjegyzést. Az Azure Cosmos-fiók egy létező régiót a zone redudancy eltávolítása a régiót, és vegye fel azt vissza a zone redudancy engedélyezve van a engedélyezheti.

Ez a funkció az alábbi Azure-régióban érhető el:

* Az Egyesült Királyság déli régiója
* Délkelet-Ázsia 
* East US
* USA 2. keleti régiója 
* USA középső régiója

> [!NOTE] 
> A rendelkezésre állási zónák engedélyezése az Azure Cosmos-fiók egy régió egyenértékű egy további régióban ad a számlájához díjakat eredményez. További információ a díjszabásról: a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/cosmos-db/) és a [többrégiós költségek az Azure Cosmos DB](optimize-cost-regions.md) cikkeket. 

Az alábbi táblázat foglalja össze különböző fiókkonfigurációt magas rendelkezésre állású képességét: 

|KPI  |Egy régióban anélkül, hogy a rendelkezésre állási zónák (nem-AZ)  |Egyetlen régió a rendelkezésre állási zónák (AZ)  |Többrégiós ír a rendelkezésre állási zónák (AZ, 2 régió) – a legtöbb javasolt beállítás |
|---------|---------|---------|---------|
|Rendelkezésre állási SLA írása     |   99.99%      |    99.99%     |  99.999%  |
|Olvassa el a rendelkezésre állási SLA   |   99.99%      |   99.99%      |  99.999%       |
|Ár  |  Egyetlen régió díjazási tarifa |  Egyetlen régió rendelkezésre állási zónában díjazási tarifa |  Többrégiós díjazási tarifa       |
|Zóna hibák – adatvesztés   |  Adatvesztés  |   Nincs adatvesztés |   Nincs adatvesztés  |
|Rendelkezésre állási zóna hibák |  Rendelkezésre állás elvesztése  | Rendelkezésre állási adatvesztés nélkül  |  Rendelkezésre állási adatvesztés nélkül  |
|Olvasási késés    |  Adatbázisközi régió    |   Adatbázisközi régió   |    Alacsony  |
|Írási késés    |   Adatbázisközi régió   |  Adatbázisközi régió    |   Alacsony   |
|Regionális kimaradás – adatvesztés    |   Adatvesztés      |  Adatvesztés       |   Adatvesztés <br/><br/> Kötött elavulás konzisztencia többszörös főkiszolgáló és több régióban, adatvesztés korlátozódik, a korlátozott frissesség, a fiók konfigurálva. <br/><br/> Regionális kimaradás során adatvesztés el kell kerülni több régióban az erős konzisztencia konfigurálásával. Ez a beállítás kompromisszumot kínál a rendelkezésre állást és teljesítményt befolyásoló tartalmaz.      |
|Regionális kimaradás – rendelkezésre állás  |  Rendelkezésre állás elvesztése       |  Rendelkezésre állás elvesztése       |  Rendelkezésre állási adatvesztés nélkül  |
|Teljesítmény    |  X RU/s kiosztott átviteli sebesség      |  X RU/s kiosztott átviteli sebesség       |  2 x RU/s kiosztott átviteli sebesség <br/><br/> A konfigurációs mód szükséges kétszer átviteli sebesség, mert két régióban van a rendelkezésre állási zónákban egyetlen régióban történő képest mennyisége.   |

> [!NOTE] 
> Ahhoz, hogy a rendelkezésre állási zóna támogatása, az Azure Cosmos DB-fiók több-főkulcs/több-region írások engedélyezve kell rendelkeznie. 

A zone redudancy engedélyezheti egy régió új vagy meglévő Azure-Cosmos-fiókok való hozzáadásakor. Jelenleg csak engedélyezheti a zone redudancy az Azure portal, PowerShell és Azure Resource Manager-sablonok. Ahhoz, hogy a zone redudancy a Azure Cosmos-fiókjában, állítsa be a `isZoneRedundant` jelzőt `true` egy konkrét hely. Ez a jelző a helyek tulajdonságok állíthatja be. Például a következő powershell-kódrészlettel lehetővé teszi a zone redudancy a "Délkelet-Ázsia" régió:

```powershell
$locations = @( 
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" }, 
    @{ "locationName"="East US"; "failoverPriority"=1 } 
) 
```

A rendelkezésre állási zónák egy Azure Cosmos-fiók létrehozásakor az Azure portal használatával engedélyezheti. Egy fiók létrehozásakor ügyeljen arra, hogy engedélyezze a **georedundancia**, **többrégiós ír**, és válassza ki a régiót, ahol a rendelkezésre állási zónák használata támogatott: 

![Az Azure portal használatával, a rendelkezésre állási zónák engedélyezése](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Magas rendelkezésre állású alkalmazások létrehozása

- Győződjön meg, hogy nagy írási és olvasási rendelkezésre állás, több írási régiók közül legalább két régió span Cosmos fiók beállítása. Ez a konfiguráció biztosítja a legmagasabb szintű rendelkezésre állásra, a legkisebb késés, és mindkét ajánlott méretezhetőség beolvassa és SLA-k által. További tudnivalókért lásd: hogyan [több írási régiót a Cosmos-fiók beállítása](tutorial-global-distribution-sql-api.md).

- Többrégiós Cosmos-fiókok, egy egyetlen-írási régió konfigurált [automatikus feladatátvétel engedélyezése az Azure CLI-vel vagy az Azure portal használatával](how-to-manage-database-account.md#automatic-failover). Miután engedélyezte az automatikus feladatátvételt egy regionális katasztrófa történik, Cosmos DB automatikusan feladatátvételt fiókját.  

- Akkor is, ha a Cosmos-fiók magas rendelkezésre állású, az alkalmazás lehetséges, hogy nem helyesen kialakítani, hogy továbbra is magas rendelkezésre állású. A végpontok közötti magas rendelkezésre állás az alkalmazás teszteléséhez rendszeres időközönként meghívása a [manuális feladatátvételt az Azure CLI-vel vagy az Azure portal használatával](how-to-manage-database-account.md#manual-failover), az alkalmazás tesztelésének vagy a vészhelyreállítás (DR) részeként gyakorlatokat.

- Egy globálisan elosztott adatbázis-környezeten belül nincs közvetlen kapcsolat folytonosságát egy régióra kiterjedő szolgáltatáskimaradás konzisztencia szint és az adatok tartósságának között. Az üzletmenet folytonosságát biztosító terve kidolgozásakor kell tudni, mielőtt az alkalmazás a zavaró eseményeket követő teljes helyreállításának maximális elfogadható idő. Az alkalmazás teljes helyreállításához szükséges időt a helyreállítási időre vonatkozó célkitűzés (RTO) néven ismert. Emellett ismernie kell a leghosszabb az alkalmazás működését, Adatfrissítés elvesztése zavaró eseményeket követő helyreállítása során. Az adatfrissítés-vesztés megengedhető időkorlátja a helyreállítási időkorlát (RPO). Az Azure Cosmos DB az RPO és RTO megtekintéséhez lásd: [konzisztencia szinteket és az adatok tartóssága](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>További lépések

Ezután az alábbi cikkekben olvashat:

* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [Globálisan a kiosztott átviteli kapacitás méretezése](scaling-throughput.md)
* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
* [Az Azure Cosmos DB-ben konzisztenciaszintek](consistency-levels.md)
* [A Cosmos-fiók konfigurálása több írási régióval](how-to-multi-master.md)

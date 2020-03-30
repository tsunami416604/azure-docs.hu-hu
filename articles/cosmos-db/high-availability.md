---
title: Magas rendelkezésre állás az Azure Cosmos DB-ben
description: Ez a cikk azt ismerteti, hogy az Azure Cosmos DB hogyan biztosít magas rendelkezésre állást
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 0f024bac535ed792d8480c991e470cf5d85932b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247422"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Magas rendelkezésre állás az Azure Cosmos DB használatával

Az Azure Cosmos DB transzparens módon replikálja az adatokat a Cosmos-fiókhoz társított összes Azure-régióban. A Cosmos DB többrétegű redundanciát alkalmaz az adatokhoz, ahogy az az alábbi képen látható:

![Fizikai particionálás](./media/high-availability/cosmosdb-data-redundancy.png)

- A Cosmos-tárolókon belüli adatok [vízszintesen particionáltak.](partitioning-overview.md)

- Az egyes régiókon belül minden partíciót egy replikakészlet véd, amely a replikák többsége replikált és tartósan véglegesített írási műveleteket. Replikák között vannak elosztva, mint 10-20 tartalék tartományok.

- Minden partíció az összes régióban replikálódik. Minden régió tartalmazza a Cosmos-tároló összes adatpartícióját, és képes fogadni az írásokat és az olvasásokat.  

Ha a Cosmos-fiók *N* n Azure-régiók között van elosztva, az összes adatból legalább *N* x 4 másolatot kap. Amellett, hogy alacsony késésű adatelérést biztosít, és a Cosmos-fiókhoz társított régiók között az írási/olvasási átviteli szintet is biztosítja, több régió (magasabb *N)* további javítása a rendelkezésre állást.  

## <a name="slas-for-availability"></a>SLOS-ok az elérhetőséghez

Globálisan elosztott adatbázisként a Cosmos DB átfogó SLA-kat biztosít, amelyek magukban foglalják az átviteli, a 99 százalékos késleltetést, a konzisztenciát és a magas rendelkezésre állást. Az alábbi táblázat bemutatja a Cosmos DB által az egy- és többrégiós fiókok magas rendelkezésre állása garantálja. A magas rendelkezésre állás érdekében mindig konfigurálja a Cosmos-fiókok több írási régiók.

|Művelet típusa  | Egy régió |Többrégiós (egyrégiós írások)|Többrégiós (többrégiós írások) |
|---------|---------|---------|-------|
|Írja    | 99,99    |99,99   |99,999|
|Olvas     | 99,99    |99,999  |99,999|

> [!NOTE]
> A gyakorlatban a tényleges írási rendelkezésre állása a határolt frissesség, munkamenet, konzisztens előtag és végleges konzisztencia modellek jelentősen magasabb, mint a közzétett SLA-k. A tényleges olvasási rendelkezésre állás minden konzisztenciaszinten jelentősen magasabb, mint a közzétett SLA-k.

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Magas rendelkezésre állás a Cosmos DB-vel regionális kimaradások esetén

A regionális kimaradások nem ritkák, az Azure Cosmos DB azonban gondoskodik arról, hogy az adatbázisa mindig magas rendelkezésre állású maradjon. A következő részletek rögzítik a Cosmos DB viselkedését egy kimaradás során, a Cosmos-fiók konfigurációjától függően:

- A Cosmos DB,mielőtt egy írási művelet et az ügyfél nyugtázott, az adatok tartósan véglegesíti a replikák kvóruma a régión belül, amely elfogadja az írási műveleteket.

- Többrégiós fiókok konfigurálása több írási régiók magas rendelkezésre állású lesz mind az írási és olvasási. A regionális feladatátvételek azonnaliak, és nem igényelnek módosításokat az alkalmazásból.

- Az egyrégiós fiókok egy regionális kimaradás után elveszíthetik a rendelkezésre állást. Mindig ajánlott legalább két **régiót** (lehetőleg legalább két írási régiót) beállítani a Cosmos-fiókkal, hogy mindig magas rendelkezésre állást biztosítson.

- **Többrégiós fiókok egyírású régióval (írási régió kimaradás):**
  - Egy írási régió kimaradása során a Cosmos-fiók automatikusan előléptet egy másodlagos régiót az új elsődleges írási régióvá, ha az **automatikus feladatátvétel engedélyezése** konfigurálva van az Azure Cosmos-fiókban. Ha engedélyezve van, a feladatátvétel egy másik régióba történik a megadott régióprioritás-sorrendben.
  - Az ügyfelek is dönthetnek úgy, hogy **manuális feladatátvételt,** és figyelheti a Cosmos írási végpont URL-címét maguk egy ügynök saját maguk által épített. Az ügyfelek összetett és kifinomult egészségügyi figyelési igények, ez csökkentheti az RTO hiba esetén az írási régióban.
  - Ha a korábban érintett terület újra online állapotba kerül, minden olyan írási adat, amely nem replikált, amikor a régió meghibásodott, elérhetővé válik az [ütközések hírcsatornáján](how-to-manage-conflicts.md#read-from-conflict-feed)keresztül. Az alkalmazások olvashatják az ütközések hírcsatornáját, feloldhatják az ütközéseket az alkalmazásspecifikus logika alapján, és szükség szerint visszaírhatják a frissített adatokat az Azure Cosmos-tárolóba.
  - Akorábban érintett írási régió helyreállítása után automatikusan elérhetővé válik olvasási régióként. Visszaválthat a helyreállított területre írási régióként. A régiók at az [Azure CLI vagy](how-to-manage-database-account.md#manual-failover)az Azure Portal használatával válthat. Nincs **adat vagy rendelkezésre állási veszteség** előtt, alatt vagy után az írási régióváltás előtt, és az alkalmazás továbbra is magas rendelkezésre állású.

- **Többrégiós fiókok egyírású régióval (olvasási régió kimaradás):**
  - Olvasási régió kimaradása során ezek a fiókok továbbra is magas rendelkezésre állású olvasási és írási.
  - Az érintett terület automatikusan megszakad, és offline lesz megjelölve. Az [Azure Cosmos DB SDK-k átirányítja](sql-api-sdk-dotnet.md) az olvasási hívásokat a következő elérhető régióaz előnyben részesített régió listájában.
  - Ha a régiók rangsorolt listáján egyetlen régió sem érhető el, a beérkező kérések automatikusan az aktuális írási régióra váltanak vissza.
  - Nincs szükség módosításokra az alkalmazáskódjában a beolvasási régió kimaradásának kezeléséhez. Végül, ha az érintett régió újra online állapotba kerül, a korábban érintett olvasási régió automatikusan szinkronizálódik az aktuális írási régióval, és újra elérhető lesz az olvasási kérelmek kiszolgálásához.
  - A további olvasásokat a szolgáltatás a helyreállt régiókhoz irányítja át anélkül, hogy módosítania kellene az alkalmazáskódot. A feladatátvétel és a korábban meghibásodott régióhoz való újbóli csatlakozás során az olvasási konzisztencia-garanciáktovábbra is tiszteletben tartják a Cosmos DB által.

- Még egy ritka és szerencsétlen esemény, amikor az Azure-régió véglegesen helyrehozhatatlan, nincs adatvesztés, ha a több régiós Cosmos-fiók *erős* konzisztencia konfigurálva van. Abban az esetben, ha egy véglegesen helyrehozhatatlan írási régió, egy többrégiós Cosmos-fiók határolt frissesség konzisztenciával konfigurálva, a potenciális adatvesztési ablak az állottsági ablakra (*K* vagy *T*) korlátozódik, ahol a K=100 000 frissítés és a T=5 perc. A munkamenet, a konzisztens előtag és a végleges konzisztenciaszintek esetében a potenciális adatvesztési ablak legfeljebb 15 percre van korlátozva. Az Azure Cosmos DB RTO- és RPO-céljairól a [Konzisztenciaszintek és az adatok tartóssága](consistency-levels-tradeoffs.md#rto) című témakörben talál további információt.

## <a name="availability-zone-support"></a>Rendelkezésre állási zóna támogatása

A régiók közötti rugalmasság mellett mostantól engedélyezheti **a zónaredundanciát,** amikor kiválaszt egy régiót az Azure Cosmos-adatbázishoz társítva.

Az Elérhetőségi zóna támogatásával az Azure Cosmos DB biztosítja, hogy a replikák egy adott régión belül több zónában legyenek elhelyezve, hogy magas rendelkezésre állást és rugalmasságot biztosítsanak a zónaszintű hibák során. Ebben a konfigurációban nincsváltozás a késésben és más SL-ekben. Egyetlen zónahiba esetén a zónaredundancia teljes adattartósságot biztosít az RPO=0 és az RTO=0 rendelkezésre állással.

A zónaredundancia a [többfős replikációs](how-to-multi-master.md) szolgáltatás *kiegészítő képessége.* A regionális rugalmasság elérése érdekében önmagában nem lehet a zónaredundanciára támaszkodni. Például abban az esetben, regionális kimaradások vagy alacsony késésű hozzáférés a régiók között, azt javasoljuk, hogy több írási régiók mellett zóna redundancia.

Többrégiós írások konfigurálásakor az Azure Cosmos-fiók, választhatzóna-redundancia többletköltség nélkül. Ellenkező esetben tekintse meg az alábbi megjegyzést a zónaredundancia támogatás díjszabásáról. Az Azure Cosmos-fiók egy meglévő régiójában engedélyezheti a zónaredundanciát a régió eltávolításával és a zónaredundancia engedélyezésével.

Ez a funkció a következő Azure-régiókban érhető el:

- Az Egyesült Királyság déli régiója

- Délkelet-Ázsia

- USA keleti régiója

- USA 2. keleti régiója

- USA középső régiója

- Nyugat-Európa

- USA nyugati régiója, 2.

> [!NOTE]
> Egyetlen régióra vonatkozó rendelkezésre állási zónák engedélyezése esetén az Azure Cosmos-fiók hoz olyan díjakat, amelyek egyenértékűek egy további régió hozzáadása a fiókhoz. A díjszabásról az [Azure Cosmos DB-cikkeiben](optimize-cost-regions.md) a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/) és a több régióra kiterjedő költségekben tájékozódási alatt tájékozódási(b)

Az alábbi táblázat a különböző fiókkonfigurációk magas rendelkezésre állási képességét foglalja össze:

|KPI  |Egyetlen régió rendelkezésre állási zónák nélkül (nem AZ)  |Egyetlen régió rendelkezésre állási zónákkal (AZ)  |Többrégiós írások rendelkezésre állási zónákkal (AZ, 2 régió) – A legtöbb ajánlott beállítás |
|---------|---------|---------|---------|
|Rendelkezésre állási SLA írása | 99.99% | 99.99% | 99.999% |
|Rendelkezésre állási SLA olvasása  | 99.99% | 99.99% | 99.999% |
|Price | Egyrégiós számlázási díj | Egyrégiós rendelkezésre állási zóna számlázási díja | Több régióra kiterjedő számlázási díj |
|Zónahibák – adatvesztés | Adatvesztés | Nincs adatvesztés | Nincs adatvesztés |
|Zónahibák – elérhetőség | Rendelkezésre állási veszteség | Nincs rendelkezésre állási veszteség | Nincs rendelkezésre állási veszteség |
|Késés olvasása | Régiók közötti | Régiók közötti | Alacsony |
|Késés írása | Régiók közötti | Régiók közötti | Alacsony |
|Regionális kimaradás – adatvesztés | Adatvesztés |  Adatvesztés | Adatvesztés <br/><br/> Ha több főkiszolgálóval és több régióval határolt frissesség-konzisztenciát használ, az adatvesztés a fiókjában konfigurált korlátozott frissességre korlátozódik. <br /><br />Elkerülheti az adatvesztést egy regionális kimaradás során, ha több régióval erős konzisztenciát konfigurál. Ez a lehetőség olyan kompromisszumokkal jár, amelyek befolyásolják a rendelkezésre állást és a teljesítményt. Csak olyan fiókokon konfigurálható, amelyek egyrégiós írásokhoz vannak konfigurálva. |
|Regionális kimaradás – elérhetőség | Rendelkezésre állási veszteség | Rendelkezésre állási veszteség | Nincs rendelkezésre állási veszteség |
|Teljesítmény | X RU/s kiosztott átviteli fazeka | X RU/s kiosztott átviteli fazeka | 2X RU/s kiépített átviteli fazeka <br/><br/> Ez a konfigurációs mód kétszer annyi átviteli, ha összehasonlítjuk a rendelkezésre állási zónákkal rendelkező egyetlen régióval, mert két régió van. |

> [!NOTE]
> A több régiós Azure Cosmos-fiók rendelkezésre állási zóna támogatásának engedélyezéséhez a fióknak engedélyeznie kell a többfős írási műveleteket.

Engedélyezheti a zónaredundanciát, ha régiót ad hozzá új vagy meglévő Azure Cosmos-fiókokhoz. Zónaredundancia engedélyezéséhez az Azure Cosmos-fiókban, állítsa be a `isZoneRedundant` jelzőt `true` egy adott helyre. Ezt a jelzőt a helytulajdonságon belül állíthatja be. A következő powershell-kódrészlet például lehetővé teszi a zónaredundanciát a "Délkelet-Ázsia" régióban:

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)
```

A következő parancs bemutatja, hogyan engedélyezheti a zónaredundanciát az "EastUS" és a "WestUS2" régiókban:

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

Az Azure Cosmos-fiók létrehozásakor engedélyezheti a rendelkezésre állási zónákat az Azure Portal használatával. Amikor létrehoz egy fiókot, győződjön meg arról, hogy engedélyezi a **Geo-redundancia**, **Többrégiós írások**, és válasszon egy régiót, ahol a rendelkezésre állási zónák támogatottak:

![Rendelkezésre állási zónák engedélyezése az Azure Portal használatával](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>Magas rendelkezésre állású alkalmazások létrehozása

- A magas írási és olvasási rendelkezésre állás biztosítása érdekében konfigurálja a Cosmos-fiókot, hogy legalább két régiótöbb írási régiókkal. Ez a konfiguráció biztosítja a legmagasabb rendelkezésre állást, a legalacsonyabb késést és a legjobb méretezhetőséget az SL-ek által támogatott olvasásokhoz és írásokhoz. További információ: A [Cosmos-fiók konfigurálása több írási régióval.](tutorial-global-distribution-sql-api.md)

- Az egyírású régióval konfigurált többrégiós Cosmos-fiókok esetén [engedélyezze az automatikus feladatátvételt az Azure CLI vagy az Azure Portal használatával.](how-to-manage-database-account.md#automatic-failover) Miután engedélyezte az automatikus feladatátvételt, ha regionális katasztrófa történik, a Cosmos DB automatikusan feladatátvételt tesz lehetővé a fiókjában.  

- Még akkor is, ha a Cosmos-fiók magas rendelkezésre állású, előfordulhat, hogy az alkalmazás nem megfelelően tervezték, hogy továbbra is magas rendelkezésre állású. Az alkalmazás tesztelése vagy vész-helyreállítási (DR) gyakorlatainak teljes körű magas rendelkezésre állásának teszteléséhez ideiglenesen tiltsa le a fiók automatikus feladatátvételét, hívja meg a [manuális feladatátvételt](how-to-manage-database-account.md#manual-failover)az Azure CLI vagy az Azure Portal használatával, majd figyelje az alkalmazás feladatátvételt. Miután elkészült, feladat-visszaaz elsődleges régióba, és állítsa vissza a fiók automatikus feladatátvétel.

- Egy globálisan elosztott adatbázis-környezetben közvetlen kapcsolat van a konzisztenciaszint és az adatok tartóssága között egy régiószintű kimaradás jelenlétében. Az üzletmenet-folytonossági terv kidolgozásakor meg kell értenie a maximális elfogadható időt, mielőtt az alkalmazás teljesen helyreáll egy zavaró esemény után. Az alkalmazás teljes helyreállításához szükséges idő a helyreállítási idő célkitűzése (RTO). Azt is meg kell értenie, hogy a legutóbbi adatfrissítések maximális időtartama az alkalmazás tolerálhatja a elveszítést, ha egy zavaró esemény után helyreáll. Az adatfrissítés-vesztés megengedhető időkorlátja a helyreállítási időkorlát (RPO). Az RPO és az RTO az Azure Cosmos DB-hez című témakörben [a Konzisztenciaszintek és az adatok tartóssága](consistency-levels-tradeoffs.md#rto) című témakörben

## <a name="next-steps"></a>További lépések

Ezután elolvashatja a következő cikkeket:

- [Rendelkezésre állási és teljesítménykompromisszumok a különböző konzisztenciaszintekhez](consistency-levels-tradeoffs.md)
- [Kiosztott átviteli sebesség globális méretezése](scaling-throughput.md)
- [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
- [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md)
- [A Cosmos-fiók konfigurálása több írási régióval](how-to-multi-master.md)

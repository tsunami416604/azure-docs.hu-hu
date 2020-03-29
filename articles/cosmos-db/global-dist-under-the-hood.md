---
title: Globális disztribúció az Azure Cosmos DB-vel a motorháztető alatt
description: Ez a cikk az Azure Cosmos DB globális disztribúciójával kapcsolatos technikai részleteket tartalmaz
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: a46a69476a2ad6550bc7b3a533fd09565d461db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872128"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Globális adatterjesztés az Azure Cosmos DB-vel – a motorháztető alatt

Az Azure Cosmos DB egy alapvető szolgáltatás az Azure-ban, így világszerte az összes Azure-régióban üzembe helyezhető, beleértve a nyilvános, a szuverén, a Védelmi Minisztériumot (DoD) és a kormányzati felhőket. Egy adatközponton belül üzembe helyezjük és kezeljük az Azure Cosmos DB-t a gépek hatalmas bélyegzőin, amelyek mindegyike dedikált helyi tárolóval rendelkezik. Egy adatközponton belül az Azure Cosmos DB számos fürtön keresztül van telepítve, amelyek mindegyike több generációnyi hardvert futtat. A fürtön belüli gépek általában 10–20 tartalék tartományban vannak elosztva, hogy egy régión belül magas rendelkezésre állás úk. Az alábbi képen a Cosmos DB globális elosztórendszer-topológiája látható:

![Rendszertopológia](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Az Azure Cosmos DB globális disztribúciója kulcsrakész:** Bármikor, néhány kattintással vagy programozott egyetlen API-hívással, hozzáadhatja vagy eltávolíthatja a Cosmos-adatbázishoz társított földrajzi régiókat. A Cosmos-adatbázis viszont Cosmos-tárolók készletéből áll. A Cosmos DB-ben a tárolók a terjesztési és méretezhetőség logikai egységeiként szolgálnak. A létrehozott gyűjtemények, táblák és grafikonok (belsőleg) csak Cosmos-tárolók. A tárolók teljesen séma-függetlenek, és hatókört biztosítanak a lekérdezéshez. A Cosmos-tárolóban lévő adatok at a rendszer automatikusan indexeli a betöltéskor. Az automatikus indexelés lehetővé teszi a felhasználók számára, hogy a séma- vagy indexkezelés szóváltása nélkül kérdezzék le az adatokat, különösen globálisan elosztott beállítások esetén.  

- Egy adott régióban a tárolón belüli adatok terjesztése egy partíció-kulcs, amely biztosítja, és az alapul szolgáló fizikai partíciók (*helyi terjesztési*) transzparens módon kezeli.  

- Minden egyes fizikai partíció földrajzi régiók között is replikálódik (*globális eloszlás*). 

Ha egy Cosmos DB-t használó alkalmazás rugalmasan skálázható egy Cosmos-tároló, vagy több tárhelyet fogyaszt, a Cosmos DB transzparens módon kezeli a partíciókezelési műveleteket (split, clone, delete) az összes régióban. A méretezési, terjesztési vagy hibáktól függetlenül a Cosmos DB továbbra is egyetlen rendszerképet biztosít a tárolókon belüli adatokról, amelyek globálisan elvannak osztva tetszőleges számú régióban.  

Amint az az alábbi képen látható, a tárolón belüli adatok két dimenzió mentén oszlanak meg - egy régión belül és régiók között, világszerte:  

![fizikai partíciók](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

A fizikai partíciót replikák egy csoportja valósítja meg, amelyet *replikáknak neveznek.* Minden gép több száz replikák, amelyek megfelelnek a különböző fizikai partíciók egy rögzített folyamatok, amint az a fenti képen látható. A fizikai partícióknak megfelelő replikák dinamikusan kerülnek elhelyezésre, és a terhelés elosztása a fürtön és egy régión belüli adatközpontokon belüli gépeken.  

A replika egyedileg tartozik egy Azure Cosmos DB-bérlő. Minden replika a Cosmos DB [adatbázis-motorjának](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)egy példányát üzemelteti, amely az erőforrásokat és a kapcsolódó indexeket kezeli. A Cosmos adatbázis-motor egy atom-rekord-szekvencia (ARS) alapú típusrendszeren működik. A motor agnosztikus a séma fogalmához képest, elmossa a határvonalat a rekordok szerkezete és példányértékei között. Cosmos DB eléri a teljes séma agnoszticizmus automatikusan indexelése mindent betöltéskor hatékony módon, amely lehetővé teszi a felhasználók számára, hogy lekérdezi a globálisan elosztott adatokat anélkül, hogy a séma vagy index kezelése.

A Cosmos adatbázis-motor olyan összetevőkből áll, amelyek több koordinációs primitív megvalósítását, a nyelvi futtatásokat, a lekérdezésfeldolgozót, valamint a tranzakciós tárolásért és adatindexelésért felelős tároló- és indexelő alrendszereket, Illetve. A tartósság és a magas rendelkezésre állás biztosítása érdekében az adatbázismotor megőrzi az SSD-ken lévő adatait és indexét, és replikálja azadatbázis-motorpéldányai között a replikakészlet(ek)en belül. A nagyobb bérlők nagyobb átviteli és tárolási skálának felelnek meg, és nagyobb vagy több replikával vagy mindkettővel rendelkeznek. A rendszer minden összetevője teljesen aszinkron – egyetlen szál sem blokkolja, és minden szál rövid életű munkát végez anélkül, hogy felesleges szálkapcsolókat vonna maga után. A sebességkorlátozás és az ellennyomás a teljes veremben a beléptetésvezérléstől az összes I/O elérési útig van. A Cosmos adatbázis-motorja a részletes egyidejűség kihasználására és a nagy átviteli sebességgel történő használatra lett tervezve, miközben a rendszer erőforrásainak takarékos mennyiségében működik.

A Cosmos DB globális disztribúciója két kulcsfontosságú absztrakción alapul : *replikakészleteken* és *partíciókészleteken.* A replikakészlet egy moduláris Lego blokk a koordinációhoz, és a partíciókészlet egy vagy több földrajzilag elosztott fizikai partíció dinamikus átfedése. Ahhoz, hogy megértsük, hogyan működik a globális elosztás, meg kell értenünk ezt a két kulcsfontosságú absztrakciók. 

## <a name="replica-sets"></a>Replikakészletek

A fizikai partíció valósul meg, mint egy önállóan kezelt és dinamikusan terheléselosztásos replikák replikák több tartalék tartományok, az úgynevezett replika-készlet. Ez a készlet együttesen valósítja meg a replikált állapotgép protokollt, hogy a fizikai partíción belüli adatok magas rendelkezésre állásúak, tartósak és konzisztensek legyenek. A replikakészlet-tagság *N* dinamikus – folyamatosan ingadozik az *NMin* és az *NMax* között a hibák, a felügyeleti műveletek és a sikertelen replikák regenerálásának/helyreállításának ideje alapján. A tagsági változások alapján a replikációs protokoll újrakonfigurálja az olvasási és írási kvórumok méretét is. Egy adott fizikai partícióhoz rendelt átviteli teljesítmény egységes elosztásához két ötletet alkalmazunk: 

- Először is, a vezető írási kérelmeinek feldolgozásának költsége magasabb, mint a frissítések alkalmazásának költsége a követőn. Ennek megfelelően a vezető költségvetése több rendszererőforrás, mint a követői. 

- Másodszor, amennyire lehetséges, egy adott konzisztenciaszint olvasási kvóruma kizárólag a követő replikákból áll. Elkerüljük a kapcsolatot a vezető szolgáló olvasás, kivéve, ha szükséges. A Cosmos DB által támogatott [öt konzisztenciamodell](consistency-levels.md) [terhelésének és kapacitásának](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) kapcsolatára vonatkozó kutatásokból származó számos ötletet alkalmazunk.  

## <a name="partition-sets"></a>Partíciókészletek

A fizikai partíciók egy-egy, a Cosmos adatbázis-régiókkal konfigurált, egy-egy, hogy kezelje az összes konfigurált régiókban replikált kulcsok egy-egy csoport. Ezt a magasabb koordinációs primitívet *partíciókészletnek* nevezik – egy adott kulcskészletet kezelő fizikai partíciók földrajzilag elosztott dinamikus átfedése. Míg egy adott fizikai partíció (egy replikakészlet) hatóköre egy fürtön belül történik, a partíciókészlet a fürtökre, adatközpontokra és földrajzi régiókra is kiterjedhet, ahogy az az alábbi képen látható:  

![Partíciókészletek](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

A partíciókészlet földrajzilag szétszórt "szuper replikakészletként" is felfogható, amely több, azonos kulcskészlettel rendelkező replikakészletből áll. A replikakészlethez hasonlóan a partíciókészlet tagsága is dinamikus – az implicit fizikai partíciókezelési műveletek alapján ingadozik, hogy új partíciókat adjon hozzá/távolítson el egy adott partíciókészletből (például amikor egy tárolóátviteli-átvitelt horizontálisfelskáláz, hozzáad/eltávolít egy régiót a Cosmos-adatbázishoz, vagy ha hibák fordulnak elő). Azzal, hogy az egyes partíciók (egy partíció-készlet) kezeli a partíciókészlet-tagság a saját replika-készlet, a tagság teljesen decentralizált és magas rendelkezésre állású. A partíciókészlet újrakonfigurálása során a fizikai partíciók közötti átfedés topológiája is létrejön. A topológia dinamikusan van kiválasztva a konzisztenciaszint, a földrajzi távolság és a rendelkezésre álló hálózati sávszélesség alapján a forrás és a cél fizikai partíciók között.  

A szolgáltatás lehetővé teszi, hogy konfigurálja a Cosmos-adatbázisok egyetlen írási régióval vagy több írási régióval, és a választástól függően a partíciókészletek úgy vannak konfigurálva, hogy pontosan egy vagy az összes régióban fogadja el az írásokat. A rendszer kétszintű, beágyazott konszenzusos protokollt alkalmaz – az egyik szint az írásokat elfogadó fizikai partíció replikakészletének replikáin belül működik, a másik pedig egy partíciókészlet szintjén működik, hogy teljes körű rendelési garanciákat nyújtson minden a partíciókészleten belül a véglegesített írásokat. Ez a többrétegű, beágyazott konszenzus kritikus fontosságú a szigorú SLA-k magas rendelkezésre állásérdekében történő megvalósítása, valamint a Cosmos DB által az ügyfelek nek kínálott konzisztenciamodellek megvalósítása szempontjából.  

## <a name="conflict-resolution"></a>Ütközések feloldása

A frissítés terjedését, a konfliktusmegoldást és az ok-okozati összefüggésnyomon követését a [járványos algoritmusokés](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) a [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) rendszer korábbi munkája ihlette. Míg a kernelek az ötletek túlélték, és egy kényelmes referenciakeret et a kommunikáció a Cosmos DB rendszer kialakítása, ők is jelentős átalakuláson mentek keresztül, ahogy alkalmaztuk őket a Cosmos DB rendszer. Erre azért volt szükség, mert a korábbi rendszereket sem az erőforrás-irányítással, sem a Cosmos DB működésének léptékével tervezték, sem pedig a képességek (például a határos állság konzisztenciája) és a szigorú és átfogó SLA-kat, amelyeket a Cosmos DB szállít ügyfeleinek.  

Emlékezzünk vissza, hogy egy partíciókészlet több régióközött van elosztva, és a Cosmos DBs (többfőes) replikációs protokollt követi az adatok replikálása az adott partíciókészletet tartalmazó fizikai partíciók között. Minden fizikai partíció (egy partíció-készlet) elfogadja írások és kiszolgálja olvasások általában az ügyfelek, amelyek a helyi adott régióban. Egy régión belüli fizikai partíció által elfogadott írások tartósan véglegesítve vannak, és magas rendelkezésre állásúvá váltak a fizikai partíción belül, mielőtt az ügyfélnek nyugtáznák őket. Ezek feltételes írások, és a partíciókészleten belüli más fizikai partíciókra propagálva egy entrópia-csatorna használatával kerülnek terjesztésre. Az ügyfelek feltételes vagy véglegesített írásokat kérhetnek egy kérelemfejléc átadásával. Az anti-entrópia propagálás (beleértve a propagálás gyakoriságát) dinamikus, a partíciókészlet topológiája, a fizikai partíciók regionális közelsége és a konfigurált konzisztenciaszint alapján. Egy partíciókészleten belül a Cosmos DB egy dinamikusan kiválasztott választotti partícióval rendelkező elsődleges véglegesítési sémát követ. A döntőbíró kiválasztása dinamikus, és szerves része a partíció-készlet újrakonfigurálásának az átfedés topológiája alapján. A véglegesített írások (beleértve a többsoros/kötegelt frissítéseket) garantáltan megrendelhetők. 

Kódolt vektorórákat (amelyek régióazonosítót és logikai órákat tartalmaznak, amelyek megfelelnek a replika- és partíció-készletben lévő konszenzus minden szintjének) az ok-okozati összefüggések nyomon követéséhez és a verzióvekvivőkhöz a frissítési ütközések észlelésére és feloldására. A topológia és a társkiválasztási algoritmus úgy van kialakítva, hogy biztosítsa a rögzített és minimális tárhelyet és a verzióvektorok minimális hálózati terhelését. Az algoritmus garantálja a szigorú konvergencia tulajdonságot.  

A több írási régióval konfigurált Cosmos-adatbázisok esetében a rendszer számos rugalmas automatikus ütközésfeloldási házirendet kínál a fejlesztők számára, többek között a következőket: 

- **Last-Write-Wins (LWW) ( Last-Write-Wins )**( amely alapértelmezés szerint egy rendszer által definiált időbélyeg tulajdonságot használ (amely az idő-szinkronizálási óraprotokollon alapul). Cosmos DB is lehetővé teszi, hogy adja meg bármely más egyéni numerikus tulajdonságot kell használni az ütközések feloldásához.  
- **Alkalmazás által definiált (egyéni) ütközésfeloldási házirend** (egyesítési eljárásokon keresztül kifejezve), amely az ütközések alkalmazás által definiált szemantika-egyeztetésére szolgál. Ezeket az eljárásokat a kiszolgálói oldalon lévő adatbázis-tranzakció égisze alatt az írási-írási ütközések észlelésekor hívja meg a rendszer. A rendszer pontosan egyszer garantálja az egyesítési eljárás végrehajtását a kötelezettségvállalási protokoll részeként. Számos [ütközésfeloldási minta](how-to-manage-conflicts.md) áll rendelkezésre, amelyekkel játszhat.  

## <a name="consistency-models"></a>Konzisztencia modellek

Függetlenül attól, hogy a Cosmos-adatbázist egyetlen vagy több írási régióval konfigurálja, az öt jól definiált konzisztenciamodell közül választhat. Több írási régióval a konzisztenciaszintek néhány figyelemre méltó aspektusa található:  

A határolt frissesség konzisztencia garantálja, hogy az összes olvasás k *előtagok* vagy *T* másodperca legutóbbi írási bármely régióban. Továbbá a kötött frissesség konzisztenciájú olvasások garantáltan monotonok és konzisztens előtaggaranciákkal. Az anti-entrópia protokoll korlátozott sebesség-korlátozott módon működik, és biztosítja, hogy az előtagok nem halmozódnak fel, és az írási műveletek ellennyomását nem kell alkalmazni. A munkamenet-konzisztencia garantálja a monoton olvasást, a monoton írást, a saját írások olvasását, az írás követést az olvasást és a konzisztens előtaggaranciákat világszerte. Az erős konzisztenciával konfigurált adatbázisok esetében több írási régió előnyei (alacsony írási késés, magas írási rendelkezésre állás) nem érvényesek a régiók közötti szinkron replikáció miatt.

A Cosmos DB öt konzisztenciamodelljének szemantikáját [itt](consistency-levels.md)ismertetjük , és matematikailag magas szintű TLA+ specifikációk használatával [itt](https://github.com/Azure/azure-cosmos-tla)írjuk le.

## <a name="next-steps"></a>További lépések

Ezután ismerje meg, hogyan konfigurálhatja a globális terjesztést az alábbi cikkek használatával:

* [Régiók hozzáadása és eltávolítása az adatbázisfiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Az ügyfelek konfigurálása több-homing](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Egyéni ütközésfeloldási házirend létrehozása](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

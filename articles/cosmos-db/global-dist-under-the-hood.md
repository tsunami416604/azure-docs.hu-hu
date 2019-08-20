---
title: Globális terjesztés Azure Cosmos DBokkal – a motorháztető alatt
description: Ez a cikk az Azure Cosmos DB globális terjesztési vonatkozó technikai részleteket ismertet
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: ce943fbed0774667100f6de4c60f91c0b02de6c3
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615347"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Globális Adatterjesztés a Azure Cosmos DB-ben – a motorháztető alatt

Azure Cosmos DB az Azure alapszintű szolgáltatása, így az összes világszerte elérhető Azure-régióban üzembe helyezhető, beleértve a nyilvános, szuverén, védelmi minisztériumi (DoD) és kormányzati felhőket is. Egy adatközponton belül azt üzembe helyezése és kezelése az Azure Cosmos DB nagy stampek gépek, az egyes dedikált helyi tárterülettel rendelkező. Egy adatközponton belül az Azure Cosmos DB van üzembe helyezve között számos fürthöz egyes potenciálisan futó hardver generációja több. A fürtön belüli gépek általában az 10-20-es tartalék tartományok között oszlanak meg a magas rendelkezésre állás érdekében a régión belül. Az alábbi képen a Cosmos DB globális terjesztésének topológia látható:

![Topológia](./media/global-dist-under-the-hood/distributed-system-topology.png)

**A Azure Cosmos DB globális eloszlása kulcsrakész:** Bármikor, néhány kattintással vagy programozott módon egyetlen API-hívással, hozzáadhat vagy eltávolíthat a Cosmos-adatbázishoz társított földrajzi régiókat. A Cosmos-adatbázisok a Cosmos-tárolók készletét tartalmazzák. A Cosmos DB-tárolók szolgálhat terjesztési és méretezhetőséget biztosít a logikai tárolóegységeket. A gyűjtemények, táblák és létrehozhat a gráfok tárolói (belső) csak Cosmos. A tárolók teljes mértékben sémák és agnosztikusok, és hatókört biztosítanak a lekérdezésekhez. Cosmos-tárolóban lévő adatok automatikusan indexelt Adatbetöltési esetén. Az Automatikus indexelés lehetővé teszi a felhasználók számára az adatlekérdezést a séma vagy az index kezelésének gondja nélkül, különösen egy globálisan elosztott telepítésben.  

- Egy adott régióban a tárolóban lévő adatokat egy olyan partíciós kulcs használatával osztják el, amelyet Ön biztosít, és a mögöttes fizikai partíciók (*helyi terjesztés*) transzparens módon kezelik.  

- Az egyes fizikai partíciók a földrajzi régiók között is replikálódnak (*globális eloszlás*). 

Ha egy Cosmos DB rugalmasan méretezi az átviteli sebességet egy Cosmos-tárolón, vagy több tárhelyet használ fel, Cosmos DB az összes régióban transzparens módon kezeli a particionálási műveleteket (felosztás, klónozás, törlés). Független a méretezési csoport, terjesztési vagy hibák, Cosmos DB folyamatosan egyetlen rendszerképet a Data-tárolók, amely globálisan bármennyi régió között oszlanak meg.  

Ahogy az az alábbi ábrán is látható, a tárolóban lévő adatok két dimenzión oszlanak el – egy régión belül és régiókban, a világ minden részén:  

![Fizikai partíciók](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

A fizikai partíciót replikák egy csoportja *hozza létre, amelyet replika-készletnek*neveznek. Mindegyik gép több száz replikát üzemeltet, amelyek a fenti képen látható különböző fizikai partíciókhoz tartoznak. A fizikai partíciók megfelelő replikák dinamikusan kerülnek, és a terheléselosztáshoz a gépek belül egy adott régión belül egy fürt és adatközpontok között.  

A replika egyedi az Azure Cosmos DB-bérlő tartozik. Minden egyes replikának a Cosmos DB példányát futtatja [adatbázismotor](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), amely kezeli az erőforrások, valamint a hozzá tartozó indexeket. A Cosmos-adatbázismotor egy Atom-Record-Sequence (ARS) alapú típusú rendszeren működik. A motor agnosztikus a séma koncepciójában, és elmossa a rekordok szerkezeti és példányi értékei közötti határt. A cosmos DB automatikusan indexeli mindent után az Adatbetöltési és hatékonyan, amely lehetővé teszi a felhasználók számára, hogy sémákkal vagy indexkezeléssel kezelése nélkül kérdezheti le a globálisan elosztott adatokon a teljes séma agnosticism biztosít.

A Cosmos adatbázismotor olyan összetevőkből áll, mint a különböző koordinációs primitívek, nyelvi futtatókörnyezetek, a lekérdezési processzor, valamint a tranzakciós tároláshoz és az adatindexeléshez felelős tárolási és indexelési alrendszerek. illetve. Tartósság és magas rendelkezésre állás biztosítása érdekében az adatbázismotor az adatok és index az SSD-k továbbra is fennáll, és azt replikálja a replika belül motor adatbázispéldányok-készleteket jelölik. A nagyobb bérlők az átviteli sebesség és a tárterület nagyobb méretének felelnek meg, és nagyobb vagy több replikával vagy mindkettővel rendelkeznek. A rendszer minden összetevője teljes aszinkron – nincs hozzászóláslánc minden eddiginél letiltja, és minden egyes szál rövid ideig tartó működik olyan felesleges szál kapcsolót anélkül. A sebességhatárolt és vissza – nyomás között a teljes verem a már a betegfelvétel vezérlőből az összes i/o-útvonalat adható hozzá. A Cosmos adatbázismotor a részletes Egyidejűség kihasználása és a nagy adatátviteli sebesség biztosítására szolgál, és a rendszer erőforrásain belül működik.

Cosmos DB globális eloszlása két kulcsfontosságú absztrakcióra támaszkodik – a *replika-* és a *partíciós készletekre*. A replikakészlet egy moduláris Lego-letiltása a koordináció, és a egy partíció-set a földrajzilag elosztott egy vagy több fizikai partíciók egy dinamikus területre. Szeretné megismerni globális működik, hogy ismerniük kell ezek két fő absztrakciók. 

## <a name="replica-sets"></a>Replikakészletekhez

A fizikai partíciók önfelügyelt és dinamikusan elosztott replikák, amelyek több tartalék tartományba vannak osztva, és az úgynevezett replika-készletnek nevezzük. Ez a készlet együttesen valósítja meg a replikált állapotú számítógép-protokollt, hogy a fizikai partíción belül elérhetővé tegye a nagyfokú rendelkezésre állást, a tartós és a konzisztens eszközöket. A replika-set tag *N* dinamikus – a hibák, a felügyeleti műveletek és a sikertelen replikák újralétrehozásához/helyreállításához szükséges idő alapján ingadozik a *NMin* és a *nnaponkénti maximum* között. A tagság végbement változások alapján, a replikáció is protokoll Átkonfigurálás olvasási méretét, és határozatképességére írása. Az adott fizikai partícióhoz rendelt átviteli sebesség egységes elosztásához két ötletet alkalmazzunk: 

- Első lépésként az írási kérelmeknek a vezetőn való feldolgozásának díja magasabb, mint a frissítések a követőn való alkalmazásának díja. Ennek megfelelően a vezető tervezett van több rendszererőforrást, mint a követők. 

- Másodszor amennyire csak lehetséges, az adott konzisztenciaszint olvasási kvórum áll kizárólag a követő metódus replikákat. Hogy ne vegye fel a kapcsolatot a vezető, de az olvasási, kivéve, ha szükséges. A Cosmos DB által támogatott [öt konzisztencia](consistency-levels.md) -modellhez a kvórumon alapuló rendszerek [terhelésének és kapacitásának](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) összekapcsolásával kapcsolatban számos ötletet alkalmazunk.  

## <a name="partition-sets"></a>Partíció-csoportok

A fizikai partíciók egy csoportja, amelyek mindegyike a Cosmos adatbázis-régiókkal van konfigurálva, az összes konfigurált régióban replikált azonos kulcsok kezelésére szolgál. Ezt a magasabb szintű koordinációs primitívet *partíciós készletnek* nevezzük – a fizikai partíciók földrajzilag elosztott dinamikus átfedése, amely egy adott kulcsot kezel. Míg egy adott fizikai partíció (egy replika-készlet) egy fürtön belül van, a partíciók a fürtökön, az adatközpontokban és a földrajzi régiókban is kiterjedhetnek, ahogy az alábbi képen is látható:  

![Partíció beállítása](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Egy partíció-set készletként földrajzilag elosztott"super replika-", amely több-replikakészletekhez ugyanazokat a kulcsokat a tulajdonos is felfoghatók. A replika-készlethez hasonlóan a partíciós tagság is dinamikus – az implicit fizikai particionálási műveletek alapján ingadozik, hogy új partíciókat vegyen fel/távolítson el egy adott partíción (például ha kibővíti az átviteli sebességet tároló, régió hozzáadása/eltávolítása a Cosmos-adatbázisba, vagy ha hiba történik. Azáltal, hogy az egyes partíciók (partíciók) a saját replikáján belül kezelik a particionálási tagságot, a tagság teljes mértékben decentralizált és magasan elérhető. Egy partíció-készlet az újrakonfigurálás alatt a topológia a fizikai partíciók között átfedés is létrejön. A topológia dinamikusan van kiválasztva a forrás és a cél fizikai partíciói között a konzisztencia, a földrajzi távolság és a rendelkezésre álló hálózati sávszélesség alapján.  

A szolgáltatás lehetővé teszi a Cosmos-adatbázisok konfigurálása egy egyetlen írási régió vagy több írási régiót, és a választás, attól függően a pontosan egy vagy minden régióban írások fogadására a partíció-csoportok vannak konfigurálva. A rendszer kétszintű, beágyazott konszenzusú protokollt alkalmaz – az egyik szint egy olyan replika replikáján belül működik, amely az írásokat fogadja el, és a másik egy partíció szintjén működik, amely teljes körű rendelési garanciát biztosít az összes a partíción belül a véglegesített írások. A többrétegű, beágyazott konszenzus, kritikus fontosságú a magas rendelkezésre állású szigorú szerződései megvalósítását, valamint a Cosmos DB biztosít az ügyfeleinek konzisztenciamodell megvalósítását.  

## <a name="conflict-resolution"></a>Ütközések feloldása

A tervezési, a frissítés propagálás, ütközésfeloldás és okozati nyomon követése a korábbi munkahelyi profilból inspirációt a [járványos algoritmusok](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) és a [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) rendszer. A kernelekkel, ötleteire megélték rendelkezik, és adjon meg egy kényelmes hivatkozási a Cosmos DB-rendszer terve való kommunikációhoz, is estek át jelentős átalakításában, a Cosmos DB rendszer táblázatunk őket. Erre azért volt szükség, mert az előző rendszerek nem az erőforrás-szabályozással, sem a Cosmos DB működésének méretezésével, sem a képességek biztosításával (például a határos elavulás következetességével) és a szigorú és átfogó SLA-kat, amelyeket Cosmos DB biztosít ügyfeleinek.  

Ne felejtse el, hogy a partíció beállítása legyen elosztva több régióban, és követi a Cosmos DB-k (több főkiszolgálós) replikációs protokoll használatával replikálja az adatokat egy adott partíció-készletet alkotó fizikai partíciók között. Az egyes fizikai partíciók (egy partíciós készlet) elfogadják az írásokat, és általában az adott régióhoz tartozó ügyfelekre mutatnak olvasási műveleteket. Az adott régióban található fizikai partíció által elfogadott írásokat a rendszer tartósan véglegesíti, és a fizikai partíción belül, az ügyfélnek való nyugtázás előtt elvégzi. Ezek feltételes írások és propagálva lesz másik fizikai partíciók egy víruskereső vysokou csatorna használatával a partíció,-csoporton belül. Ügyfelek lekérdezhetik feltételes vagy véglegesített írások fejléc átadásával. A víruskereső vysokou propagálás (beleértve a propagálás gyakorisága) el dinamikus, a partíció-készletet, a regionális közelség fizikai partíciók és a konzisztencia, beállított szint a topológia alapján. Egy partíció-csoportba a Cosmos DB dinamikusan kiválasztott soron partícióval rendelkező elsődleges véglegesítési séma követi. A soron lehetőség a dinamikus, és szerves része az újrakonfigurálás a partíció-készlet alapján topológiáját, az átmeneti területre. A lefoglalt írás (többek között több-row/kötegelt frissítések) garantáltan meg fog felelni. 

A kódolt vektor órák (régió azonosítója és a megfelelő konszenzus a replikakészlethez: minden egyes szintjét, és a partíció-készlet, logikai órák tartalmazó jelölik) okozati nyomon követése és verzió vektorok észlelése és megoldása érdekében a frissítés, alkalmazunk. A topológia és a társ-kiválasztási algoritmus úgy tervezték, hogy rögzített, és minimális tárolási és a verzióvektor, minimális hálózati terhelést. Az algoritmus a szigorú convergence tulajdonság garantálja.  

A konfigurált több írási régióval rendelkező Cosmos-adatbázisok, a rendszer rugalmas automatikus ütközés számos megoldás olyan szabályzatot kínál a fejlesztőknek, választhat, többek között: 

- A **Last-Write-WINS (LWW)** , amely alapértelmezés szerint a rendszer által definiált időbélyeg-tulajdonságot használja (amely az időszinkronizálási órajel protokollon alapul). A cosmos DB lehetővé teszi bármely más egyéni numerikus tulajdonság használandó ütközésfeloldás megadása.  
- **Alkalmazás által definiált (egyéni) ütközés-feloldási házirend** (egyesítési eljárásokkal kifejezve), amely az alkalmazás által definiált szemantikai egyeztetésekhez készült. Ezek az eljárások az írás-írás ütközés a kiszolgálói oldalon az adatbázis-tranzakciók felügyelete alatt észlelésekor első meghívni. A rendszer pontosan egyszer garantálja az egyesítési eljárás végrehajtását a kötelezettségvállalási protokoll részeként. [Több ütközési megoldási minta](how-to-manage-conflicts.md) is elérhető a következővel:.  

## <a name="consistency-models"></a>Konzisztenciamodell

Akár egy vagy több írási régióval konfigurálja a Cosmos-adatbázist, az öt jól meghatározott konzisztencia-modell közül választhat. Több írási régió esetében a konzisztencia szintjeinek néhány jelentős aspektusa a következő:  

A határos állandósági konzisztencia garantálja, hogy az összes olvasás *K* előtagon vagy *T* másodpercen belül maradjon a legújabb írásokból bármelyik régióban. Emellett a korlátozott elavulás konzisztenciájával való olvasás garantáltan monoton és konzisztens előtag-garanciákat biztosít. A víruskereső vysokou protokoll sebessége korlátozott módon működik, és biztosítja, hogy az előtagok nem gyűlik össze, és az írási ellennyomásának nem rendelkezik a alkalmazni. A munkamenet konzisztenciája garantálja az monoton olvasást, az monoton írást, a saját írásait, az írást, az olvasást és az konzisztens előtag-garanciákat világszerte. Az erős konzisztencia-konfigurációval konfigurált adatbázisok esetében a több írási régió előnyei (alacsony írási késés, magas írási rendelkezésre állás) nem érvényesek a különböző régiók közötti szinkron replikáció miatt.

Az Cosmos DB öt konzisztencia-modelljének szemantikai leírását [itt](consistency-levels.md)találja, és matematikai módon ismertetjük a magas szintű tla + specifikációk [](https://github.com/Azure/azure-cosmos-tla)segítségével.

## <a name="next-steps"></a>További lépések

Ezután megtudhatja, hogyan globális terjesztés konfigurálása a következő cikkek segítségével:

* [Régiók hozzáadása/eltávolítása az adatbázis-fiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [A többkiszolgálós ügyfelek konfigurálása](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Egyéni ütközés-feloldási szabályzat létrehozása](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

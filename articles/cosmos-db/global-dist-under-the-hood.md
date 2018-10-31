---
title: Az Azure Cosmos DB globális terjesztésének – technikai részletek
description: Ez a cikk az Azure Cosmos DB globális terjesztési vonatkozó technikai részleteket ismertet
services: cosmos-db
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 21464ccfbd5712b18e46a271a93232dc3ba7d3c8
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244079"
---
# <a name="global-distribution---under-the-hood"></a>Globális terjesztés – technikai részletek

Az Azure Cosmos DB szolgáltatás eligazodást az Azure-ban, így az összes Azure-régiókban világszerte többek között a nyilvános, a független, a védelmi Minisztérium (DoD) és a kormányzati felhők között központilag telepítették. Egy adatközponton belül azt üzembe helyezése és kezelése az Azure Cosmos DB szolgáltatás nagy "stampek" gépek, a dedikált helyi tárterülettel rendelkező. Egy adatközponton belül az Azure Cosmos DB van üzembe helyezve között számos fürthöz egyes potenciálisan futó hardver generációja több. Fürtön belüli gépek általában 10 – 20 tartalék tartományokban vannak elosztva.

![Topológia](./media/global-dist-under-the-hood/distributed-system-topology.png)
**topológia**

Az Azure Cosmos DB globális terjesztését az kulcsrakész: bármikor, néhány kattintással vagy programozott módon egyetlen API hívással ügyfél hozzáadhat és eltávolíthat a földrajzi régiók társított a Cosmos database. Cosmos-tárolók egy készletét ezután áll egy Cosmos-adatbázis. A Cosmos DB-tárolók szolgálhat terjesztési és méretezhetőséget biztosít a logikai tárolóegységeket. A gyűjtemények, táblák és létrehozhat a gráfok tárolói (belső) csak Cosmos. Tárolók rendszer teljesen sémafüggetlen, és adja meg a lekérdezés hatókörét. Cosmos-tárolóban lévő összes adat automatikusan indexelt Adatbetöltési esetén. Automatikus indexelés lehetővé teszi a felhasználóknak nem kell foglalkozni a séma vagy index kezelését, különösen egy globálisan elosztott környezetben, és az adatok lekérdezéséhez.  

Ahogy az az alábbi képen is látható, a tárolóban lévő adatok terjesztése két dimenzió mentén:  

- Egy adott régióban tárolóban lévő adatok egy partíciós kulccsal, amely megadja, és átlátható módon kezeli az alapul szolgáló forráserőforrás-partíció (helyi elosztás) használatával vannak.  
- Minden erőforrás-partícióból több földrajzi régióban készül másolat (globális elosztás). 

Amikor egy alkalmazást rugalmasan Cosmos DB használatával méretezi az átviteli sebességet (vagy további tárhelyet használ) egy Cosmos-tárolón, Cosmos DB transzparens módon kezeli a műveleteket (split, klónozza, Törlés) minden olyan régióban. Független a méretezési csoport, terjesztési vagy hibák, Cosmos DB folyamatosan egyetlen rendszerképet a Data-tárolók, amely globálisan bármennyi régió között oszlanak meg.  

![Erőforrás-partíciók](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)
**erőforrás-partíciók eloszlása**

Fizikailag erőforrás-partíció replikákat, a replikakészlethez nevű csoportja által valósul meg. Egyes gépek az előző képen látható módon a rögzített folyamatok belül különböző erőforrás-partíciók megfelelő replikák több száz üzemelteti. Az erőforrás-partíciók megfelelő replikák dinamikusan kerülnek, és a terheléselosztáshoz a gépek belül egy adott régión belül egy fürt és adatközpontok között.  

A replika egyedi az Azure Cosmos DB-bérlő tartozik. Minden egyes replikának a Cosmos DB példányát futtatja [adatbázismotor](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), amely kezeli az erőforrások, valamint a hozzá tartozó indexeket. A Cosmos DB adatbázismotorja az atom-rekord-szekvencia (ARS) alapuló típus rendszeren működik. A motor egy séma- és a rekordok szerkezetét és a példány értékek közötti határ felismerhetetlenné fogalma független. A cosmos DB automatikusan indexeli mindent után az Adatbetöltési és hatékonyan, amely lehetővé teszi a felhasználók számára, hogy sémákkal vagy indexkezeléssel kezelése nélkül kérdezheti le a globálisan elosztott adatokon a teljes séma agnosticism biztosít.

A Cosmos DB adatbázismotorja végrehajtása több koordináció primitívek nyelvi futtatókörnyezetet, a lekérdezésfeldolgozó és a tárolási és indexelő felelős tranzakciós tárolási alrendszerek és az indexelő az adatokat, többek között összetevőből áll: jelölik. Tartósság és magas rendelkezésre állás biztosítása érdekében az adatbázismotor az adatok és index az SSD-k továbbra is fennáll, és azt replikálja a replika belül motor adatbázispéldányok-készleteket jelölik. Nagyobb méretű bérlők magasabb átviteli sebesség és tárterület méretezése felelnek meg, és nagyobb méretű vagy több replika vagy mindkettő száma. A rendszer minden összetevője teljes aszinkron – nincs hozzászóláslánc minden eddiginél letiltja, és minden egyes szál rövid ideig tartó működik olyan felesleges szál kapcsolót anélkül. A sebességhatárolt és vissza – nyomás között a teljes verem a már a betegfelvétel vezérlőből az összes i/o-útvonalat adható hozzá. Az adatbázismotor részletes egyidejűségi kihasználásához és nagy átviteli sebesség biztosításához belül frugal mennyiségű rendszer-erőforrásokat, miközben tervezték.

Cosmos DB globális terjesztésének támaszkodik két fő absztrakciók – -replikakészletekhez és a partíció-csoportok. A replikakészlet egy moduláris Lego-letiltása a koordináció, és egy partíció-set legalább egy földrajzilag elosztott erőforrás-partíciók egy dinamikus területre. Szeretné megismerni globális működik, hogy ismerniük kell ezek két fő absztrakciók. 

## <a name="replica-sets"></a>Replikakészletekhez

Erőforrás-partíció a tényleges táblán alapuló replikák több tartalék tartomány, a replikakészlethez nevű elosztva önállóan felügyelt, és dinamikusan kiegyenlített terhelésű csoportként. A készlet együttesen a replikált állapot gép protokollt, hogy az erőforrás-partíción belül az adatok magas rendelkezésre álló, tartós és egységes valósítja meg. A replikakészlet tagság N dinamikus –, tartja NMin és a hibákat, a felügyeleti műveletek és a sikertelen replikák időpontját alapján NMax fiókkulcs újbóli létrehozása vagy a helyreállítás között ingadozik. A tagság végbement változások alapján, a replikáció is protokoll Átkonfigurálás olvasási méretét, és határozatképességére írása. Szeretné egyenletesen elosztani a átviteli sebesség az adott erőforrás-partíció van rendelve, a két ötleteket alkalmazunk: először a vezető az írási kérelmek feldolgozásának költsége magasabb, mint a frissítések alkalmazása a követő metódus költsége. Ennek megfelelően a vezető tervezett van több rendszererőforrást, mint a követők. Másodszor amennyire csak lehetséges, az adott konzisztenciaszint olvasási kvórum áll kizárólag a követő metódus replikákat. Hogy ne vegye fel a kapcsolatot a vezető, de az olvasási, kivéve, ha szükséges. A Research történik, a kapcsolat az ötleteit számos alkalmazunk [terhelés és a kapacitás](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) a kvórum-alapú rendszerekben a öt konzisztencia modellek, amelyek a Cosmos DB támogatja.  

## <a name="partition-sets"></a>Partíció-csoportok

Erőforrás-partíciók, egyet az egyes a Cosmos-adatbázis régióban a beállított alkalmazáscsoportokból ugyanazokat az összes konfigurált régiók közötti replikálására, kulcsok kezeléséhez. Ez magasabb koordináció primitív egy partíció-set - erőforrás-partíciók egy adott halmazát kulcsok kezelése egy földrajzilag elosztott dinamikus területre nevezzük. Egy adott erőforrás-partíció (egy replika-készlet) fürtön belüli hatókörét, amíg a partíció-set is kiterjedhet fürtök, az adatközpontok és a földrajzi régióban, az alábbi képen látható módon:  

![Csoportok particionálása](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)
**partíció-készlet nem egy dinamikus területre az erőforrás-partíciók**

Egy partíció-set készletként földrajzilag elosztott"super replika-", amely több-replikakészletekhez ugyanazokat a kulcsokat a tulajdonos is felfoghatók. A replikakészlet hasonlóan egy partíció-készlet tagsági is dinamikus –, ingadozik implicit erőforrás műveleteket és törlése lehetőségéhez új partíciókat és- tárolókról egy adott partíció-készlet alapján (például ha horizontális felskálázása átviteli sebesség a egy tároló hozzáadása egy régiót a Cosmos-adatbázis, vagy hibák esetén) alapján, hogy minden olyan partíció (legyen az egy partíció-) kezelése a saját replikakészlethez partíció-set tagjaik, a tagsági teljes decentralizált és magas érhető el. Egy partíció-készlet az újrakonfigurálás alatt a topológia az erőforrás-partíciók között átfedés is létrejön. A topológia dinamikusan van kiválasztva, konzisztenciaszintjétől, a földrajzi távolságtól és a rendelkezésre álló hálózati sávszélesség a forrás- és a célként megadott erőforrás-partíciók közötti alapján.  

A szolgáltatás lehetővé teszi a Cosmos-adatbázisok konfigurálása egy egyetlen írási régió vagy több írási régiót, és a választás, attól függően a pontosan egy vagy minden régióban írások fogadására a partíció-csoportok vannak konfigurálva. A rendszer egy kétszintű, beágyazott konszenzus protokollt használ – egy szint a replikák fogadja az írások erőforrás-partíció replikakészlet belül működik, és a másik egy partíció-készlet teljes sorbarendezésre garanciákat biztosít minden szinten működik a partíció-csoporton belül véglegesített írási műveletek. A többrétegű, beágyazott konszenzus, kritikus fontosságú a magas rendelkezésre állású szigorú szerződései megvalósítását, valamint a Cosmos DB biztosít az ügyfeleinek konzisztenciamodell megvalósítását.  

## <a name="conflict-resolution"></a>Ütközések feloldása

A tervezési, a frissítés propagálás, ütközésfeloldás és okozati nyomon követése a korábbi munkahelyi profilból inspirációt a [járványos algoritmusok](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) és a [Bayou](http://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) rendszer. A kernelekkel, ötleteire megélték rendelkezik, és adjon meg egy kényelmes hivatkozási a Cosmos DB-rendszer terve való kommunikációhoz, is estek át jelentős átalakításában, a Cosmos DB rendszer táblázatunk őket. Ez volt szükség, mert a korábbi rendszerekhez készültek, sem az erőforrás-szabályozás, sem a méretezéssel, ahol a Cosmos DB kell megfelelően működjenek, és nem az funkciók (például korlátozott frissesség konzisztencia), és szigorú és átfogó SLA-k, amelyek a Cosmos DB biztosít az ügyfeleinek.  

Ne felejtse el, hogy egy partíció-készlet legyen elosztva több régióban, és a Cosmos DB-k (több főkiszolgálós) replikációs protokoll használatával replikálja az adatokat, többek között az erőforrás-partíciók, amely magában foglalja egy adott partíció-set a következő. Minden egyes erőforrás-partíció (legyen az egy partíció-) írási műveletek fogad, és olvasási általában szolgál az ügyfelek, amelyek a helyi régióban. Fogadja el az erőforrás-partíció egy adott régión belül írási tartósan véglegesítve és beállítani magas rendelkezésre az erőforrás-partíción belül, mielőtt azok a igazoltak vissza az ügyfélnek. Ezek feltételes írások és egyéb erőforrás-partíciók használatával egy víruskereső vysokou csatorna partíció csoporton belül propagálva. Ügyfelek lekérdezhetik feltételes vagy véglegesített írások fejléc átadásával. A víruskereső vysokou propagálás (beleértve a propagálás gyakorisága) el dinamikus, a partíció-készletet, a regionális közelség az erőforrás-partíciók, és a konzisztencia, beállított szint a topológia alapján. Egy partíció-csoportba a Cosmos DB dinamikusan kiválasztott soron partícióval rendelkező elsődleges véglegesítési séma követi. A soron lehetőség a dinamikus, és szerves része az újrakonfigurálás a partíció-készlet alapján topológiáját, az átmeneti területre. A lefoglalt írás (többek között több-row/kötegelt frissítések) garantáltan meg fog felelni. 

A kódolt vektor órák (régió azonosítója és a megfelelő konszenzus a replikakészlethez: minden egyes szintjét, és a partíció-készlet, logikai órák tartalmazó jelölik) okozati nyomon követése és verzió vektorok észlelése és megoldása érdekében a frissítés, alkalmazunk. A topológia és a társ-kiválasztási algoritmus úgy tervezték, hogy rögzített, és minimális tárolási és a verzióvektor, minimális hálózati terhelést. Az algoritmus a szigorú convergence tulajdonság garantálja.  

A konfigurált több írási régióval rendelkező Cosmos-adatbázisok, a rendszer rugalmas automatikus ütközés számos megoldás olyan szabályzatot kínál a fejlesztőknek, választhat, többek között: 

- Wins-utolsó-írási (LWW), amely alapértelmezés szerint a rendszer által meghatározott időbélyeg-tulajdonság (amely az idő-szinkronizálási órája protokollon alapul) használja. A cosmos DB lehetővé teszi bármely más egyéni numerikus tulajdonság használandó ütközésfeloldás megadása.  
- Alkalmazás által meghatározott egyéni ütközésfeloldási házirend (egyesítési eljárások kifejezve) amely ütközések alkalmazás által meghatározott szemantika egyeztetéséhez. Ezek az eljárások az írás-írás ütközés a kiszolgálói oldalon az adatbázis-tranzakciók felügyelete alatt észlelésekor első meghívni. A rendszer biztosít pontosan egyszer garantálja a kötelezettségvállalás protokoll részeként egy merge eljárás végrehajtására. Nincsenek elérhető, hogy játsszon több mintát.  

## <a name="consistency-models"></a>Konzisztenciamodell

A Cosmos-adatbázis egy vagy több írási régiót konfigurál, hogy öt jól definiált konzisztenciamodellekkel közül választhat. Az újonnan hozzáadott engedélyezésének támogatása több írás régiója, az az alábbiakban néhány jelentős aspektusait konzisztenciaszintekről:  

Mivel előtt, a korlátozott frissesség konzisztencia garantálja, hogy olvasások k verzióval vagy t másodperc, a legújabb írási belül lesz, a régiók egyikében. Továbbá korlátozott frissesség konzisztencia az olvasási garantáltan monoton és a konzisztens előtag garanciákkal. A víruskereső vysokou protokoll sebessége korlátozott módon működik, és biztosítja, hogy az előtagok nem gyűlik össze, és az írási ellennyomásának nem rendelkezik a alkalmazni. Előtt, a saját írási műveletek munkamenet konzisztenciára vonatkozó garanciákat monoton olvasási, monoton írás, Olvasás, a következő írási-olvasási és konzisztens előtag világszerte garantálja. Az erős konzisztencia konfigurált adatbázisok esetén a rendszer vált vissza egyetlen belül a partíció-készletek vezető kijelölésével írási régióba. 

A Cosmos DB öt konzisztenciamodell szemantikáját leírt [Itt](consistency-levels.md) és matematikai látható egy magas szintű TLA + leírások segítségével [Itt](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>További lépések

Ezután megtudhatja, hogyan globális terjesztés konfigurálása a következő cikkek segítségével:

* [A többkiszolgálós ügyfelek konfigurálása](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Hogyan régiók hozzáadása/eltávolítása az adatbázisból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Egy egyéni ütközésfeloldási házirend SQL API-fiókok létrehozása](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

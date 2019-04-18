---
title: Globális terjesztését az Azure Cosmos DB – technikai részletek
description: Ez a cikk az Azure Cosmos DB globális terjesztési vonatkozó technikai részleteket ismertet
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 84ce13ae3bb0a4b66b8167e61b720fe6cecbe95c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762412"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Globális adatok terjesztési az Azure Cosmos DB – technikai részletek

Az Azure Cosmos DB szolgáltatás eligazodást az Azure-ban, így az összes Azure-régiókban világszerte többek között a nyilvános, a független, a védelmi Minisztérium (DoD) és a kormányzati felhők között központilag telepítették. Egy adatközponton belül azt üzembe helyezése és kezelése az Azure Cosmos DB nagy stampek gépek, az egyes dedikált helyi tárterülettel rendelkező. Egy adatközponton belül az Azure Cosmos DB van üzembe helyezve között számos fürthöz egyes potenciálisan futó hardver generációja több. Fürtön belüli gépek általában 10 – 20 tartalék tartományok ugyanabban a régióban a magas rendelkezésre állású vannak elosztva. Az alábbi képen a Cosmos DB globális terjesztésének topológia látható:

![Topológia](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Az Azure Cosmos DB globális elosztás kulcsrakész:** Bármikor, néhány kattintással vagy programozott módon egyetlen API hívással adja hozzá, vagy távolítsa el a Cosmos-adatbázis társított földrajzi régiókhoz. Egy Cosmos-adatbázis, Cosmos-tárolók készlete áll. A Cosmos DB-tárolók szolgálhat terjesztési és méretezhetőséget biztosít a logikai tárolóegységeket. A gyűjtemények, táblák és létrehozhat a gráfok tárolói (belső) csak Cosmos. A tárolók teljesen sémafüggetlen, és adja meg a lekérdezés hatókörét. Cosmos-tárolóban lévő adatok automatikusan indexelt Adatbetöltési esetén. Automatikus indexelés lehetővé teszi a felhasználók az adatokat, különösen egy globálisan elosztott környezetben a séma vagy index kezelése nélkül.  

- Egy adott régióban tárolóban lévő adatok egy partíciós kulccsal, amely megadja, és átlátható módon kezeli az alapul szolgáló fizikai partíciók használatával vannak (*helyi terjesztési*).  

- Minden egyes fizikai partícióból több földrajzi régióban (*globális terjesztés*). 

Amikor egy alkalmazást rugalmasan Cosmos DB használatával méretezi az átviteli sebességet egy Cosmos-tárolón, vagy további tárhelyet használ, a Cosmos DB transzparens módon kezeli-e a műveleteket (split, klónozza, Törlés) minden olyan régióban. Független a méretezési csoport, terjesztési vagy hibák, Cosmos DB folyamatosan egyetlen rendszerképet a Data-tárolók, amely globálisan bármennyi régió között oszlanak meg.  

Ahogy az az alábbi képen is látható, a tárolóban lévő adatok terjesztése két dimenzió - egy régión belül, és különböző régiókban világszerte mentén:  

![Fizikai partíciók](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Fizikai partíciók replikáit, nevű csoportja által valósul egy *replikakészlethez*. Egyes gépek, amelyek megfelelnek a rögzített folyamatok belül különböző fizikai partíciókra, a fenti képen látható módon replikák több száz üzemelteti. A fizikai partíciók megfelelő replikák dinamikusan kerülnek, és a terheléselosztáshoz a gépek belül egy adott régión belül egy fürt és adatközpontok között.  

A replika egyedi az Azure Cosmos DB-bérlő tartozik. Minden egyes replikának a Cosmos DB példányát futtatja [adatbázismotor](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), amely kezeli az erőforrások, valamint a hozzá tartozó indexeket. A Cosmos DB adatbázismotorja az atom-rekord-szekvencia (ARS) alapuló típus rendszeren működik. A motor független sémát, a rekordok szerkezetét és a példány értékek közötti határ felismerhetetlenné fogalma. A cosmos DB automatikusan indexeli mindent után az Adatbetöltési és hatékonyan, amely lehetővé teszi a felhasználók számára, hogy sémákkal vagy indexkezeléssel kezelése nélkül kérdezheti le a globálisan elosztott adatokon a teljes séma agnosticism biztosít.

A Cosmos database engine végrehajtása több koordináció primitívek nyelvi futtatókörnyezetet, a lekérdezésfeldolgozó és a tárolási és indexelő felelős tranzakciós tárolási alrendszerek és az indexelő az adatokat, többek között összetevőből áll: jelölik. Tartósság és magas rendelkezésre állás biztosítása érdekében az adatbázismotor az adatok és index az SSD-k továbbra is fennáll, és azt replikálja a replika belül motor adatbázispéldányok-készleteket jelölik. Nagyobb méretű bérlők megfelelnek a nagyobb méreteket átviteli sebesség és tárterület, és rendelkezik, vagy nagyobb méretű vagy több replika vagy mindkettőt. A rendszer minden összetevője teljes aszinkron – nincs hozzászóláslánc minden eddiginél letiltja, és minden egyes szál rövid ideig tartó működik olyan felesleges szál kapcsolót anélkül. A sebességhatárolt és vissza – nyomás között a teljes verem a már a betegfelvétel vezérlőből az összes i/o-útvonalat adható hozzá. Cosmos-adatbázismotor részletes egyidejűségi kihasználásához és nagy átviteli sebesség biztosításához belül frugal mennyiségű rendszer-erőforrásokat, miközben tervezték.

Két fő absztrakciók – támaszkodik a cosmos DB globális terjesztésének *-replikakészletekhez* és *partíció-csoportok*. A replikakészlet egy moduláris Lego-letiltása a koordináció, és a egy partíció-set a földrajzilag elosztott egy vagy több fizikai partíciók egy dinamikus területre. Szeretné megismerni globális működik, hogy ismerniük kell ezek két fő absztrakciók. 

## <a name="replica-sets"></a>Replikakészletekhez

Egy fizikai partíciónak a tényleges táblán alapuló replikák több tartalék tartomány, a replikakészlethez nevű elosztva önállóan felügyelt, és dinamikusan kiegyenlített terhelésű csoportként. A készlet együttesen a replikált állapot gép protokollt, hogy a fizikai partíción belül az adatok magas rendelkezésre álló, tartós és egységes valósítja meg. A replikakészlet tagság *N* dinamikus –, tartja között ingadozik *NMin* és *NMax* alapján a hibákat, a felügyeleti műveletek és az időt nem sikerült replikák fiókkulcs újbóli létrehozása vagy a helyreállítás. A tagság végbement változások alapján, a replikáció is protokoll Átkonfigurálás olvasási méretét, és határozatképességére írása. Az átviteli sebességet egy adott fizikai partíciónak hozzárendelt egyenletesen osztja, alkalmazunk két ötleteit: 

- Először a vezető az írási kérelmek feldolgozásának költsége magasabb, mint a frissítések alkalmazása a követő metódus költsége. Ennek megfelelően a vezető tervezett van több rendszererőforrást, mint a követők. 

- Másodszor amennyire csak lehetséges, az adott konzisztenciaszint olvasási kvórum áll kizárólag a követő metódus replikákat. Hogy ne vegye fel a kapcsolatot a vezető, de az olvasási, kivéve, ha szükséges. Ötleteket a történik, a kapcsolat a Research számos alkalmazunk [terhelés és a kapacitás](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) a kvórum-alapú rendszerek esetében a [öt konzisztenciamodell](consistency-levels.md) , amely támogatja a Cosmos DB.  

## <a name="partition-sets"></a>Partíció-csoportok

Egy, az a megadott Cosmos database régióban, az egyes fizikai partíciók alkalmazáscsoportokból ugyanazokat az összes konfigurált régiók közötti replikálására, kulcsok kezeléséhez. Ez magasabb koordináció primitív nevezzük egy *partíció-set* – egy adott halmazát kulcsok kezelése fizikai partíciók egy földrajzilag elosztott dinamikus területre. Egy adott fizikai partíciónak (a replika-készlet) hatókörét a fürtön belül, miközben egy partíció-set is kiterjedhet fürtök, az adatközpontok és a földrajzi régióban, az alábbi képen látható módon:  

![Partíció beállítása](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Egy partíció-set készletként földrajzilag elosztott"super replika-", amely több-replikakészletekhez ugyanazokat a kulcsokat a tulajdonos is felfoghatók. A replikakészlet hasonlóan egy partíció-készlet tagsági is dinamikus –, ingadozik implicit fizikai partíciók felügyeleti műveletek és törlése lehetőségéhez új partíciókat és- tárolókról egy adott partíció-készlet alapján (például ha horizontális felskálázása átviteli sebesség a egy a tároló, hozzáadása egy régiót a Cosmos-adatbázis, vagy ha hiba történik). Alapján fel minden olyan partíció (legyen az egy partíció-) kezelése a saját replikakészlethez partíció-set tagjaik, a tagsági a teljes körűen decentralizált és magas rendelkezésre állású. Egy partíció-készlet az újrakonfigurálás alatt a topológia a fizikai partíciók között átfedés is létrejön. A topológia a konzisztencia szintjét, a földrajzi távolságtól, és a rendelkezésre álló hálózati sávszélesség a forrás- és a cél fizikai partíciók közötti alapján dinamikusan választja.  

A szolgáltatás lehetővé teszi a Cosmos-adatbázisok konfigurálása egy egyetlen írási régió vagy több írási régiót, és a választás, attól függően a pontosan egy vagy minden régióban írások fogadására a partíció-csoportok vannak konfigurálva. A rendszer egy kétszintű, beágyazott konszenzus protokollt használ – egy szint a replikák fogadja az írási műveletek egy fizikai partíciónak replikakészlet belül működik, és a másik egy partíció-készlet teljes sorbarendezésre garanciákat biztosít minden szinten működik a partíció-csoporton belül véglegesített írási műveletek. A többrétegű, beágyazott konszenzus, kritikus fontosságú a magas rendelkezésre állású szigorú szerződései megvalósítását, valamint a Cosmos DB biztosít az ügyfeleinek konzisztenciamodell megvalósítását.  

## <a name="conflict-resolution"></a>Ütközések feloldása

A tervezési, a frissítés propagálás, ütközésfeloldás és okozati nyomon követése a korábbi munkahelyi profilból inspirációt a [járványos algoritmusok](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) és a [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) rendszer. A kernelekkel, ötleteire megélték rendelkezik, és adjon meg egy kényelmes hivatkozási a Cosmos DB-rendszer terve való kommunikációhoz, is estek át jelentős átalakításában, a Cosmos DB rendszer táblázatunk őket. Ez volt szükség, mert a korábbi rendszerekhez készültek, sem az erőforrás-szabályozás, sem a méretezéssel, ahol a Cosmos DB kell megfelelően működjenek, és nem az a funkciók (például korlátozott frissesség konzisztencia) és a szigorú és átfogó SLA-k, amelyek a Cosmos DB biztosít az ügyfeleinek.  

Ne felejtse el, hogy a partíció beállítása legyen elosztva több régióban, és követi a Cosmos DB-k (több főkiszolgálós) replikációs protokoll használatával replikálja az adatokat egy adott partíció-készletet alkotó fizikai partíciók között. Minden egyes fizikai partíciók (legyen az egy partíció-) írási fogad, és olvasási általában szolgál az ügyfelek, amelyek a helyi régióban. Írási műveletek egy adott régión belül fizikai partíciók által elfogadott tartósan véglegesítve és előtt ezeket az ügyfél nem nyugtázza beállítani magas rendelkezésre állását a fizikai partíción belül. Ezek feltételes írások és propagálva lesz másik fizikai partíciók egy víruskereső vysokou csatorna használatával a partíció,-csoporton belül. Ügyfelek lekérdezhetik feltételes vagy véglegesített írások fejléc átadásával. A víruskereső vysokou propagálás (beleértve a propagálás gyakorisága) el dinamikus, a partíció-készletet, a regionális közelség fizikai partíciók és a konzisztencia, beállított szint a topológia alapján. Egy partíció-csoportba a Cosmos DB dinamikusan kiválasztott soron partícióval rendelkező elsődleges véglegesítési séma követi. A soron lehetőség a dinamikus, és szerves része az újrakonfigurálás a partíció-készlet alapján topológiáját, az átmeneti területre. A lefoglalt írás (többek között több-row/kötegelt frissítések) garantáltan meg fog felelni. 

A kódolt vektor órák (régió azonosítója és a megfelelő konszenzus a replikakészlethez: minden egyes szintjét, és a partíció-készlet, logikai órák tartalmazó jelölik) okozati nyomon követése és verzió vektorok észlelése és megoldása érdekében a frissítés, alkalmazunk. A topológia és a társ-kiválasztási algoritmus úgy tervezték, hogy rögzített, és minimális tárolási és a verzióvektor, minimális hálózati terhelést. Az algoritmus a szigorú convergence tulajdonság garantálja.  

A konfigurált több írási régióval rendelkező Cosmos-adatbázisok, a rendszer rugalmas automatikus ütközés számos megoldás olyan szabályzatot kínál a fejlesztőknek, választhat, többek között: 

- **Wins-utolsó-írási (LWW)**, amely alapértelmezés szerint a rendszer által meghatározott időbélyeg-tulajdonság (amely az idő-szinkronizálási órája protokollon alapul) használja. A cosmos DB lehetővé teszi bármely más egyéni numerikus tulajdonság használandó ütközésfeloldás megadása.  
- **Alkalmazás által meghatározott (egyéni) ütközik a névfeloldási házirend** (egyesítési eljárások kifejezve), amely készült alkalmazás által meghatározott szemantika egyeztetés az ütközések. Ezek az eljárások az írás-írás ütközés a kiszolgálói oldalon az adatbázis-tranzakciók felügyelete alatt észlelésekor első meghívni. A rendszer biztosít pontosan egyszer garantálja a kötelezettségvállalás protokoll részeként egy merge eljárás végrehajtására. Nincsenek [több ütköző feloldási minták](how-to-manage-conflicts.md) is kísérletezhet.  

## <a name="consistency-models"></a>Konzisztenciamodell

A Cosmos-adatbázis egy vagy több írási régiót konfigurál, hogy az öt jól definiált konzisztenciamodellekkel közül választhat. Több írási régióval az alábbiakban néhány jelentős aspektusait konzisztenciaszintekről:  

A korlátozott frissesség konzisztencia garantálja, hogy az olvasások belül lesznek *K* előtagok vagy *T* a legújabb írási régiók egyikében területéről. Továbbá korlátozott frissesség konzisztencia az olvasási garantáltan monoton és a konzisztens előtag garanciákkal. A víruskereső vysokou protokoll sebessége korlátozott módon működik, és biztosítja, hogy az előtagok nem gyűlik össze, és az írási ellennyomásának nem rendelkezik a alkalmazni. A saját írási műveletek munkamenet konzisztencia megvalósulásának monoton olvasási, monoton írási, olvasási, írási követi az olvasási és konzisztens előtag garanciákat, világszerte. Az erős konzisztencia konfigurált adatbázisok esetén több írási régiót előnyöket (alacsony írási késést, írási magas rendelkezésre állás) nem érvényes,-régiók között szinkron replikáció miatt.

A Cosmos DB öt konzisztenciamodell szemantikáját leírt [Itt](consistency-levels.md), és matematikai leírt használatával egy magas szintű TLA + specifikációk [Itt](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>További lépések

Ezután megtudhatja, hogyan globális terjesztés konfigurálása a következő cikkek segítségével:

* [Régiók hozzáadása/eltávolítása az adatbázis-fiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [A többkiszolgálós ügyfelek konfigurálása](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Egy egyéni ütközésfeloldási házirend létrehozása](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

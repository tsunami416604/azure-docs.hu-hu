---
title: Az Azure Cosmos DB-ben konzisztenciaszintek |} A Microsoft Docs
description: Az Azure Cosmos DB öt konzisztenciaszint egyenleg végleges konzisztencia, a rendelkezésre állás és a késleltetés kompromisszummal segítségével rendelkezik.
keywords: végleges konzisztencia, az azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 20edcd5e8e3ec3a9d3d294f7a81a2e97b4958f50
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857184"
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Adatok aprólékosan beállítható konzisztenciaszintek az Azure Cosmos DB
Az Azure Cosmos DB célja az alapoktól fel a globális terjesztés szem előtt az összes adatmodell. Előre jelezhető alacsony késési garancia és több jól definiált Könnyített konzisztenciamodellekkel tervezték. Jelenleg a Azure Cosmos DB öt konzisztenciaszintet kínál: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. Kötött elavulás, munkamenet, konzisztens előtag és végleges azok néven "Könnyített konzisztenciamodell", ezek biztosítják, hogy kevesebb konzisztencia erős, mint amelyek a legtöbb erősen konzisztens modell érhető el. 

Mellett a **erős** és **végleges konzisztencia** modellek gyakran által nyújtott elosztott adatbázisok az Azure Cosmos DB három további körültekintően kódolt és szolgáltatáscsomagot konzisztencia modellt kínál:  **korlátozott frissesség**, **munkamenet**, és **konzisztens előtag**. Valós használati esetek alapján egyes ezek konzisztenciaszintek hasznosságát ellenőrzése megtörtént. Együttesen ezek öt konzisztenciaszintek lehetővé teszi, hogy jól indoklással és hátrányokkal konzisztencia, a rendelkezésre állás és a késés. 

Az alábbi videó az Azure Cosmos DB Programigazgatója Andrew Liu a kulcsrakész globális disztribúció funkciókat mutatja be.

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>Elosztott adatbázisok és a konzisztencia
Kereskedelmi forgalomban elérhető adatbázisok két kategóriába sorolhatók: egyáltalán nem kínál jól definiált konzisztenciaszintet hangelemzés, és adatbázisok, amelyek (erős vagy végleges konzisztencia) két szélsőséges programozási lehetőséget kínálnak. 

Az előbbi replikációs protokolljainak részletessége komoly fejfájást okozhat az alkalmazásfejlesztőknek, ráadásul rajtuk múlik az egyensúlyi állapot megteremtése a konzisztencia, a rendelkezésre állás, a késés és az átviteli sebesség között. Az utóbbinál pedig komoly terhet jelent a választás a két véglet közül. A kutatási adatok bősége és a több mint 50 konzisztenciamodellre vonatkozó javaslatok ellenére az elosztott adatbázisok közössége eleddig nem volt képes az erős és végleges konzisztencián túl szabványosítani a konzisztenciaszinteket. A cosmos DB lehetővé teszi, hogy a fejlesztők számára, hogy öt jól definiált konzisztenciamodellekkel konzisztenciaspektrumot – erős, kötött elavulás, választhat [munkamenet](http://dl.acm.org/citation.cfm?id=383631), konzisztens előtag és végleges. 

![Az Azure Cosmos DB több jól definiált (enyhén korlátozott) konzisztenciamodellt is elérhetővé tesz](./media/consistency-levels/five-consistency-levels.png)

Az alábbi táblázat azt illusztrálja, hogy melyik konzisztenciaszint milyen garanciákat biztosít.
 
**Konzisztenciaszintek és garanciák**

| Konzisztenciaszint | Garanciák |
| --- | --- |
| Erős | Linearizálhatósági. Olvasási garantáltan a legújabb verziója található elem visszaadása.|
| Kötött elavulás | Konzisztens előtag. Olvasási késés jelentkezhet írási legfeljebb k verzióval vagy t időközzel |
| Munkamenet   | Konzisztens előtag. Monoton olvasások, monoton írások, írás utáni visszaolvasás, beolvasás utáni írás |
| Konzisztens előtag | A visszaadott frissítések között az összes frissítés néhány egymást követő verziója szerepel, mindig megfelelő sorrendben |
| Végleges  | Nem sorrendben történő olvasások |

Konfigurálhatja az alapértelmezett konzisztenciaszintet Cosmos DB-fiókjában (és később felülbírálhatja a konzisztenciát egy adott olvasási kérésen). Belsőleg az alapértelmezett konzisztenciaszint a partíció készletek, amelyre kiterjedhet régiók belüli azon adatokra vonatkozik. Az Azure Cosmos DB bérlők használata munkamenet-konzisztencia körülbelül 73 % és a 20 %-os inkább korlátozott frissesség. Az Azure Cosmos DB-ügyfelek körülbelül 3 % kísérletezhet a különböző konzisztenciaszintet kezdetben előtt alkalmazását egy adott konzisztencia választás a rendezése. Azure Cosmos DB-bérlők csak 2 % felülbírálása konzisztenciaszintek kérelmek száma alapján. 

A Cosmos DB-olvasások szolgálja ki, munkamenet, konzisztens előtag és végleges konzisztencia kétszer olvasások erős vagy korlátozott frissesség konzisztencia, a költséghatékony. A cosmos DB rendelkezik iparágvezető átfogó SLA-k, beleértve a rendelkezésre állás, az átviteli sebesség és a késleltetés és garantált adatkonzisztenciát biztosítanak. Az Azure Cosmos DB alkalmaz egy [linearizálhatósági ellenőrző](http://dl.acm.org/citation.cfm?id=1806634), amely a szolgáltatási telemetria folyamatosan működik, és Önnek konzisztencia szabálysértések némelyik jelentések. Korlátozott frissesség, az Azure Cosmos DB figyeli, és jelenti a k és t esik a kapcsolatban felmerülő szabálysértéseket. Az összes öt Könnyített konzisztenciaszint, az Azure Cosmos DB is jelent a [probabilistically korlátozott frissesség metrika](http://dl.acm.org/citation.cfm?id=2212359) közvetlenül is.  

## <a name="service-level-agreements"></a>Szolgáltatásiszint-szerződések

Az Azure Cosmos DB kínál átfogó 99,99 %-os [SLA-k](https://azure.microsoft.com/support/legal/sla/cosmos-db/) melyik garancia átviteli sebesség, a konzisztencia, a rendelkezésre állás és a közel valós idejű Azure Cosmos DB adatbázis-azokkal az öt konzisztencia konfigurált egyetlen Azure-régióba hatókörrel rendelkező fiókok szint, vagy több Azure-régióban, azokkal a négy Könnyített konzisztenciaszintet konfigurált átívelő database-fiókok. Továbbá a kiválasztott konzisztenciaszint független, az Azure Cosmos DB kínál egy 99,999 %-os SLA olvasási rendelkezésre állás érdekében két vagy több Azure-régiók átívelő database-fiókok esetében.

## <a name="scope-of-consistency"></a>Konzisztencia hatóköre
A granularitási konzisztencia egyetlen felhasználói kérelem hatókörét. Az írási kérelem előfordulhat, hogy egy insert, replace, upsert felelnek meg, vagy törölje a tranzakció. Írási-olvasási és lekérdezési tranzakció is egyetlen felhasználói kérelem hatókörét. A felhasználó szükség lehet a nagy méretű keresztül böngész eredményhalmaz, több partícióra kiterjedő, de minden olvasási tranzakció hatóköre egyetlen lapon, és ugyanazon a partíción belül szolgáltatja.

## <a name="consistency-levels"></a>Konzisztenciaszintek
Egy alapértelmezett konzisztenciaszintet Cosmos DB-fiókja alatt konfigurálhatja a fiókot, amely az összes tárolókat (és adatbázisok) vonatkozik. Alapértelmezés szerint minden olvasási és a felhasználó által definiált erőforrásokat állították lekérdezéseket megadva az adatbázisfiók alapértelmezett konzisztenciaszintjét használja. A konzisztencia szintjét egy adott olvasási és lekérdezési kérés használatával az egyes, a támogatott API-k is enyhítése. Öt típusa van konzisztenciaszintek az Azure Cosmos DB replikációs protokoll által támogatott, amelyek egy adott konzisztenciagaranciákat és a teljesítmény, közötti egyértelmű kompromisszum ebben a szakaszban leírtak szerint.

<a id="strong"></a>
**Erős**: 

* Erős konzisztencia kínálja a [linearizálhatósági](https://aphyr.com/posts/313-strong-consistency-models) garantálja az olvasások garantált, hogy egy elem legújabb verzióját a. 
* Erős konzisztencia garantálja, hogy az írási csak akkor látható, a replikák kvóruma tartósan véglegesített után. Az írási vagy szinkron módon tartósan által előjegyzett mind az elsődleges és másodlagos példány hozható létre a kvóruma, vagy azt a rendszer megszakította. Olvasási van mindig a többségi olvasási kvórum, az ügyfél számára soha nem látható egy nem véglegesített vagy részleges írása, és minden esetben garantáltan a legújabb nyugtázott írási műveletet olvassa. 
* Erős konzisztencia használatára vannak konfigurálva az Azure Cosmos DB-fiókokhoz nem társítható egynél több Azure-régióba az Azure Cosmos DB-fiókjuk.  
* Olvasási művelet költsége (a [kérelemegységek](request-units.md) felhasznált) erős konzisztencia magasabb, mint a munkamenet és végleges, azonban ugyanaz, mint a korlátozott frissesség.

<a id="bounded-staleness"></a>
**Korlátozott frissesség**: 

* Korlátozott frissesség és garantált adatkonzisztenciát biztosítanak, amelyek az olvasások előfordulhat, hogy lag írásokat legfeljebb *K* verziók vagy egy elem előtagok vagy *t* időköz. 
* Ezért kiválasztása korlátozott frissesség, a "frissesség" konfigurálható kétféleképpen: verziók száma *K* az elem, amely szerint az olvasási késés jelentkezhet az írási és az az időintervallum *t* 
* Korlátozott frissesség ajánlatok teljes globális rendelési kivételével a "frissesség időszakban." A monoton olvasási garancia keretén belül egy régión belül és kívül is a "frissesség ablak." 
* Korlátozott frissesség, munkamenet, konzisztens előtag vagy végleges konzisztencia mint egy erősebb konzisztenciagaranciának biztosít. Globálisan elosztott alkalmazások esetében azt javasoljuk, korlátozott frissesség forgatókönyvek hova az erős konzisztencia rendelkeznek, de is szeretnének a 99,99 %-os rendelkezésre állást és kis késése.   
* Az Azure Cosmos DB-fiókok vannak konfigurálva, korlátozott frissesség konzisztencia tetszőleges számú Azure-régiók társíthatja az Azure Cosmos DB-fiókot. 
* Egy olvasási művelet (tekintetében felhasznált Kérelemegységek) költségét, korlátozott frissesség magasabb, mint a munkamenet és végleges konzisztencia, de ugyanaz, mint az erős konzisztencia.

<a id="session"></a>
**Munkamenet**: 

* Ellentétben az erős és a korlátozott frissesség konzisztenciaszint által kínált globális összhangot modellek egy ügyfél-munkamenet munkamenet-konzisztencia hatókörét. 
* Munkamenet-konzisztencia ideális megoldást biztosít minden olyan esetben, ahol van szó egy eszközre vagy felhasználóra vonatkozik-munkamenetet, mivel ez garantálja, hogy monoton olvasások, monoton írások, és olvassa el a saját írási műveletek (RYW) garantálja. 
* Munkamenet-konzisztencia kiszámítható konzisztenciát biztosít a munkamenet, és maximális olvasási teljesítménye a legkisebb késés írások és olvasások garantál. 
* A munkamenet-konzisztencia konfigurált az Azure Cosmos DB-fiókok Azure Cosmos DB-fiókjuk társíthat tetszőleges számú Azure-régióban. 
* Egy olvasási művelet (tekintetében felhasznált Kérelemegységek) költsége a munkamenet konzisztencia szintjét használata esetén a kisebb, mint az erős és korlátozott frissesség, de több mint végső konzisztenciát.

<a id="consistent-prefix"></a>
**Konzisztens előtag**: 

* Konzisztens előtag garantálja, hogy további írási műveleteket hiányában a csoporton belüli replikák végül lesz. 
* Konzisztens előtag garantálja, hogy olvasások soha nem látható sorrendben írást. Ha írási műveletek lettek végrehajtva a sorrendben `A, B, C`, ügyfél vagy látja majd `A`, `A,B`, vagy `A,B,C`, de soha nem sorrendben például `A,C` vagy `B,A,C`.
* Az Azure Cosmos DB-fiókok vannak konfigurálva, konzisztens előtag konzisztencia tetszőleges számú Azure-régiók társíthatja az Azure Cosmos DB-fiók. 

<a id="eventual"></a>
**Végleges**: 

* Végleges konzisztencia garantálja, hogy további írási műveleteket hiányában a csoporton belüli replikák végül lesz. 
* Végleges konzisztencia a konzisztencia leggyengébb formája, ahol előfordulhat, hogy kap egy ügyfél a régebbi, mint a korábban látott értékeket.
* Végleges konzisztencia leggyengébb olvasás konzisztenciájának biztosít, de az olvasásokat és az írásokat a legkisebb késést biztosít.
* Végleges konzisztencia konfigurált az Azure Cosmos DB-fiókok Azure Cosmos DB-fiókjuk társíthat tetszőleges számú Azure-régióban. 
* Egy olvasási művelet (tekintetében felhasznált Kérelemegységek) költsége a végleges konzisztencia szintje az Azure Cosmos DB konzisztenciaszintek a legalacsonyabb.

## <a name="configuring-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint konfigurálása
1. Az a [az Azure portal](https://portal.azure.com/), az Ugrósávon kattintson **Azure Cosmos DB**.
2. Az **Azure Cosmos DB** oldalon válassza ki a módosítandó adatbázis-fiókot.
3. A fiók lapon kattintson a **alapértelmezett konzisztencia**.
4. Az a **alapértelmezett konzisztencia** lapon válassza ki az új konzisztencia szintjét, és kattintson **mentése**.
   
    ![Képernyőfelvétel a beállítások ikonra, és alapértelmezett konzisztencia bejegyzés menüpont kiemelve](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Konzisztenciaszintek a lekérdezésekhez
Alapértelmezés szerint a felhasználó által definiált erőforrásokat a lekérdezések konzisztenciaszint megegyezik a konzisztenciaszint az olvasásokhoz. Alapértelmezés szerint az index frissítése szinkron módon történik a minden egyes insert, replace vagy elem, a Cosmos DB-tárolók törlése. Ez lehetővé teszi, hogy tartsa tiszteletben a konzisztencia szintjét, pont olvasási lekérdezések. Noha az Azure Cosmos DB írási lett optimalizálva, és támogatja az írást, a szinkron indexkarbantartás és konzisztens-lekérdezések kiszolgálása tartós kötet, konfigurálhatja az egyes tárolók ráérősen frissítéséhez az index. További Lusta indexelő serkenti az írási teljesítményének és ideális tömeges betöltési alkalmazási helyzetekben elsősorban a olvasási műveltekből egy számítási feladat esetén.  

| Az indexelő mód | Olvasások | Lekérdezések |
| --- | --- | --- |
| CONSISTENT (alapértelmezett) |Válassza ki az erős, kötött elavulás, munkamenet, konzisztens előtag vagy végleges |Válassza ki az erős, kötött elavulás, munkamenet, végleges vagy |
| Lusta |Válassza ki az erős, kötött elavulás, munkamenet, konzisztens előtag vagy végleges |Végleges |
| None |Válassza ki az erős, kötött elavulás, munkamenet, konzisztens előtag vagy végleges |Nem alkalmazható |

Mint az olvasási kérések csökkenthető a konzisztencia szintjét egy adott lekérdezés kérelem minden egyes API-ban.

## <a name="consistency-levels-for-the-mongodb-api"></a>Konzisztenciaszintek a MongoDB API-hoz

Az Azure Cosmos DB jelenleg megvalósítja a MongoDB 3.4-es, verziójú rendelkező két konzisztenciabeállítások, erős és végleges. Mivel az Azure Cosmos DB több API-t támogat, a konzisztenciabeállítások a fiók szintjén alkalmazhatók, és a konzisztencia kikényszerítését az egyes API-k vezérlik.  A MongoDB 3.6-ig nem létezett a munkamenet-konzisztencia fogalma, ezért amikor beállít egy MongoDB API-fiókot a munkamenet-konzisztencia használatára, a MongoDB API-k használatakor a rendszer visszaállítja a konzisztenciát véglegesre. Ha „saját írások olvasása” garanciára van szüksége egy MongoDB API-fiókhoz, a fiók alapértelmezett konzisztenciaszintjét erősre vagy kötött elavulásra kell állítani.

## <a name="next-steps"></a>További lépések
Ha szeretné, hogy ehhez a konzisztenciaszintek és kompromisszumot kínál a további olvasó, a következőket javasoljuk:

* [A replikált adatok konzisztencia Baseball példáján (videó) Doug Terry által](https://www.youtube.com/watch?v=gluIh8zd26I)
* [A replikált adatok konzisztencia Baseball példáján (tanulmány) Doug Terry által](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* [Munkamenet garanciákat, kis mértékben konzisztens replikált adatok](http://dl.acm.org/citation.cfm?id=383631)
* [Konzisztencia kompromisszumot kínál a Modern elosztott adatbázis rendszerek kialakításában: korlát a történetet csak egy részét képezi.](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [A gyakorlati részleges határozatképességére valószínűségi korlátozott frissesség (PBS)](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* [Végleges konzisztens – javított változat](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* [A betöltés, a kapacitás és a rendelkezésre állási kvórum rendszerek, a számítási SIAM napló](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* [Line-up: a teljes és automatikus linearizálhatósági-ellenőrző eljárás programozási nyelv tervezési és megvalósítási 2010 ACM SIGPLAN konferencia](http://dl.acm.org/citation.cfm?id=1806634)
* [A gyakorlati részleges határozatképességére probabilistically korlátozott frissesség](http://dl.acm.org/citation.cfm?id=2212359)

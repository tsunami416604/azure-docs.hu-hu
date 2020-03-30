---
title: Teljes tulajdonlási költség (TCO) az Azure Cosmos DB-val
description: Ez a cikk összehasonlítja az Azure Cosmos DB és az IaaS és a helyszíni adatbázisok teljes tulajdonlási költségét
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: b24b69716e472082abfdb388e7d79e88a8e23e64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754795"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Teljes tulajdonlási költség (TCO) az Azure Cosmos DB-val

Az Azure Cosmos DB részletes, több-bérlős működésre és erőforrás-szabályozásra lett tervezve. Ez lehetővé teszi, hogy az Azure Cosmos DB jelentősen alacsonyabb költségekkel működjön, és így a felhasználók pénzt takaríthassanak meg. Jelenleg az Azure Cosmos DB több mint 280 ügyfél általi számítási feladatot támogat gépenként, miközben ennek sűrűsége folyamatosan nő, valamint több ezret egy fürtön belül. Elosztja az ügyfél általi számítási feladatok replikáit egy adott fürt különböző gépei, valamint egy adatközpont több fürtje között a terheléskiegyenlítés érdekében. További információ: [Azure Cosmos DB: A globálisan elosztott adatbázisok határának leszorítása.](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/) Az erőforrások kalirányítását, a több-bérlős és az Azure-infrastruktúra többi részével való natív integrációt, az Azure Cosmos DB átlagosan 4-6-szor olcsóbb, mint a MongoDB, Cassandra vagy más OSS NoSQL, amely iaaS-en fut, és akár 10-szer olcsóbb, mint az adatbázis a helyszínen működő motorok. Lásd a [NoSQL adatbázis-felhőszolgáltatás (nem) tulajdonjogának teljes költségét.](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)

Az OSS NoSQL adatbázis-megoldások, mint például az Apache Cassandra, MongoDB, HBase, motorok a helyszíni használatra tervezték. Felügyelt szolgáltatásként kínálva egyenértékűek egy erőforrás-kezelősablonnal, amely bérlői adatbázissal rendelkezik a kiépített fürtök kezeléséhez és a támogatás figyeléséhez. Az OSS NoSQL architektúrák jelentős működési többletterhelést igényelnek, és a szakértelem nehéz és költséges lehet megtalálni. Másrészt az Azure Cosmos DB egy teljes körűen felügyelt felhőszolgáltatás, amely lehetővé teszi a fejlesztők számára, hogy az üzleti innovációra összpontosítsanak, nem pedig az adatbázis-infrastruktúra kezelésére és karbantartására. 

Ellentétben a felhőnatív adatbázis-szolgáltatás Az Azure Cosmos DB, OSS NoSQL adatbázis-motorok nem tervezték és építették az erőforrás-szabályozás vagy a részletes több-bérlős, mint az alapvető architekturális elveket. Az OSS NoSQL adatbázis-motorjai, mint a Cassandra és a MongoDB, alapvető feltételezést tesznek lehetővé, hogy a virtuális gép minden erőforrása, amelyen futnak, használható. Az adatbázis-motorok közül sok nem működik, ha az erőforrások mennyisége egy bizonyos küszöbérték alá csökken. Például a kis virtuálisgép-példányok, és azok a szállító által ajánlott konfigurációk általában nagy méretű virtuális gépek magasabb költséggel érhető el. Így nem lehet üzemeltetni egy OSS NoSQL vagy bármely más helyszíni adatbázis-motor, és elérhetővé teszi a fogyasztás-alapú töltési modell, például a kérelmek másodpercenként vagy a felhasznált tárhely használatával.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Az Azure Cosmos DB teljes tulajdonlási költsége 

Az Azure Cosmos DB kiszolgáló nélküli kiépítési modellje szükségtelenné teszi az adatbázis-infrastruktúra túlkiépítését. Az Azure Cosmos DB-erőforrások speciális konfigurációk vagy licencelés nélkül állnak rendelkezésre. Ennek eredményeképpen az Azure Cosmos DB-támogatott alkalmazások futhat, mint egy 70 százalékos teljes költség a tulajdonjog-megtakarítás, összehasonlítva oss NoSQL-adatbázisok. Néhány valós idejű példát az [ügyfélhasználati esetekben](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc)talál. Az Azure Cosmos DB díjszabási modell további előnyei a következők:

* **Nagy ár-érték arány:** A piaci elemzők, az ügyfelek és a partnerek megerősítették, hogy az Azure Cosmos DB által kínálandó összes szolgáltatás nagyobb értéket képvisel sokkal alacsonyabb áron, mint amit az ügyfelek kaphatnak, amikor ezeket a megoldásokat saját maguk vagy más szállítókon keresztül valósítják meg. Az adatbázis olyan globális disztribúciót, többfőkiszolgálós, jól definiált és intuitív konzisztenciamodelleket tartalmaz, amelyek az automatikus indexelés jelentősen leegyszerűsödnek az Azure Cosmos DB-vel, anélkül, hogy bonyolult, általános vagy állásidő lenne.

* **Nincs szükség NoSQL DevOps-felügyeletre:** Az Azure Cosmos DB-vel nem kell devops-ot alkalmaznia az üzemelő példányok kezeléséhez, karbantartás végrehajtásához, méretezéséhez vagy javításához. A helyszíni vagy felhőalapú infrastruktúrán üzemeltetett OSS NoSQL-fürttel végrehajthatja az összes olyan számítási feladatot, amelyet a helyszíni vagy felhőalapú infrastruktúrán üzemeltetne.

![Az Azure Cosmos DB tulajdonlási költsége](./media/total-cost-ownership/tco.png)

* **Rugalmas skálázási képesség:** Az Azure Cosmos DB átviteli teljesítmény skálázható felfelé és lefelé, így csökkentheti a nem csúcsidőszakokban a tulajdonlási költségeket. A felhőalapú infrastruktúrán üzembe helyezett OSS NoSQL-fürtök korlátozott rugalmasságot kínálnak, és a helyszíni telepítések definíció szerint nem rugalmasak. Az Azure Cosmos DB-ben, ha nagyobb átviteli, az átviteli rendszer garantáltan lineárisan skálázódik. Ezt a garanciát a pénzügyi SLA-k és a 99.

* **Méretgazdaságosság:** Egy felügyelt szolgáltatás, például az Azure Cosmos DB számos csomócsomóval működik, natívmódon integrálva a hálózatkezeléssel, a tárolással és a számításokkal. Az Azure Cosmos DB nagyméretű szabványosítása miatt költségeket takaríthat meg.

* **Felhőre optimalizálva:** Az Azure Cosmos DB-t az alapoktól kezdve, több bérleti és teljesítmény-elkülönítéssel tervezték. Ez lehetővé teszi a bérlők és munkaterheléseik ezreinek és munkaterheléseinek optimális elhelyezését, végrehajtását és kiegyensúlyozását a fürtök és adatközpontok között. Ezzel szemben az OSS NoSQL-adatbázisok jelenlegi generációja a helyszínen működik, és a teljes virtuális gép feltételezi, hogy egyetlen bérlő számítási feladatai futtatására. Ezek az adatbázisok szintén nem úgy vannak kialakítva, hogy teljes mértékben kihasználják a felhőszolgáltató infrastruktúráját és hardverét. Például egy OSS NoSQL adatbázis-motor nem ismeri a különbséget a virtuális gép, hogy le Vs egy rutin rendszerkép-frissítés, vagy az a tény, hogy a prémium szintű lemez már háromutas replikált. Nem tudja kihasználni ezeket az előnyöket, és adja át az előnyöket és megtakarításokat az ügyfeleknek.

* **Órabérben fizet:** A nagy méretű számítási feladatok, amelyek bármikor kell skálázni, csak az óra díja. Az alkalmazások munkaterhelései általában az év egyes időpontjaiban és a lekérdezett adatok tól függően változnak. Az Azure Cosmos DB segítségével igény szerint fel- és leskálázhatja, és csak azért fizethet, amire szüksége van. A helyszíni vagy IaaS-üzemeltetésű rendszerek nem egyezik meg ezzel a modellel, mert nincs mód a hardver óránként ileszerelésére. Ilyen esetekben az Azure Cosmos DB-vel átlagosan 10–14 alkalommal mentheti meg.

* **Számos funkciót kap ingyen:** Az Azure Cosmos DB-ben az írási számítási feladatok lényegesen olcsóbbak az alternatív adatbázis-szolgáltatásokhoz képest. Emellett az Azure Cosmos DB olyan funkciókat is kínál, mint például [az automatikus indexelés,](indexing-policies.md) [a Time to Live (TTL),](time-to-live.md) [a Feed módosítása](change-feed.md) és mások további díjak nélkül, amit más adatbázis-szolgáltatások általában díjat számítanak fel.

* **Egységes pénznemet használ a különböző munkaterhelésekhez:** Az alternatív ajánlatoktól eltérően az Azure Cosmos DB-ben nem kell a számítási feladatokat például olvasási és írási műveletekre szegmentálni. Vagy a számítási feladatok típusa, amely az olvasási átviteli és az írási átviteli keresztül. Az Azure Cosmos DB-ben a kiosztott átviteli teljesítmény egy egységes és normalizált pénznem használatával van fenntartva a kérelemegységek vagy a RU/mp szempontjából. Ez a megközelítés lehetővé teszi, hogy könnyedén váltsa fel ugyanazt a RU/s-t a különböző műveletek és a munkaterhelés-típusok között.

* **Nem igényel kiépítés virtuális gépek méretezése:** A legtöbb működő adatbázisok megköveteli, hogy menjen a nagy virtuális gépek, hogy elkerüljék a zajos szomszédok és a laza erőforrás-szabályozás, ha azt szeretné, méretezés. Ez a terhet és a költségek előzetes kötelezettségvállalását rója az ügyfelekre. Az Azure Cosmos DB segítségével zökkenőmentesen indíthatja el a nagyméretű számítási feladatok méretét, és anélkül, hogy bármilyen állásidő vagy hatással lenne az adatok rendelkezésre állására.

* **A kiépített átviteli teljesítmény maximális korlátig használható:** Az Azure Cosmos DB almagos multiplexelése alapján a kiosztott átviteli sebességgel nagyobb mértékben telítheti a kiosztott átviteli, mint az IaaS üzemeltetett beállításai vagy harmadik fél ajánlatai. Ez a módszer sokkal többet takarít meg, mint az alternatív megoldások.

* **Az Azure Cosmos DB és más Azure-szolgáltatások mély integrációja.** Az Azure Cosmos DB natív integrációval rendelkezik a hálózatkezelés, a számítási, az Azure Functions (kiszolgáló nélküli), az Azure IoT és más Azure-szolgáltatásokkal. Ezzel az integrációval a legjobb teljesítményt, az adatreplikáció sebességét világszerte, robusztus garanciákkal szerezheti meg. A harmadik féltől származó megoldások nem lesznek képesek egyeztetni, vagy általában díjat számítanak fel az ilyen funkciók nyújtásáért.

* **Alapértelmezés szerint legalább 10–20 tartaléktartományesetén automatikusan magas rendelkezésre állást kap:** Az Azure Cosmos DB támogatja a számítási feladatok elosztását a tartalék tartományok között, amely a magas rendelkezésre állás szempontjából kritikus funkció. Kínál 99.999 magas rendelkezésre állás olvasási és írási a 99 percentilis szerte bárhol a világon. A végrehajtási költségek valami ilyesmi a saját vagy egy harmadik féltől származó megoldás, magas lenne.

* **Automatikusan megkapja az összes vállalati képességet, további költségek nélkül.** Az Azure Cosmos DB a legátfogóbb megfelelőségi tanúsítványokat, biztonságot és titkosítást kínálja inaktív és mozgásban, további költségek nélkül (a versenytársakhoz képest). Automatikusan regionális elérhetőséget kap a világ bármely pontján. Az adatbázis tetszőleges számú Azure-régióban, és bármikor hozzáadhat vagy eltávolíthat régiókat.

* **A költségek akár 65%-át is megtakaríthatja a fenntartott kapacitással:** Az Azure Cosmos DB [fenntartott kapacitása](cosmos-db-reserved-capacity.md) segítségével pénzt takaríthat meg, ha egy évig vagy három évig előre fizet az Azure Cosmos DB-erőforrásokért. A költségeket jelentősen csökkentheti egyéves vagy hároméves előzetes kötelezettségvállalásokkal, és a rendszeres árképzéshez képest 20-65% kedvezményt takaríthat meg. A kritikus fontosságú számítási feladatokban jobb SLA-kat kaphat a kapacitás kiépítése tekintetében.

## <a name="next-steps"></a>További lépések

* További információ [arról, hogy az Azure Cosmos DB díjszabási modellje hogyan költséghatékony az ügyfelek számára?](total-cost-ownership.md)
* További információ [az optimalizálásról fejlesztési és tesztelési célokra](optimize-dev-test.md)
* További információ az [átviteli költség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási költség optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási költségek optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések költségének optimalizálásáról](optimize-cost-queries.md)
* További információ a több régióra kiterjedő [Cosmos-fiókok költségeinek optimalizálásáról](optimize-cost-regions.md)
* További információ [a NoSQL Database Cloud Szolgáltatás (nem) tulajdonjogának teljes költségéről](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)

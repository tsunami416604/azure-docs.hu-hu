---
title: Az Azure Cosmos DB Ownership(TCO) teljes költsége
description: Ez a cikk összehasonlítja a teljes tulajdonosi költség, az Azure Cosmos DB az IaaS és a helyszíni adatbázisok
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: rimman
ms.openlocfilehash: 419ad6681af39aee468a23319b773de23619984d
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555455"
---
# <a name="total-cost-of-ownershiptco-with-azure-cosmos-db"></a>Az Azure Cosmos DB Ownership(TCO) teljes költsége

Az Azure Cosmos DB-a részletes több-bérlős és erőforrás-szabályozási tervezték. Ez a kialakítás lehetővé teszi, hogy az Azure Cosmos DB-mentse jelentősen alacsonyabb költségek és a Súgó felhasználókat kezeljen. Jelenleg az Azure Cosmos DB támogatja a több mint 280 ügyfél munkaterheléseinek egyetlen gépen a sűrűséget folyamatosan növekvő, és több ezer ügyfél munkaterheléseinek a fürtön belül. Ez egy fürtben lévő különböző gépek és a további tudnivalókat a adatközponton belül több fürt között elosztja a terhelést ügyfelek munkaterhelések replikák [Azure Cosmos DB: Hogyan lehet továbbítani rá határát, globálisan elosztott adatbázisokat](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Erőforrás-szabályozási, több-bérlős és natív integrációt olyan Azure-infrastruktúra részeit, mert az Azure Cosmos DB átlagosan 4, 6 felébe, MongoDB, Cassandra vagy más nyílt Forráskódú nosql-alapú IaaS-on, és akár 10 alkalommal olcsóbb, mint az adatbázis fut az a helyszínen futó motorokkal. Tekintse meg a papír [a teljes tulajdonosi költség, (nem), egy NoSQL-adatbázis felhőszolgáltatás](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf).

A nyílt Forráskódú nosql-alapú adatbázis megoldások, például az Apache Cassandra, MongoDB, a HBase- és motorok helyszíni lettek tervezve. Ha egy felügyelt szolgáltatásként érhető el azok a kiépített fürtök kezeléséhez és monitorozásához támogatási bérlői adatbázist a Resource Manager-sablon egyenértékű. Nyílt Forráskódú nosql-alapú architektúrák működési jelentős többletterhelést igényel, és azzal a szakértelemmel bonyolult és költséges találja. Másrészről az Azure Cosmos DB az olyan teljes körűen felügyelt felhőszolgáltatás, amely lehetővé teszi a fejlesztők számára, hogy fókusz üzleti innováció kezeléséről és adatbázis-infrastruktúra karbantartása helyett. 

Egy natív adatbázis-szolgáltatás az Azure Cosmos DB, ellentétben a nosql-alapú nyílt Forráskódú adatbázismotorokat is nem terveztük és készítettük el az erőforrás-szabályozás vagy a minden részletre kiterjedő több-bérlős, az architekturális alapelveket. Nosql-alapú nyílt Forráskódú adatbázismotorokat, például Cassandra, MongoDB, és győződjön meg, egy alapvető feltételezzük, hogy a virtuális gép, amelyen fut az erőforrások elérhetők-e a használatukat. Ezek adatbázismotorokat számos nem fog működni, ha egy meghatározott küszöbérték alá csökken az erőforrás-mennyiséggel. Például kis méretű virtuális gép példányokat és azok érhetők el a szállítói ajánlott konfigurációkat javasolhat általában nagy méretű virtuális gépek magasabb költsége. Így nincs lehetőség egy nyílt Forráskódú nosql-alapú vagy bármely más helyszíni adatbázis-kezelő üzemeltethet, és a egy fogyasztásalapú díjszabási modell használatával például a kérelmek száma második vagy a felhasznált tárolási elérhetővé tenni.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Az Azure Cosmos DB tulajdonosi költségek 

A kiszolgáló nélküli üzembe helyezési modell az Azure Cosmos DB nincs szükség fölösleges üzembe az adatbázis-infrastruktúrával. Az Azure Cosmos DB-erőforrások megadva, nem kell olyan speciális konfigurációkhoz használhatók, vagy a licencelési. Ennek eredményeképpen az Azure Cosmos DB-alapú alkalmazások futtatható, akár a 70 %-os teljes költsége a tulajdonjog-megtakarítás a nyílt Forráskódú NoSQL-adatbázisok képest. Néhány valós idejű példákért lásd [ügyfél használati esetek](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Az Azure Cosmos DB díjszabási modell további előnyei a következők:

* **Az ár származzon:** Piaci elemzők, ügyfelek és partnerek már ellenőrizte az Azure Cosmos DB-hez képest mi magonkénti ezen megoldások megvalósításához, a saját vagy más megoldás segítségével sokkal alacsonyabb áron kínál szolgáltatásokat nagyobb értéket. A database funkcióinak ilyen globális terjesztés, több főkiszolgálós, jól definiált és intuitív konzisztenciamodelleket, Automatikus indexelés vannak nagyban megkönnyíti az Azure Cosmos DB összetettségét, többletterhelést okoz, és üzemkimaradás nélkül.

* **Semmilyen nosql-alapú fejlesztési és üzemeltetési felügyeletet nem szükséges:** Az Azure Cosmos DB egy nem kell fejlesztési és üzemeltetési központi telepítések felügyeletéhez szükséges, karbantartásához, a méretezési csoport vagy a patch a alkalmazni. Az összes számítási feladatot, amely a nyílt Forráskódú nosql-alapú fürt üzemeltetett helyszíni vagy felhőalapú infrastruktúrán kellene tennie hajthat végre.

![Az Azure Cosmos DB tulajdonosi költségek](./media/total-cost-ownership/tco.png)

* **Rugalmasan méretezhető képessége:** Az Azure Cosmos DB-átviteli méretezhetők felfelé és lefelé, így csúcsidőn – a tulajdonosi költségek csökkentése érdekében. Nyílt Forráskódú nosql-alapú fürtök üzembe helyezett a felhőalapú infrastruktúra korlátozott rugalmasságot kínál, és a helyszíni üzemelő példányok nem rugalmas definíció szerint. Az Azure Cosmos DB Ha üzembe helyezi a kapacitás növelése érdekében, az átviteli sebességet garantáltan lineárisan lehessen skálázni. Ez garantálja a biztonsági mentését, a pénzügyi SLA-k, és bármilyen méretben 99 százalékon.

* **Méretgazdaságosság:** Egy felügyelt szolgáltatás, például az Azure Cosmos DB, nagy számú csomópont esetén működik natív módon integrálva van a hálózati, tárolási és számítási erőforrások. Mivel az Azure Cosmos DB nagy méretű, mentheti a költségek szabványügyi szervezet.

* **Optimalizálva a felhőben:** Az Azure Cosmos DB részletes több-bérlős és a teljesítmény elkülönítését az alapoktól tervezték. Ez lehetővé teszi az optimális elhelyezése, végrehajtását és a bérlők és a számítási feladatok több ezer terheléselosztási fürtök és adatközpontok között. Ezzel szemben a nyílt Forráskódú NoSQL-adatbázisok a jelenlegi generációja működnek a helyszíni a teljes virtuális gépet feltételezi, hogy egyetlen bérlő számítási feladatok futtatásához. Ezek az adatbázisok nem is célja egy felhőszolgáltató infrastruktúra hardver- és a teljes mértékben kihasználni. Például egy nyílt Forráskódú nosql-alapú adatbázis-kezelő nem Vs le a virtuális gép folyamatban közötti különbségek figyelembe rutin lemezkép frissítése, vagy azt a tényt, hogy prémium szintű lemez már háromutas replikálja. Ez nem kihasználhatja ezeket az előnyöket, és adja át az előnyök és megtakarítások az ügyfelek számára.

* **Óránkénti fizet:** Nagy méretű számítási feladatok esetén, hogy bármikor idő alatt szükség van, csak alapján számítjuk fel az órát. A számítási feladatok az alkalmazások általában az év, és a lekérdezett adatok között változhat. Az Azure Cosmos DB skálázhatja felfelé vagy lefelé kell, és csak akkor kell fizetnie. Helyszíni vagy IaaS által üzemeltetett rendszerekkel ebben a modellben nem egyezik, mert nincs olyan módon, a hardver óránként leszerelése. Ezekben az esetekben is potenciálisan mentheti közötti 10-14-szerese az Azure Cosmos DB az átlagos.

* **Számos funkciója ingyenesen kap:** Az Azure Cosmos DB a számítási feladatok lényegében olcsóbb összehasonlítja másodlagos adatbázis-szolgáltatások írni. Emellett az Azure Cosmos DB szolgáltatást kínál például például [Automatikus indexelés](indexing-policies.md), [élettartamot (TTL)](time-to-live.md), [módosítási hírcsatorna](change-feed.md) és más egyéb költségek nélkül valami más adatbázis-szolgáltatások jellemzően díja szerint számítjuk fel.

* **Használja az egységes pénznem a különböző számítási feladatokhoz:** Alternatív, az Azure Cosmos DB-ajánlatoktól eltérően nem kell szegmens számítási feladatok, például az olvasási és írási. Vagy üzembe helyezése átviteli sebességet egy / átviteli sebesség és a lemezírás teljesítménye olvasható számításifeladat-típust. Az Azure Cosmos DB egy egységes és normalizált pénznem átvitelek kérelemegysége kiosztott átviteli sebesség foglalt, vagy RU/is lehet Azure Cosmos DB nem kényszerítheti, hogy prioritást rendelhet a számítási feladatok, a kapacitástervezés vagy a különböző típusú kapacitást kell fizetnie külön-külön. Az ilyen megközelítés lehetővé teszi, hogy könnyen adatcsere különféle műveletek és számítási feladatok típusa között ugyanazon RU/s.

* **Méretezhető virtuális gépek kiépítése nem igényel:** A legtöbb operatív adatbázis szükséges, hogy nyissa meg a nagyméretű virtuális gépek zajos szomszédok elkerülése érdekében, és a laza erőforrás-szabályozás, ha azt szeretné, hogy a méretezési csoport. Az ügyfelek a terheket és a költség az előzetes kötelezettségvállalásától ez használatával. Az Azure Cosmos DB is kezdhetik, és zökkenőmentesen, és nélkül semmilyen kimaradást vagy az adatok rendelkezésre állását a művelet hatása a nagy méretű számítási feladatok méretek továbbléphetnek.

* **A maximális kiosztott átviteli sebesség használhat:** Alárendelt core az Azure Cosmos DB multiplexálási rendelkezik akkor is telítsük nagyobb mértékben IaaS üzemel, beállítások vagy harmadik fél kínál, mint a kiosztott átviteli sebesség. Ez a módszer kíméli sokkal több, mint az alternatív megoldások.

* **Szoros integráció az Azure Cosmos DB más Azure-szolgáltatásokkal.** Az Azure Cosmos DB natív integráció hálózati, számítási, az Azure Functions (a kiszolgáló nélküli), az Azure IoT és más Azure-szolgáltatásokkal rendelkezik. Ez az integráció a legjobb teljesítmény érdekében az adatreplikációt a robusztus garanciákkal világszerte sebességétől kap. A harmadik féltől származó megoldások nem tudják megfelelően, vagy szeretné általában díja szerint számítjuk fel az ilyen szolgáltatásokkal prémium.

* **Magas rendelkezésre állás érdekében legalább 10 – 20 tartalék tartományokkal alapértelmezés szerint automatikusan megkapja:** Az Azure Cosmos DB támogatja az számítási feladatok több tartalék tartományba, a szolgáltatás, amelyet a kritikus fontosságú a magas rendelkezésre állás érdekében. Az olvasások 99.999 magas rendelkezésre állást biztosít, és a különböző bárhol az írási műveleteknél a 99. percentilis a világon. A végrehajtási, valami ilyesmit saját maga, vagy egy külső megoldás segítségével költség magas lehet.

* **Automatikusan megkapja az összes nagyvállalati funkciókat, további költségek nélkül.** Azure Cosmos DB kínálja a legátfogóbb megfelelőségi tanúsítványok, a biztonság és a titkosítás inaktív és a mozgásban lévő (a versenytársainkat képest) további költségek nélkül. Régiónkénti rendelkezésre állás bárhol a világon automatikusan kap. Akkor is kiterjednek az adatbázis tetszőleges mennyiségű Azure-régiók és régiók hozzáadása vagy eltávolítása bármikor.

* **Akár 65 %-a költségek mentheti a lefoglalt kapacitás:** Az Azure Cosmos DB [fenntartott kapacitás](cosmos-db-reserved-capacity.md) révén pénzt takaríthat meg előre erőforrásokért kellene fizetnie Azure Cosmos DB egyéves vagy hároméves. Jelentősen csökkentheti költségeit egyéves vagy hároméves előzetes kötelezettségvállalás a, és a 20-65 %-os engedményt képest. a normál díjszabás közötti mentése. Az alapvető fontosságú számítási feladatok hatékonyabb SLA-k tekintetében kapacitás kiépítése kérheti le.

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [hogyan Azure Cosmos DB díjszabási modell az ügyfelek költséghatékony](total-cost-ownership.md)
* Tudjon meg többet [optimalizálása fejlesztéshez és teszteléshez](optimize-dev-test.md)
* Tudjon meg többet [átviteli költségek optimalizálása](optimize-cost-throughput.md)
* Tudjon meg többet [tárolási költségek optimalizálása](optimize-cost-storage.md)
* Tudjon meg többet [olvasási és írási a költségek optimalizálása](optimize-cost-reads-writes.md)
* Tudjon meg többet [lekérdezések költségeinek optimalizálása](optimize-cost-queries.md)
* Tudjon meg többet [többrégiós Cosmos-fiókok költségeinek optimalizálása](optimize-cost-regions.md)
* Tudjon meg többet [(nem) tulajdonjogát egy NoSQL-adatbázis Felhőbeli szolgáltatás a teljes költsége](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)

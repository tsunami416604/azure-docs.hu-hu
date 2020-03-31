---
title: Átviteli átbocsátás kiépítése az Azure Cosmos-tárolókon és -adatbázisokon
description: Megtudhatja, hogyan állíthatja be az Azure Cosmos-tárolók és -adatbázisok kiépített átviteli mertét.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: e7a64776cba00a6840af70cecad5bf9c02b3f38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251972"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Átviteli sebesség kiosztása tárolókra és adatbázisokra

Az Azure Cosmos-adatbázis egy tárolókészlet felügyeleti egysége. Az adatbázis séma-független tárolók készletéből áll. Az Azure Cosmos-tároló az átviteli és tárolási méretezhetőségi egysége. Egy tároló vízszintesen particionált egy Azure-régión belüli gépek között, és az Azure Cosmos-fiókhoz társított összes Azure-régióközött elosztva van elosztva.

Az Azure Cosmos DB-vel két részletességgel építheti ki az átviteli
 
- Azure Cosmos-tárolók
- Azure Cosmos-adatbázisok

## <a name="set-throughput-on-a-container"></a>Átviteli fazon beállítása tárolón  

Az Azure Cosmos-tárolóban kiosztott átviteli szolgáltatás kizárólag az adott tároló számára van fenntartva. A tároló mindig megkapja a kiosztott átviteli. A tároló nizált átviteli egy tároló pénzügyileg támogatja az SLA-k. Az Azure Cosmos-tároló átviteli átviteli címének konfigurálásáról az [Azure Cosmos-tárolóátviteli-idő kiépítése](how-to-provision-container-throughput.md)című témakörben olvashat.

A tárolón a kiosztott átviteli fazon beállítása a leggyakrabban használt beállítás. Egy tároló átviteli keresztüli átalódrugalmasan skálázható a [kérelemegységek (RUs)](request-units.md)használatával bármilyen mennyiségű átviteli igény kiépítésével. 

A tárolóhoz kiépített átviteli teljesítmény egyenletesen oszlik el a fizikai partíciók között, és feltételezve, hogy egy jó partíciókulcs, amely egyenletesen osztja el a logikai partíciókat a fizikai partíciók között, az átviteli teljesítmény is egyenletesen oszlik el az összes a tároló logikai partícióit. A logikai partíciók átviteli fókusa nem adható meg szelektíven. Mivel egy tároló egy vagy több logikai partícióját egy fizikai partíció üzemelteti, a fizikai partíciók kizárólag a tárolóhoz tartoznak, és támogatják a tárolón kiosztott átviteli hangot. 

Ha a logikai partíción futó számítási feladat többet használ fel, mint az adott logikai partícióhoz lefoglalt átviteli sebesség, a műveletek díjkorlátot kapnak. Sebességkorlátozás esetén növelheti a teljes tároló kiosztott átviteli sebességét, vagy újrapróbálkozhat a műveletektel. A particionálásról további információt a [Logikai partíciók](partition-data.md)című témakörben talál.

Azt javasoljuk, hogy konfigurálja az átviteli teljesítményt a tároló részletességét, ha azt szeretné, hogy a tároló garantált teljesítményt.

Az alábbi képen látható, hogy egy fizikai partíció hogyan üzemelteti a tároló egy vagy több logikai partícióját:

![Fizikai partíció](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Átviteli adatbeállítás adatbázisban

Amikor egy Azure Cosmos-adatbázis ban átviteli, az átviteli keresztül az adatbázisban lévő összes tároló (úgynevezett megosztott adatbázis-tárolók) között meg oszlik. Kivételt képez, ha az adatbázis adott tárolóira adott kiosztott átviteli hatóát adott meg. Az adatbázis-szintű kiosztott átviteli hang megosztása a tárolók között hasonló az adatbázis gépek fürtön való üzemeltetéséhez. Mivel az adatbázison belüli összes tároló megosztja a számítógépen elérhető erőforrásokat, természetesen nem kap kiszámítható teljesítményt egy adott tárolóban. A kiépített átviteli hang konfigurálása egy adatbázisban a [Kiépített átviteli hang konfigurálása az Azure Cosmos-adatbázisban](how-to-provision-database-throughput.md)című témakörben olvashat.

Az Azure Cosmos-adatbázis átviteli-átviteli állásának beállítása garantálja, hogy az adatbázis hoz létre az adatbázis mindig a kiosztott átviteli. Mivel az adatbázison belüli összes tároló osztozik a kiosztott átviteli fazonban, az Azure Cosmos DB nem biztosít előre látható átviteli garanciát az adatbázis egy adott tárolójához. Az átviteli fazeka egy adott tároló által kapható része a következőktől függ:

* A konténerek száma.
* A különböző tárolók partíciókulcsainak kiválasztása.
* A számítási feladatok elosztása a tárolók különböző logikai partíciói között. 

Azt javasoljuk, hogy konfigurálja az átviteli egy adatbázisban, ha meg szeretné osztani az átviteli több tárolóközött, de nem szeretné az átviteli cím egy adott tárolóra. 

Az alábbi példák bemutatják, hogy az adatbázis szintjén hol érdemes az átviteli szintet kiépíteni:

* Az adatbázis kiosztott átviteli csatornájának megosztása a tárolók között hasznos egy több-bérlős alkalmazás számára. Minden felhasználó egy külön Azure Cosmos-tárolóval jeleníthető meg.

* Az adatbázis kiosztott átviteli közbeni használata a tárolók között akkor hasznos, ha áttelepít egy NoSQL-adatbázist, például a MongoDB-t vagy a Cassand-ot, amely virtuális gépek fürtjében vagy helyszíni fizikai kiszolgálókról az Azure Cosmos DB-re van telepítve. Gondoljon az Azure Cosmos-adatbázisban konfigurált kiosztott átviteli értékre, mint egy logikai egyenértékű, de költséghatékonyabb és rugalmasabb, a MongoDB vagy a Cassandra-fürt számítási kapacitásával.  

A kiosztott átviteli függményt tartalmazó adatbázison belül létrehozott összes tárolót [partíciókkal](partition-data.md)kell létrehozni. Bármely adott időpontban az adatbázison belüli tárolóhoz lefoglalt átviteli sebességgel a tároló összes logikai partíciója között oszlik meg. Ha a tárolók, amelyek megosztják a kiosztott átviteli áteresztőképes adatbázisban konfigurálva, nem lehet szelektíven alkalmazni az átviteli egy adott tárolóra vagy egy logikai partícióra. 

Ha a logikai partíción lévő munkaterhelés több, mint egy adott logikai partíciószámára lefoglalt átviteli sebesség, a műveletek sebességkorlátozottak. Sebességkorlátozás esetén növelheti a teljes adatbázis átviteli sebességét, vagy újra próbálkozhat a műveletektel. A particionálásról további információt a [Logikai partíciók](partition-data.md)című témakörben talál.

A megosztott átviteli-adatbázis tárolói megosztják az adatbázishoz rendelt átviteli -áteresztőkapcsolót (RU/s).Containers in a shared throughput database share the throughput (RU/s) allocated to that database. Legfeljebb négy tárolók legalább 400 RU/s az adatbázisban. Minden új tartály az első négy után további 100 RU/s minimumot igényel. Ha például nyolc tárolóval rendelkező megosztott átviteli-adatbázissal rendelkezik, az adatbázis minimális RU/s-a 800 RU/s lesz.

> [!NOTE]
> 2020 februárjában bevezettünk egy módosítást, amely lehetővé teszi, hogy legfeljebb 25 tárolót töltsön be egy megosztott átviteli-adatbázisban, amely jobban lehetővé teszi az átviteli teljesítmény megosztását a tárolók között. Az első 25 tároló után csak akkor adhat hozzá további tárolókat az adatbázishoz, ha azok dedikált átviteli úgy vannak [kiépítve,](#set-throughput-on-a-database-and-a-container)amely elkülönül az adatbázis megosztott átviteli jától.<br>
Ha az Azure Cosmos DB-fiókja már tartalmaz egy megosztott átviteli adatbázist >=25 tárolóval, a fiók és az összes többi fiók ugyanabban az Azure-előfizetésben mentesülnek a módosítás alól. Kérjük, [lépjen kapcsolatba a terméktámogatási szolgálattal,](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ha van visszajelzése vagy kérdése. 

Ha a számítási feladatok az adatbázis összes gyűjteményének törlését és újbóli létrehozását foglalják magukban, ajánlott az üres adatbázist eldobni, és a gyűjtemény létrehozása előtt újra létrehozni egy új adatbázist. Az alábbi képen látható, hogy egy fizikai partíció hogyan tud egy vagy több logikai partíciót üzemeltetni, amelyek az adatbázis különböző tárolóihoz tartoznak:

![Fizikai partíció](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Átviteli ár beállítása adatbázison és tárolón

Kombinálhatja a két modellt. Az adatbázis és a tároló átviteli átbocsátása engedélyezett. A következő példa bemutatja, hogyan építheti ki az átviteli szintet egy Azure Cosmos-adatbázisban és egy tárolóban:

* Létrehozhat egy *Z* nevű Azure Cosmos-adatbázist *a "K"* rt-ek kiépített átviteli feszültségével. 
* Ezután hozzon létre öt *A,* *B,* *C,* *D*és *E* nevű tárolót az adatbázison belül. A B tároló létrehozásakor győződjön meg arról, hogy engedélyezi **a dedikált átviteli-átviteli a tároló beállítás,** és explicit módon konfigurálja *a "P"* rúpiák a kiosztott átviteli ebben a tárolóban. Vegye figyelembe, hogy a megosztott és dedikált átviteli hang csak az adatbázis és a tároló létrehozásakor konfigurálható. 

   ![Az átviteli rendszer beállítása a tároló szintjén](./media/set-throughput/coll-level-throughput.png)

* A *"K"* rt-átviteli átmenő t a négy *A,* *C,* *D*és *E*tároló között osztják meg. Az *A,* *C,* *D*vagy *E* számára rendelkezésre álló átviteli teljesítmény pontos mennyisége változó. Nincsenek SL-ek az egyes tárolók átviteli.
* A *B* nevű tároló garantáltan mindig megkapja a *"P"* rus átviteli szintet. Az SLA-k támasztják alá.

> [!NOTE]
> A kiépített átviteli fokkal rendelkező tároló nem konvertálható megosztott adatbázis-tárolóvá. Ezzel szemben egy megosztott adatbázis-tároló nem konvertálható dedikált átviteli fokkal.

## <a name="update-throughput-on-a-database-or-a-container"></a>Átviteli adatszolgáltatás frissítése adatbázison vagy tárolón

Miután létrehozott egy Azure Cosmos-tárolót vagy egy adatbázist, frissítheti a kiosztott átviteli fazont. Nincs korlátozva a maximális kiosztott átviteli, amely konfigurálható az adatbázisban vagy a tárolóban. A [minimálisan kiosztott átviteli arány](concepts-limits.md#storage-and-throughput) a következő tényezőktől függ: 

* A tárolóban valaha tárolt maximális adatméret
* A tárolóra valaha kiosztott maximális átviteli teljesítmény
* A megosztott átviteli eresztővel rendelkező adatbázisban található Azure Cosmos-tárolók aktuális száma. 

Lekérheti a tároló vagy egy adatbázis minimális átviteli értékét programozott módon az SDK-k használatával, vagy megtekintheti az értéket az Azure Portalon. A .NET SDK használataesetén a [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) metódus lehetővé teszi a kiosztott átviteli érték méretezését. A Java SDK használata esetén a [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) metódus lehetővé teszi a kiosztott átviteli érték méretezése. 

A .NET SDK használataesetén a [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) metódus lehetővé teszi egy tároló vagy adatbázis minimális átviteli idejének beolvasását. 

A tároló vagy az adatbázis kiosztott átviteli fazekadt átméretezése bármikor. Ha egy méretezési műveletet hajt végre az átviteli rendszer növelése érdekében, a rendszerfeladatok miatt hosszabb időt vehet igénybe a szükséges erőforrások kiépítése. Ellenőrizheti a méretezési művelet állapotát az Azure Portalon vagy programozott módon az SDK-k használatával. A .Net SDK használatakor a méretezési művelet állapotát `DocumentClient.ReadOfferAsync` a módszerrel kaphatja meg.

## <a name="comparison-of-models"></a>A modellek összehasonlítása

|**Paraméter**  |**Adatbázisban kiosztott átviteli adatáta**  |**Tárolón kiosztott átviteli adatáta**|
|---------|---------|---------|
|Minimális RUs |400 (Az első négy tároló után minden további tárolóhoz legalább 100 vtsz szükséges másodpercenként.) |400|
|Minimális rus-k tárolónként|100|400|
|Maximális rus|Korlátlan, az adatbázisban.|Korlátlan, a konténeren.|
|Adott tárolóhoz rendelt vagy elérhető futóegység|Nincs garancia. Egy adott tárolóhoz rendelt több tároló tulajdonságaitól függ. Tulajdonságok lehet a partíciókulcsok az átviteli terhelést, a számítási feladatok elosztását és a tárolók számát használó tárolók partíciókulcsainak kiválasztása. |A tárolón konfigurált összes fenntartott fenntartott rendszeregység kizárólag a tároló számára van fenntartva.|
|A tárolók maximális tárolása|Korlátlan.|Korlátlan.|
|A tároló logikai partíciónkénti maximális átviteli-áteresztőjele|10k rus|10k rus|
|Maximális tárolás (adat + index) tároló logikai partíciónként|20 GB|20 GB|

## <a name="next-steps"></a>További lépések

* További információ a [logikai partíciókról.](partition-data.md)
* Ismerje meg, hogyan [építheti ki az átviteli fazonát egy Azure Cosmos-tárolóban.](how-to-provision-container-throughput.md)
* Ismerje meg, hogyan [építheti ki az átviteli mertét egy Azure Cosmos-adatbázisban.](how-to-provision-database-throughput.md)


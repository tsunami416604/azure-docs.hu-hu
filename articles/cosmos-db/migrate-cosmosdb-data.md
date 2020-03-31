---
title: Több száz terabájtnyi adat migrálása az Azure Cosmos DB-be
description: Ez a dokumentum leírja, hogyan lehet áttelepíteni 100s terabájtnyi adatot Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: bharathb
ms.openlocfilehash: 69b400eb7838c986ac6f275da58c7457179ebea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72880206"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Több száz terabájtnyi adat migrálása az Azure Cosmos DB-be 

Az Azure Cosmos DB több terabájtnyi adatot tud tárolni. Nagy léptékű adatmigrálás elvégzésével áthelyezheti éles számítási feladatait az Azure Cosmos DB-be. Ez a cikk azokat a kihívásokat ismerteti, amelyek a nagy léptékű adatok az Azure Cosmos DB-be való áthelyezésével kapcsolatosak, továbbá bemutatja az eszközt, amely segít a kihívások leküzdésében, és az adatok az Azure Cosmos DB-be történő migrálásában. Ebben az esettanulmányban az ügyfél a Cosmos DB SQL API-t használta.  

Mielőtt áttelepíti a teljes számítási feladatok at Azure Cosmos DB, áttelepítheti az adatok egy részét, hogy érvényesítse néhány szempontot, például a partíciókulcs választás, lekérdezési teljesítmény és az adatmodellezés. A koncepció igazolásának ellenőrzése után áthelyezheti a teljes számítási feladatot az Azure Cosmos DB-be.  

## <a name="tools-for-data-migration"></a>Eszközök az adatok áttelepítéséhez 

Az Azure Cosmos DB áttelepítési stratégiák jelenleg eltérnek az API-választás és az adatok mérete alapján. Kisebb adatkészletek áttelepítéséhez – az adatmodellezés, a lekérdezési teljesítmény, [a](import-data.md) partíciókulcs-választás stb. [Azure Data Factory’s Azure Cosmos DB connector](../data-factory/connector-azure-cosmos-db.md) Ha ismeri a Sparkot, az Azure [Cosmos DB Spark-összekötő](spark-connector.md) használatával is használhatja az adatok áttelepítését.

## <a name="challenges-for-large-scale-migrations"></a>A nagyléptékű migrációval kapcsolatos kihívások 

Az Azure Cosmos DB-be való áttelepítés meglévő eszközeinek vannak bizonyos korlátai, amelyek nagy léptékben különösen nyilvánvalóvá válnak:

 * **Korlátozott horizontális felskálázási képességek:** Annak érdekében, hogy több terabájtnyi adatot migráljon az Azure Cosmos DB-be a lehető leggyorsabban, és hatékonyan használja fel a teljes kiosztott átviteli kapacitást, az áttelepítési ügyfelek számára lehetővé kell tenni a korlátlan horizontális felskálázást.  

* **A folyamatkövetés és az ellenőrzés-meghatározás hiánya**: Fontos nyomon követni az áttelepítés előrehaladását, és ellenőrizni a nagy adatkészletek áttelepítése közben az ellenőrzést. Ellenkező esetben az áttelepítés során előforduló hibák leállítják az áttelepítést, és a folyamatot teljesen elölről kell elindítani. Nem lenne produktív a teljes migrációs folyamat újraindítása, ha annak 99%-a már befejeződött.  

* **Kézbesítetlen levelek várólistájának hiánya:** Nagy adatkészleteken belül bizonyos esetekben problémák merülhetnek fel a forrásadatok egyes részeivel. Emellett előfordulhat, hogy átmeneti problémák vannak az ügyféllel vagy a hálózattal. Az esetek bármelyike nem okozhatja a teljes áttelepítés sikertelensét. Annak ellenére, hogy a legtöbb áttelepítési eszköz robusztus újrapróbálkozási képességekkel rendelkezik, amelyek megvédik az időszakos problémákat, ez nem mindig elegendő. Ha például a forrásadatok dokumentumainak kevesebb mint 0,01%-a nagyobb, mint 2 MB, akkor a dokumentum írási sikertelené válik az Azure Cosmos DB-ben. Ideális esetben hasznos, ha az áttelepítési eszköz ezeket a "sikertelen" dokumentumokat egy másik kézbesítetlen levél várólistára állítja, amely az áttelepítés után feldolgozható. 

Ezek közül a korlátozások közül sok ki van javítva olyan eszközökhöz, mint az Azure Data factory, az Azure Data Migration services. 

## <a name="custom-tool-with-bulk-executor-library"></a>Egyéni eszköz tömeges végrehajtó tárral 

A fenti szakaszban leírt kihívások megoldhatók egy egyéni eszközzel, amely könnyen kiskálázható több példányra, és rugalmas az átmeneti hibákkal szemben. Emellett az egyéni eszköz szüneteltetheti és folytathatja az áttelepítést különböző ellenőrzőpontokon. Az Azure Cosmos DB már biztosítja a [tömeges végrehajtó könyvtárat,](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) amely tartalmazza ezeket a funkciókat. Például a tömeges végrehajtó könyvtár már rendelkezik az átmeneti hibák kezeléséhez szükséges funkciókkal, és egyetlen csomópontban lévő szálak at skálázhatja, hogy csomópontonként körülbelül 500 K rus-t használjon fel. A tömeges végrehajtó könyvtár is particionálja a forrás adatkészletet a mikro-kötegek, amelyek egymástól függetlenül működnek ellenőrzőpontok formájában.  

Az egyéni eszköz a tömeges végrehajtó könyvtárat használja, és támogatja a több ügyfél közötti horizontális felskálázást, és a betöltési folyamat során elkövetett hibák nyomon követését. Az eszköz használatához a forrásadatokat külön-külön kell particionálni az Azure Data Lake Storage (ADLS) szolgáltatásban, hogy a különböző áttelepítési dolgozók felvehetik az egyes fájlokat, és betöltése az Azure Cosmos DB-be. Az egyéni eszköz egy külön gyűjteményt használ, amely az egyes forrásfájlok áttelepítési folyamatának metaadatait tárolja az ADLS-ben, és nyomon követi a hozzájuk kapcsolódó hibákat.  

Az alábbi kép az egyéni eszközzel történő áttelepítési folyamatot ismerteti. Az eszköz virtuális gépek készletén fut, és minden virtuális gép lekérdezi a követési gyűjtemény az Azure Cosmos DB-ben, hogy a forrás adatpartíciók egyikének bérletét szerezze be. Ha ez megtörtént, az eszköz beolvassa a forrásadat-partíciót, és a tömeges végrehajtó kódtár használatával az Azure Cosmos DB-be kerül. Ezután a nyomon követési gyűjtemény frissül, hogy rögzítse az adatbetöltés előrehaladását és a felmerült hibákat. Az adatpartíció feldolgozása után az eszköz megpróbálja lekérdezni a következő elérhető forráspartíciót. Továbbra is feldolgozza a következő forráspartíciót, amíg az összes adat áttelepítése meg nem történik. Az eszköz forráskódja [itt](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion)érhető el.  

 
![Áttelepítési eszköz beállítása](./media/migrate-cosmosdb-data/migrationsetup.png)
 

 

A nyomkövetési gyűjtemény az alábbi példában látható dokumentumokat tartalmazza. Látni fogja az ilyen dokumentumok minden partícióhoz a forrásadatokban.  Minden dokumentum tartalmazza a forrásadat-partíció metaadatait, például a helyét, az áttelepítés állapotát és a hibákat (ha vannak):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Az adatok áttelepítésének előfeltételei 

Az adatáttelepítés megkezdése előtt néhány előfeltételt figyelembe kell venni:  

#### <a name="estimate-the-data-size"></a>Az adatok méretének becslése:  

Előfordulhat, hogy a forrásadatok mérete nem pontosan az Azure Cosmos DB adatméretéhez van leképezve. Néhány minta dokumentumok a forrásból beilleszthető az adatok méretének ellenőrzéséhez az Azure Cosmos DB. Amintadokumentum méretétől függően az Azure Cosmos DB teljes adatmérete az áttelepítés után megbecsülhető. 

Ha például az Azure Cosmos DB-ben való áttelepítés után minden dokumentum körülbelül 1 KB, és ha a forrásadatkészletben körülbelül 60 milliárd dokumentum található, az azt jelentené, hogy az Azure Cosmos DB becsült mérete megközelíti a 60 TB-ot. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Hozzon létre elegendő rus-t tartalmazó tárolókat: 

Bár az Azure Cosmos DB automatikusan skálázódik a tároló, nem tanácsos a legkisebb tárolóméretéből kiindulni. Kisebb tárolók alacsonyabb átviteli rendelkezésre állási, ami azt jelenti, hogy az áttelepítés sokkal hosszabb időt vesz igénybe. Ehelyett hasznos a tárolók létrehozása a végleges adatméret (az előző lépésben becsült), és győződjön meg arról, hogy az áttelepítési számítási feladatok teljes mértékben felhasználja a kiosztott átviteli.  

Az előző lépésben. mivel az adatok méretét körülbelül 60 TB-ra becsülték, a teljes adatkészlet befogadásához legalább 2,4 M-es rt-tárolóra van szükség.  

 

#### <a name="estimate-the-migration-speed"></a>Az áttelepítési sebesség becslése: 

Feltételezve, hogy az áttelepítési számítási feladatok a teljes kiosztott átviteli sebességet igénybe vehetik, a kiépített teljes folyamat során becslést ad az áttelepítési sebesség. Az előző példában folytatva 5 több egység szükséges egy 1 KB-os dokumentum Azure Cosmos DB SQL API-fiókba való írásához.  A 2,4 millió több mint egy e-kód 480 000 dokumentum /másodperc (vagy 480 MB/s) átvitelét tenné lehetővé. Ez azt jelenti, hogy a teljes migráció 60 TB lesz 125.000 másodperc, vagy körülbelül 34 óra.  

Abban az esetben, ha azt szeretné, hogy az áttelepítés egy napon belül befejeződjön, a kiosztott átviteli szintet 5 millió Több rektorra kell növelnie. 

 

#### <a name="turn-off-the-indexing"></a>Az indexelés kikapcsolása:  

Mivel az áttelepítést a lehető leghamarabb be kell fejezni, célszerű minimalizálni az egyes bevitt dokumentumok indexeinek létrehozására fordított időt és a Felelős szervezeteket.  Az Azure Cosmos DB automatikusan indexeli az összes tulajdonságot, érdemes minimalizálni a kiválasztott néhány kifejezésre történő indexelést, vagy teljesen kikapcsolni az áttelepítés során. A tároló indexelési házirendjének kikapcsolásával az indexelési mód nincs, ahogy az alábbiakban látható:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Az áttelepítés befejezése után frissítheti az indexelést.  

## <a name="migration-process"></a>Migrálási folyamat 

Az előfeltételek befejezése után az alábbi lépésekkel telepítheti át az adatokat:  

1. Először importálja az adatokat a forrásból az Azure Blob Storage-ba. Az áttelepítés sebességének növelése érdekében hasznos párhuzamosítani a különböző forráspartíciók között. Az áttelepítés megkezdése előtt a forrásadatkészletet 200 MB-nál nagyobb méretű fájlokra kell particionálni.   

2. A tömeges végrehajtó könyvtár felskálázható, 500 000 több felhasználói egység egyetlen ügyfél virtuális gépben. Mivel a rendelkezésre álló átviteli 5 millió virtuális és összefüggő hely, 10 Ubuntu 16.04 virtuális gépek (Standard_D32_v3) kell kiépíteni ugyanabban a régióban, ahol az Azure Cosmos-adatbázis található. Elő kell készítenie ezeket a virtuális gépeket az áttelepítési eszközzel és annak beállításfájljával.  

3. Futtassa a várólista-lépést az ügyfél egyik virtuális gépén. Ez a lépés létrehozza a nyomon követési gyűjteményt, amely megvizsgálja az ADLS-tárolót, és létrehoz egy folyamatkövetési dokumentumot a forrásadatkészlet partíciófájljaihoz.  

4. Ezután futtassa az importálási lépést az összes ügyfél virtuális számítógépen. Az ügyfelek mindegyike átveheti a forráspartíció tulajdonjogát, és az adatok at az Azure Cosmos DB-be. Miután befejeződött, és az állapota frissült a nyomkövetési gyűjteményben, az ügyfelek lekérdezhetik a következő elérhető forráspartíciót a nyomkövetési gyűjteményben.  

5. Ez a folyamat addig folytatódik, amíg a forráspartíciók teljes készletét be nem kell dolgozni. Miután az összes forráspartíciófeldolgozásra került, az eszközt újra kell futtatni a hibajavítási módban ugyanazon a követési gyűjteményen. Ez a lépés szükséges a hibák miatt újra feldolgozandó forráspartíciók azonosításához.  

6. A hibák némelyike a forrásadatokban lévő helytelen dokumentumokmiatt történhet. Ezeket azonosítani és rögzíteni kell. Ezután futtassa újra az importálási lépést a sikertelen partíciókon, hogy újrabevethesse őket. 

Miután az áttelepítés befejeződött, ellenőrizheti, hogy a dokumentum száma az Azure Cosmos DB-ben megegyezik-e a forrásadatbázisban lévő dokumentumszámmal. Ebben a példában az Azure Cosmos DB teljes mérete 65 terabájtból lett kimutatva. Az áttelepítés után az indexelés szelektíven bekapcsolható, és a rendszerműködtető konkre csökkenthető a számítási feladatok műveletei által megkövetelt szintre.

## <a name="contact-the-azure-cosmos-db-team"></a>Kapcsolatfelvétel az Azure Cosmos DB csapatával
Bár az útmutató támpontként követheti a nagy adatkészletek sikeres áttelepítéséhez az Azure Cosmos DB-be, a nagy méretű áttelepítések esetén ajánlott elérni az Azure Cosmos DB termékcsapatát az adatmodellezés és az általános architektúra-felülvizsgálat érvényesítéséhez. Az adatkészlet és a munkaterhelés alapján a termékcsapat más teljesítmény- és költségoptimalizálást is javasolhat, amely önre alkalmazható. Ha az Azure Cosmos DB csapatával szeretne segítséget kérni a nagyméretű áttelepítések esetén, megnyithat egy támogatási jegyet az "Általános tanácsadó" probléma típusa és a "Nagy (TB+) áttelepítések" probléma altípusa alatt az alábbiak szerint.

![Áttelepítést támogató témakör](./media/migrate-cosmosdb-data/supporttopic.png)


## <a name="next-steps"></a>További lépések

* További információ: a [.NET](bulk-executor-dot-net.md) és Java tömeges végrehajtó könyvtárát használó mintaalkalmazások [kipróbálásával.](bulk-executor-java.md) 
* A tömeges végrehajtó könyvtár integrálva van a Cosmos DB Spark-összekötő, további információkért tekintse meg [az Azure Cosmos DB Spark-összekötő](spark-connector.md) cikket.  
* Lépjen kapcsolatba az Azure Cosmos DB termékcsapatával egy támogatási jegy megnyitásával az "Általános tanácsadó" problématípusa és a "Nagy (TB+) áttelepítések" probléma altípusa alatt további segítséget a nagyméretű áttelepítések esetén. 

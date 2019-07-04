---
title: A grafikon adatainak modellezése az Azure Cosmos DB Gremlin API-hoz
description: Ismerje meg, hogyan modell egy gráfadatbázist Cosmos DB Gremlin API használatával.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/24/2019
ms.author: lbosq
ms.openlocfilehash: c6ae23efa90874bbefc2aff35f8798aa6c0da791
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503419"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>A grafikon adatainak modellezése az Azure Cosmos DB Gremlin API-hoz

A következő dokumentum célja graph adatmodellezési javaslatokat nyújt. Ez a lépés annak érdekében, hogy a méretezhetőség és a egy graph-adatbázis rendszer teljesítményének biztosítása érdekében, az adatok haladásával létfontosságú. Egy hatékony adatmodell különösen fontos a nagy méretű gráfok.

## <a name="requirements"></a>Követelmények

Ebben az útmutatóban leírtak szerint a következő feltételek alapján:
 * A **entitások** azonosítja a probléma területen. Ezek az entitások úgy van kialakítva, hogy a felhasznált _szolgáltatásfrissítést_ az egyes kérések. Más szóval az adatbázis-rendszer nem célja, hogy több lekérdezési kérelmek az egyetlen entitás adatainak beolvasása.
 * Van egy-egy **olvasási és írási követelmények** az adatbázis rendszerhez. Ezek a követelmények végigvezeti a gráfos adatmodellnek szükséges optimalizálásokat.
 * A vizualizációtervezés a [Apache Tinkerpop tulajdonság graph standard](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) megértsük.

## <a name="when-do-i-need-a-graph-database"></a>Ha a gráfadatbázis kell?

Graph adatbázis-megoldás optimális alkalmazható, ha az entitások és kapcsolatok az adatok tartományhoz az alábbi jellemzők valamelyike: 

* Az entitások **magas csatlakoztatott** leíró kapcsolatok révén. Ebben a forgatókönyvben előnye a tény, hogy a kapcsolatok megmaradnak a storage-ban.
* Nincsenek **körkörös kapcsolatokat** vagy **önálló hivatkozott entitások**. Ezt a mintát gyakran relációs használatával vagy a dokumentum-adatbázisok kihívást jelent.
* Nincsenek **dinamikusan fejlődnek kapcsolatok** entitások között. Ez a minta esetében különösen sok szint hierarchikus vagy fa strukturált adatok alkalmazható.
* Nincsenek **több-a-többhöz kapcsolatok** entitások között.
* Nincsenek **írható és olvasható az entitások és a kapcsolatok követelmények**. 

Ha a fenti feltételek teljesülnek, akkor valószínű, hogy a graph-adatbázis megközelítést biztosít előnyöket **összetettséget lekérdezése**, **adatmodell méretezhetőség**, és **lekérdezési teljesítmény**.

A következő lépés az határozza meg, ha a grafikonon fogja elemzési vagy tranzakciós célokra használhatók. A gráf célja nagy számítási és adatfeldolgozási számítási feladatokhoz használható, ha lenne érdemes böngészhet a [Cosmos DB Spark-összekötő](https://docs.microsoft.com/azure/cosmos-db/spark-connector) és használatát a [GraphX-könyvtár](https://spark.apache.org/graphx/). 

## <a name="how-to-use-graph-objects"></a>Graph-objektumok használata

A [Apache Tinkerpop tulajdonság graph standard](http://tinkerpop.apache.org/docs/current/reference/#graph-computing) határozza meg a két típusú objektumok **csúcspontok** és **élek**. 

A tulajdonságokat a graph-objektumok az ajánlott eljárások a következők:

| Object | Tulajdonság | Típus | Megjegyzések |
| --- | --- | --- |  --- |
| Csúcspont | id | String | Partíciónként egyedileg lép érvénybe. Ha egy érték nem a szolgáltatás megadott, és automatikusan létrehozott GUID Azonosítóját tárolja. |
| Csúcspont | label | String | Ez a tulajdonság a csúcspont képviselő entitás típusának azonosítására szolgál. Ha az értéke nincs megadva, alapértelmezett érték "csomópont" használandó. |
| Csúcspont | properties | Karakterlánc, logikai érték beolvasása, numerikus | Minden csúcspont a kulcs-érték párokat tárol külön tulajdonságok listája. |
| Csúcspont | Partíciókulcs | Karakterlánc, logikai érték beolvasása, numerikus | Ez a tulajdonság határozza meg, a csúcspont és a kimenő élek a rendszer hol tárolja. Tudjon meg többet [graph-particionálás](graph-partitioning.md). |
| Edge | id | String | Partíciónként egyedileg lép érvénybe. Automatikusan létrehozott alapértelmezés szerint. Élek általában nem rendelkezik azonosító egyedi módon kérhető szükséges |
| Edge | label | String | Ez a tulajdonság két csúcspontok rendelkező kapcsolat típusának azonosítására szolgál. |
| Edge | properties | Karakterlánc, logikai érték beolvasása, numerikus | Az egyes edge kulcs-érték párokat tárol külön tulajdonságok listája. |

> [!NOTE]
> Élek nem igényel partíciókulcs-értékkel, mivel az értékét a rendszer automatikusan hozzárendel a forráscsúcspont alapján. További információ: a [graph-particionálás](graph-partitioning.md) cikk.

## <a name="entity-and-relationship-modeling-guidelines"></a>Entitás- és modellezés irányelvek kapcsolat

Az alábbiakban a megközelítés az egy Azure Cosmos DB Gremlin API gráfadatbázist az adatmodellezés irányelveket készletét. Ezeket az irányelveket, az azt feltételezik, hogy nincs-e egy meglévő definícióját egy adatok tartomány és a hozzá tartozó lekérdezések.

> [!NOTE]
> Az alábbi lépéseket követve jelennek meg javaslatok. A kész modell kiértékeli, és a figyelmet, éles használatra kész előtt kell. Emellett az alábbi javaslatokat kapcsolódnak az Azure Cosmos DB Gremlin API-implementáció. 

### <a name="modeling-vertices-and-properties"></a>Modellezési csúcsokat és tulajdonságok 

Az első lépés a gráfos adatmodellnek, hogy az összes azonosított entitások leképezése egy **csúcspont objektum**. Minden entitás a csúcspontok egy-egy térképét egy kezdeti lépés és a tulajdonos módosítása kell lennie.

Egy közös adatelemekkel képezi le egyetlen entitást, külön csúcsok tulajdonságait. Vegye figyelembe, ha két különböző módon jelölt ugyanahhoz az entitáshoz, az alábbi példában:

* **A vertex-alapú tulajdonságok**: Ebben a megközelítésben az entitás segítségével három különálló csúcsok és élek két tulajdonságainak leírása. Amíg ez a megközelítés előfordulhat, hogy a redundancia csökkentése érdekében növeli a modell összetettségét. Modell összetettséget növekedése hozzáadott késést, a lekérdezés összetettségétől és a számítási költségeket eredményezhet. Ez a modell is kihívást a particionálást.

![Entitás modell tulajdonságai csúcsot.](./media/graph-modeling/graph-modeling-1.png)

* **Vlastnost beágyazott csúcspontok**: Ez a megközelítés kihasználja a kulcs-érték pár lista, amely az entitás egy csúcspont belüli összes tulajdonság jelöli. Ez a megközelítés biztosít csökkentett modell összetettségét, amely egyszerűbb lekérdezések és több költséghatékony bejárásokat vezet.

![Entitás modell tulajdonságai csúcsot.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> A fenti példák bemutatják egy egyszerűsített graph modell csak az entitás tulajdonságai felosztása két módon lehet összehasonlítása jeleníti meg.

A **tulajdonság beágyazott csúcspontok** mintát általában tartalmaz egy további nagy teljesítményű és skálázható megközelítést. Az alapértelmezett módszer egy új gráf adatmodellhez kell gravitate Ez a minta felé.

Vannak azonban forgatókönyvek, ahol tulajdonságra hivatkozó előfordulhat, hogy az előnyöket biztosítják. Példa: Ha a hivatkozott tulajdonság rendszeresen frissül. Egy külön csúcspont használatával olyan tulajdonságot, amely folyamatosan változik képviselő lenne a lehető legkevesebb az írási műveletek, amelyek a frissítés esetén.

### <a name="relationship-modeling-with-edge-directions"></a>Modellezés az edge irányban kapcsolat

A csúcspontok van modellezve, miután az élek jelölésére közöttük a kapcsolatokat is hozzáadhatók. Az első szempont, amelyet ki kell értékelni a **a kapcsolat irányát**. 

Edge-objektumok rendelkeznek egy alapértelmezett iránya, amely egy bejárási követ használatakor a `out()` vagy `outE()` függvény. Használatával a természetes iránya eredményezi egy hatékony művelet, mivel minden csúcspontok a kimenő élek együtt vannak tárolva. 

Azonban az ellenkező irányba, az edge áthaladó, használja a `in()` működik, minden esetben eredményez olyan partícióra kiterjedő lekérdezések. Tudjon meg többet [graph-particionálás](graph-partitioning.md). Ha folyamatosan gyermekelemeinek használatával szükség van a `in()` funkciót, azt javasoljuk, hogy mindkét irányban élek hozzáadása.

Meghatározhatja az edge iránya használatával a `.to()` vagy `.from()` való predikátumokat a `.addE()` Gremlin. lépés. Vagy a [BulkExecutor kódtára a Gremlin API](bulk-executor-graph-dotnet.md).

> [!NOTE]
> Edge-objektumokat egy irányban alapértelmezés szerint rendelkezik.

### <a name="relationship-labeling"></a>Kapcsolat címkézés

A kapcsolat leíró címkék használatával javíthatja a peremhálózati megoldás műveletek hatékonyságát. Ez a minta a következő módokon lehet alkalmazni:
* Nem általános feltételek használatával kapcsolat címkézését.
* A célcsúcspont címkéjére a forráscsúcspont címkéjének társítása a kapcsolat nevét.

![A kapcsolat címkézés példákat.](./media/graph-modeling/graph-modeling-3.png)

A pontosabb, amelynek használatával a traverser minél jobban szűrése az élek a címkét. Ezt a döntést jelentős hatással lehet a lekérdezés költség is. A lekérdezés költség kiértékelheti bármikor [executionProfile lépés használata](graph-execution-profile.md).


## <a name="next-steps"></a>Következő lépések: 
* Tekintse meg a listában, a támogatott [gremlinnel kapcsolatos lépések](gremlin-support.md).
* Ismerje meg [graph-adatbázis-particionálást](graph-partitioning.md) nagyméretű gráfok kezelésére.
* A Gremlin-lekérdezések segítségével kiértékelheti a [végrehajtási profil lépés](graph-execution-profile.md).

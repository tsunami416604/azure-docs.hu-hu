---
title: Graph-adatmodellezés az Azure Cosmos DB Gremlin API-hoz
description: Ismerje meg, hogyan modellezze a gráfadatbázist az Azure Cosmos DB Gremlin API használatával. Ez a cikk azt ismerteti, hogy mikor kell diagram-adatbázist és ajánlott eljárásokat használni az entitások és kapcsolatok modellezéséhez.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: lbosq
ms.openlocfilehash: dc9a5616aa2bb1f7e09045b9cfe4f4d7e9c69be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898329"
---
# <a name="graph-data-modeling-for-azure-cosmos-db-gremlin-api"></a>Graph-adatmodellezés az Azure Cosmos DB Gremlin API-hoz

A következő dokumentum célja, hogy a gráf adatmodellezési javaslatokat. Ez a lépés létfontosságú a gráfadatbázis-rendszer méretezhetőségének és teljesítményének biztosításához az adatok fejlődésével. A hatékony adatmodell különösen fontos a nagyméretű grafikonok.

## <a name="requirements"></a>Követelmények

Az ebben az útmutatóban ismertetett folyamat a következő feltételezéseken alapul:
 * A problématérben lévő **entitások** azonosításra kerülnek. Ezeket az entitásokat minden kéréshez _atomilag_ kell felhasználni. Más szóval az adatbázis-rendszer nem úgy tervezték, hogy egyetlen entitás adatait több lekérdezési kérelmek.
 * Az adatbázis-rendszer **írási és írási követelményeit** ismerik. Ezek a követelmények irányítják a gráf adatmodelljéhez szükséges optimalizálásokat.
 * Az Apache [Tinkerpop tulajdonsággráf szabvány](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) alapelvei jól ismertek.

## <a name="when-do-i-need-a-graph-database"></a>Mikor ra van szükségem grafikon-adatbázisra?

A gráf-adatbázis-megoldás akkor alkalmazható optimálisan, ha az adattartomány entitásai és kapcsolatai az alábbi jellemzők bármelyikével rendelkeznek: 

* Az entitások **nagymértékben kapcsolódnak** leíró kapcsolatokon keresztül. Ebben a forgatókönyvben az a tény, hogy a kapcsolatok a tárolóban megmaradnak.
* Vannak **ciklikus kapcsolatok** vagy **önhivatkozott entitások**. Ez a minta gyakran kihívást jelent relációs vagy dokumentum-adatbázisok használataesetén.
* Az **entitások** között dinamikusan változó kapcsolatok vannak. Ez a minta különösen alkalmazható hierarchikus vagy fa szerkezetű adatok több szinten.
* Az **entitások között több-a-többhöz kapcsolatok** vannak.
* Írási és olvasási követelmények vannak **mind az entitásokra, mind a kapcsolatokra.** 

Ha a fenti feltételek teljesülnek, valószínű, hogy a gráfadatbázis-megközelítés előnyökkel jár a **lekérdezés ekként való összetettsége,** **az adatmodell méretezhetősége**és **a lekérdezési teljesítmény számára.**

A következő lépés annak meghatározása, hogy a grafikon lesz használható analitikus vagy tranzakciós célokra. Ha a grafikon nehéz számítási és adatfeldolgozási számítási feladatokhoz használható, érdemes felfedezni a [Cosmos DB Spark-összekötőt](https://docs.microsoft.com/azure/cosmos-db/spark-connector) és a [GraphX-könyvtár](https://spark.apache.org/graphx/)használatát. 

## <a name="how-to-use-graph-objects"></a>A diagramobjektumok használata

Az [Apache Tinkerpop tulajdonsággráf szabvány](https://tinkerpop.apache.org/docs/current/reference/#graph-computing) kétféle objektumvertert és **Vertices** **éleket**határoz meg. 

A diagramobjektumok tulajdonságaira vonatkozó gyakorlati tanácsok a következők:

| Objektum | Tulajdonság | Típus | Megjegyzések |
| --- | --- | --- |  --- |
| Vertex | ID (Azonosító) | Sztring | Partíciónként egyedileg kényszerítve. Ha a beszúráskor nem ad meg értéket, a függvény automatikusan generált GUID-t tárol. |
| Vertex | label | Sztring | Ez a tulajdonság a csúcspont által képviselt entitás típusának meghatározására szolgál. Ha nincs megadva érték, a rendszer alapértelmezett "csúcspontot" használ. |
| Vertex | properties | String, Logikai, Numerikus | Az egyes csúcspontokban kulcsérték-párokként tárolt különálló tulajdonságok listája. |
| Vertex | partíciókulcs | String, Logikai, Numerikus | Ez a tulajdonság határozza meg, hogy a csúcspont és a kimenő élek hol tárolódnak. További információ a [gráfparticionálásról.](graph-partitioning.md) |
| Edge | ID (Azonosító) | Sztring | Partíciónként egyedileg kényszerítve. Alapértelmezés szerint automatikusan generálva. Élek általában nem kell egyedileg beolvasni egy azonosítót. |
| Edge | label | Sztring | Ezzel a tulajdonsággal határozható meg, hogy két csúcsa milyen típusú kapcsolattal rendelkezik. |
| Edge | properties | String, Logikai, Numerikus | Az egyes szegélyeken kulcsérték-párokként tárolt különálló tulajdonságok listája. |

> [!NOTE]
> Az élek nem igényelnek partíciókulcs-értéket, mivel az értéke automatikusan a forráscsúcsuk alapján lesz hozzárendelve. További információ a [graph particionálási](graph-partitioning.md) cikkben.

## <a name="entity-and-relationship-modeling-guidelines"></a>Entitás- és kapcsolatmodellezési irányelvek

Az alábbiakban egy Azure Cosmos DB Gremlin API graph-adatbázis adatmodellezésének megközelítésére vonatkozó irányelvek et követünk. Ezek az irányelvek feltételezik, hogy létezik egy adattartomány és lekérdezések meglévő definíciója.

> [!NOTE]
> Az alábbiakban ismertetett lépések ajánlásként jelennek meg. A végleges modellt ki kell értékelni és tesztelni kell, mielőtt termelésre késznek tekintenék. Emellett az alábbi javaslatok az Azure Cosmos DB Gremlin API-implementációjára vonatkoznak. 

### <a name="modeling-vertices-and-properties"></a>Csúcsok és tulajdonságok modellezése 

A gráfadat-modell első lépése az, hogy minden azonosított entitást **egy csúcspontobjektumhoz**képez le. Az összes entitás vertikátleképezése kezdeti lépésnek és módosításnak kell lennie.

Az egyik gyakori buktatot az, hogy egyetlen entitás tulajdonságait külön csúcsokként kell leképezni. Vegyük az alábbi példát, ahol ugyanaz az entitás két különböző módon jelenik meg:

* **Csúcspont-alapú tulajdonságok**: Ebben a megközelítésben az entitás három különálló csúcsot és két élet használ a tulajdonságainak leírására. Bár ez a megközelítés csökkentheti a redundanciát, növeli a modell összetettségét. A modell összetettségének növekedése további késést, lekérdezési összetettséget és számítási költséget eredményezhet. Ez a modell is kihívást jelenthet a particionálás.

![Entitásmodell csúcsokkal a tulajdonságokhoz.](./media/graph-modeling/graph-modeling-1.png)

* **Tulajdonságba ágyazott csúcspontok:** Ez a megközelítés kihasználja a kulcs-érték pár lista, hogy képviselje az összes tulajdonságait az entitás egy csúcspont belül. Ez a megközelítés a modell összetettségének csökkenését biztosítja, ami egyszerűbb lekérdezésekhez és költséghatékonyabb bejárásokhoz vezet.

![Entitásmodell csúcsokkal a tulajdonságokhoz.](./media/graph-modeling/graph-modeling-2.png)

> [!NOTE]
> A fenti példák egy egyszerűsített grafikonmodellt mutatnak be, amely csak az entitástulajdonságok felosztásának két módja közötti összehasonlítást mutatja.

A **tulajdonságba ágyazott csúcsok** mintázata általában jobban teljesíthető és méretezhetőbb megközelítést biztosít. Az új gráfadat-modell alapértelmezett megközelítésének erre a mintára kell irányulnia.

Vannak azonban olyan esetek, ahol egy tulajdonságra való hivatkozás előnyökkel járhat. Például: ha a hivatkozott tulajdonságot gyakran frissítik. Ha egy folyamatosan módosított tulajdonságot külön csúcspont használatával a frissítéshez szükséges írási műveletek száma minimálisra csökkenne.

### <a name="relationship-modeling-with-edge-directions"></a>Kapcsolatmodellezés élirányúval

A csúcsok modellezése után a szegélyek hozzáadhatók a köztük lévő kapcsolatok jelöléséhez. Az első szempont, amelyet értékelni kell, **a kapcsolat iránya**. 

A szegélyobjektumok alapértelmezett iránya a `out()` `outE()` vagy a függvény használatakor egy átjárás követi. Ennek a természetes iránynak a használata hatékony működést eredményez, mivel az összes csúcsot a kimenő élekkel tárolják. 

Azonban a `in()` kör ellentétes felében a függvény használatával történő áthaladás mindig partícióközi lekérdezést eredményez. További információ a [gráfparticionálásról.](graph-partitioning.md) Ha szükség van arra, hogy folyamatosan `in()` áthaladjon a funkció használatával, javasoljuk, hogy mindkét irányban adja hozzá a széleket.

A gremlin lépés hez `.to()` `.from()` vezető vagy predikátumok segítségével határozhatja meg a `.addE()` peremvonal irányát. Vagy a [Gremlin API tömeges végrehajtó könyvtárának](bulk-executor-graph-dotnet.md)használatával.

> [!NOTE]
> A szegélyobjektumok nak alapértelmezés szerint van irányuk.

### <a name="relationship-labeling"></a>Kapcsolat címkézése

A leíró kapcsolatfeliratok használata javíthatja a peremhálózati felbontási műveletek hatékonyságát. Ez a minta a következő módokon alkalmazható:
* Kapcsolat címkézéséhez használjon nem általános kifejezéseket.
* Társítsa a forráscsúcs címkéjét a célcsúcs címkéjéhez a kapcsolat nevével.

![Példák kapcsolatcímkézési példák.](./media/graph-modeling/graph-modeling-3.png)

Minél pontosabb a címke, amelyet a traverser a szélek szűrésére használ, annál jobb. Ez a döntés jelentős hatással lehet a lekérdezési költségre is. A lekérdezés költségét bármikor kiértékelheti [a executionProfile lépés sel.](graph-execution-profile.md)


## <a name="next-steps"></a>Következő lépések: 
* Nézze meg a listát a támogatott [Gremlin lépéseket.](gremlin-support.md)
* Ismerje meg a [gráf adatbázis particionálás](graph-partitioning.md) kezelésére nagyszabású grafikonok.
* Értékelje ki a Gremlin-lekérdezéseket a [Végrehajtási profil lépés sel.](graph-execution-profile.md)

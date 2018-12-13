---
title: Az Azure Cosmos DB Gremlin-támogatás
description: Tudnivalók az Apache TinkerPop Gremlin nyelvéről. Megismerheti, milyen funkciók és lépések érhetők el az Azure Cosmos DB-ben.
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: overview
ms.date: 01/02/2018
ms.author: lbosq
ms.openlocfilehash: 53ca762232db5e79acdacbb3d52ce05f88dc108b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080401"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>A Gremlin-gráfok Azure Cosmos DB általi támogatása
Az Azure Cosmos DB támogatja az [Apache TinkerPop](https://tinkerpop.apache.org) gráfbejárási nyelvét, a [Gremlint](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), amely egy Gremlin API a gráfentitások létrehozásához és gráflekérdezési műveletek végrehajtásához. A Gremlin nyelv segítségével létrehozhat gráfentitásokat (csúcspontokat és éleket), módosíthatja ezen entitások tulajdonságait, végrehajthat lekérdezéseket és bejárásokat, és törölhet entitásokat. 

Az Azure Cosmos DB nagyvállalati szintű funkciókat biztosít a gráfadatbázisokhoz. Ezek közé tartozik a globális terjesztési, a tárolási és átviteli megoldások független méretezése, az előre jelezhető, egyjegyű ezredmásodperces késések, az automatikus indexelés, az SLA-k, és az olvasási rendelkezésre állás a két vagy több Azure-régióra kiterjedő adatbázisfiókokhoz. Mivel az Azure Cosmos DB támogatja a TinkerPop/Gremlin nyelvet, egyszerűen, a kód módosításának szükségessége nélkül migrálhatók a más gráfadatbázisokkal írt alkalmazások. Emellett a Gremlin-támogatásnak köszönhetően az Azure Cosmos DB zökkenőmentesen integrálható a TinkerPop-kompatibilis elemzési keretrendszerekkel, például az [Apache Spark GraphX](https://spark.apache.org/graphx/)-szel. 

Ez a cikk egy rövid útmutatót nyújt a Gremlin használatához, valamint bemutatja a Gremlin azon funkcióit és lépéseit, amelyeket a Gremlin API támogat.

## <a name="gremlin-by-example"></a>Példa a Gremlin használatára
Használjunk egy mintagráfot annak megismeréséhez, hogyan adhatók meg lekérdezések a Gremlinben. Az alábbi ábrán egy üzleti alkalmazás látható, amely felhasználókkal, érdeklődési körökkel és eszközökkel kapcsolatos adatokat kezel egy gráf formájában.  

![Személyeket, eszközöket és érdeklődési köröket tartalmazó mintaadatbázis](./media/gremlin-support/sample-graph.png) 

A gráfban a következő csúcsponttípusok találhatók (ezeket a Gremlinben „címkéknek” nevezzük):

- Személyek: a gráfban három személy található: Robin, Thomas és Ben.
- Érdeklődési körök: a személyek érdeklődési köre, amely ebben a példában a labdarúgás.
- Eszközök: a személyek által használt eszközök.
- Operációs rendszerek: az eszközökön futó operációs rendszerek.

Ezen entitások kapcsolatait a következő éltípusok/címkék jelzik:

- Knows (Ismeri): Például „Thomas ismeri Robint”.
- Interested (Érdekli): A gráfban szereplő személyek érdeklődési körét jelzi, például „Bent érdekli a labdarúgás”.
- RunsOS (Futó operációs rendszer): A laptopon Windows operációs rendszer fut.
- Uses (Használ): A személyek által használt eszközt képviseli. Robin például egy Motorola-telefont használ, amelynek sorozatszáma 77.

Most futtassunk néhány műveletet a gráfra vonatkozóan a [Gremlin Console](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) segítségével. Ezek a műveletek tetszőleges platformon is (Java, Node.js, Python vagy .NET) végrehajthatók a Gremlin-illesztők használatával.  Mielőtt áttekintenénk, hogy az Azure Cosmos DB mit támogat, lássunk néhány példát, amelyek segítségével megismerkedhetünk a szintaxissal.

Először is lássuk a CRUD-feladatokat. A következő Gremlin-utasítás beszúrja a „Thomas” csúcspontot a gráfba:

```
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

A következő Gremlin-utasítás beszúr egy „knows” (ismeri) élt Thomas és Robin közé.

```
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Az alábbi lekérdezés visszaadja a „személy” csúcspontokat, az utóneveik fordított ABC-sorrendjében:
```
:> g.V().hasLabel('person').order().by('firstName', decr)
```

A gráfok különösen akkor hasznosak, ha olyan kérdéseket kell megválaszolnunk, mint például: „Milyen operációs rendszereket használnak Thomas barátai?”. Ez az információ a következő egyszerű Gremlin-bejárás futtatásával nyerhető ki a gráfból:

```
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Most pedig tekintsük át, mit kínál az Azure Cosmos DB a Gremlin-fejlesztők számára.

## <a name="gremlin-features"></a>Gremlin-funkciók
A TinkerPop egy olyan szabvány, amely számos különböző gráftechnológiára kiterjed. Ebből adódóan szabványos kifejezésekkel írja le, hogy az egyes gráfszolgáltatók milyen funkciókat nyújtanak. Az Azure Cosmos DB egy állandó, magas egyidejűségű, írható gráfadatbázis, amely egyszerre több kiszolgálóra vagy fürtre is particionálható. 

Az alábbi táblázat a TinkerPop azon funkcióit sorolja fel, amelyeket az Azure Cosmos DB megvalósít: 

| Kategória | Azure Cosmos DB-megvalósítás |  Megjegyzések | 
| --- | --- | --- |
| Gráffunkciók | Állandóságot és egyidejű hozzáférést biztosít. A tranzakciók támogatására lett tervezve. | A számítógépes módszerek a Spark-összekötőn keresztül valósíthatók meg. |
| Változófunkciók | A logikai, egész számú, bájt-, dupla, lebegőpontos, hosszú és sztringértékeket támogatja. | Támogatja az egyszerű típusokat, és kompatibilis az összetett típusokkal adatmodellek révén. |
| Csúcspontfunkciók | A következőket támogatja: RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty.  | Lehetővé teszi csúcspontok létrehozását, módosítását és törlését. |
| Csúcsponttulajdonság-funkciók | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Lehetővé teszi csúcsponttulajdonságok létrehozását, módosítását és törlését. |
| Élfunkciók | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Lehetővé teszi élek létrehozását, módosítását és törlését. |
| Éltulajdonság-funkciók | Tulajdonságok, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Lehetővé teszi éltulajdonságok létrehozását, módosítását és törlését. |

## <a name="gremlin-wire-format-graphson"></a>A Gremlin átviteli formátuma: GraphSON

Az Azure Cosmos DB a [GraphSON formátum](https://github.com/thinkaurelius/faunus/wiki/GraphSON-Format) használatával adja vissza a Gremlin-műveletek eredményeit. A GraphSON a Gremlin szabványos formátuma a csúcspontok, élek és tulajdonságok (egy- és többértékű tulajdonságok) jelöléséhez a JSON használatával. 

Az alábbi kódrészletben például az Azure Cosmos DB-ből *az ügyfél számára visszaadott* csúcspont GraphSON-jelölése látható. 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

A GraphSON az alábbi tulajdonságokat használja a csúcspontokhoz:

| Tulajdonság | Leírás |
| --- | --- |
| id | A csúcspont azonosítója. Egyedinek kell lennie (adott esetben a _partition értékével kombinálva). |
| label | A csúcspont címkéje. Nem kötelező megadni, és az entitás típusának leírására használható. |
| type | A használatával megkülönböztethetők a csúcspontok a nem gráfdokumentumoktól. |
| properties | A csúcsponthoz tartozó, felhasználó által megadott tulajdonságok összessége. Minden tulajdonságnak több értéke is lehet. |
| _partition (konfigurálható) | A csúcspont partíciókulcsa. Használatával a gráfok horizontálisan felskálázhatók több kiszolgálóra. |
| outE | Egy csúcspont külső éleinek listáját tartalmazza. A csúcspontok szomszédsági adatainak tárolása lehetővé teszi a bejárások gyors végrehajtását. Az élek a címkéik alapján vannak csoportosítva. |

Az él pedig a következő információkat tartalmazza, ezzel segítve a gráf többi részéhez való navigációt.

| Tulajdonság | Leírás |
| --- | --- |
| id | Az él azonosítója. Egyedinek kell lennie (adott esetben a _partition értékével kombinálva). |
| label | Az él címkéje. Ezt a tulajdonságot nem kötelező megadni, és a kapcsolat típusának leírására használható. |
| inV | Egy él belső csúcspontjainak listáját tartalmazza. Az élek szomszédsági adatainak tárolása lehetővé teszi a bejárások gyors végrehajtását. A csúcspontok a címkéik alapján vannak csoportosítva. |
| properties | Az élhez tartozó, felhasználó által megadott tulajdonságok összessége. Minden tulajdonságnak több értéke is lehet. |

Az egyes tulajdonságok több értéket is tárolhatnak egy tömbben. 

| Tulajdonság | Leírás |
| --- | --- |
| érték | A tulajdonság értéke.

## <a name="gremlin-steps"></a>Gremlin-lépések
Most pedig tekintsük át az Azure Cosmos DB által támogatott Gremlin-lépéseket. A Gremlin teljes körű ismertetését a [TinkerPop referenciaanyaga](https://tinkerpop.apache.org/docs/current/reference) tartalmazza.

| lépés | Leírás | TinkerPop 3.2-dokumentáció |
| --- | --- | --- |
| `addE` | Hozzáad egy élt két csúcspont között. | [addE lépés](https://tinkerpop.apache.org/docs/current/reference/#addedge-step) |
| `addV` | Hozzáad egy csúcspontot a gráfhoz. | [addV lépés](https://tinkerpop.apache.org/docs/current/reference/#addvertex-step) |
| `and` | Biztosítja, hogy minden bejárás visszaadjon egy értéket. | [and lépés](https://tinkerpop.apache.org/docs/current/reference/#and-step) |
| `as` | Egy lépésmodulátor, amely egy változót rendel a lépés kimenetéhez. | [as lépés](https://tinkerpop.apache.org/docs/current/reference/#as-step) |
| `by` | A `group` és az `order` lépéssel használt lépésmodulátor. | [by lépés](https://tinkerpop.apache.org/docs/current/reference/#by-step) |
| `coalesce` | Visszaadja az első olyan bejárást, amely értéket ad vissza. | [coalesce lépés](https://tinkerpop.apache.org/docs/current/reference/#coalesce-step) |
| `constant` | Egy állandó értéket ad vissza. A `coalesce` lépéssel használható.| [constant lépés](https://tinkerpop.apache.org/docs/current/reference/#constant-step) |
| `count` | Visszaadja a darabszámot a bejárásból. | [count lépés](https://tinkerpop.apache.org/docs/current/reference/#count-step) |
| `dedup` | Visszaadja az értékeket, eltávolítva az ismétlődéseket. | [dedup lépés](https://tinkerpop.apache.org/docs/current/reference/#dedup-step) |
| `drop` | Elveti az értékeket (csúcspont/él). | [drop lépés](https://tinkerpop.apache.org/docs/current/reference/#drop-step) |
| `fold` | Korlátként funkcionál, amely kiszámítja az eredmények összesítését.| [fold lépés](https://tinkerpop.apache.org/docs/current/reference/#fold-step) |
| `group` | Csoportosítja az értékeket a megadott címkék alapján.| [group lépés](https://tinkerpop.apache.org/docs/current/reference/#group-step) |
| `has` | Tulajdonságok, csúcspontok és élek szűrésére szolgál. A következő változatokat támogatja: `hasLabel`, `hasId`, `hasNot` és `has`. | [has lépés](https://tinkerpop.apache.org/docs/current/reference/#has-step) |
| `inject` | Értékeket szúr be egy streambe.| [inject lépés](https://tinkerpop.apache.org/docs/current/reference/#inject-step) |
| `is` | Szűrés végrehajtására használható egy logikai kifejezés használatával. | [is lépés](https://tinkerpop.apache.org/docs/current/reference/#is-step) |
| `limit` | A bejárásban található elemek számának korlátozására szolgál.| [limit lépés](https://tinkerpop.apache.org/docs/current/reference/#limit-step) |
| `local` | Helyileg becsomagolja egy bejárás egy szakaszát, egy segédlekérdezéshez hasonlóan. | [local lépés](https://tinkerpop.apache.org/docs/current/reference/#local-step) |
| `not` | Egy szűrő eltávolítására szolgál. | [not lépés](https://tinkerpop.apache.org/docs/current/reference/#not-step) |
| `optional` | A megadott bejárás eredményét adja vissza, ha az ad eredményt, ha nem, akkor a hívó elemet adja vissza. | [optional lépés](https://tinkerpop.apache.org/docs/current/reference/#optional-step) |
| `or` | Biztosítja, hogy legalább az egyik bejárás visszaadjon egy értéket. | [or lépés](https://tinkerpop.apache.org/docs/current/reference/#or-step) |
| `order` | A megadott rendezési sorrendben adja vissza az eredményeket. | [order lépés](https://tinkerpop.apache.org/docs/current/reference/#order-step) |
| `path` | Visszaadja a bejárás teljes útvonalát. | [path lépés](https://tinkerpop.apache.org/docs/current/reference/#path-step) |
| `project` | Leképezésként jeleníti meg a tulajdonságokat. | [project lépés](https://tinkerpop.apache.org/docs/current/reference/#project-step) |
| `properties` | Visszaadja a megadott címkék tulajdonságait. | [properties lépés](https://tinkerpop.apache.org/docs/current/reference/#properties-step) |
| `range` | A megadott értéktartományra szűr.| [range lépés](https://tinkerpop.apache.org/docs/current/reference/#range-step) |
| `repeat` | Megismétli a lépést a megadott számú alkalommal. Ismétlődések beállítására szolgál. | [repeat lépés](https://tinkerpop.apache.org/docs/current/reference/#repeat-step) |
| `sample` | Mintát vesz a bejárás eredményeiből. | [sample lépés](https://tinkerpop.apache.org/docs/current/reference/#sample-step) |
| `select` | Megjeleníti a bejárás eredményeit. |  [select lépés](https://tinkerpop.apache.org/docs/current/reference/#select-step) | |
| `store` | Nem blokkoló összesítéseket hajt végre a bejárásból. | [store lépés](https://tinkerpop.apache.org/docs/current/reference/#store-step) |
| `tree` | Egy fában összesíti a csúcspontból induló útvonalakat. | [tree lépés](https://tinkerpop.apache.org/docs/current/reference/#tree-step) |
| `unfold` | Visszaalakít egy iterátort egy lépésként.| [unfold lépés](https://tinkerpop.apache.org/docs/current/reference/#unfold-step) |
| `union` | Egyesíti több bejárás eredményeit.| [union lépés](https://tinkerpop.apache.org/docs/current/reference/#union-step) |
| `V` | A csúcspontok és élek közötti bejárásokhoz szükséges lépéseket foglalja magában: `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` és `otherV`. | [vertex lépések](https://tinkerpop.apache.org/docs/current/reference/#vertex-steps) |
| `where` | A bejárás eredményeinek szűrésére szolgál. A következő operátorokat támogatja: `eq`, `neq`, `lt`, `lte`, `gt`, `gte`, `between`.  | [where lépés](https://tinkerpop.apache.org/docs/current/reference/#where-step) |

Az Azure Cosmos DB által biztosított, írásra optimalizált motor alapértelmezés szerint támogatja a csúcspontokon és éleken belüli összes tulajdonság automatikus indexelését. Ezért a szűrővel rendelkező lekérdezéseket, a tartománylekérdezéseket, a rendezéseket és a tulajdonságösszesítések mindegyikét a rendszer közvetlenül az indexből dolgozza fel a hatékony kiszolgálás érdekében. Az indexelésnek az Azure Cosmos DB-ben való működésével kapcsolatban a [sémafüggetlen indexelésről](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) szóló tanulmányunkban tekinthet meg további információt.

## <a name="next-steps"></a>További lépések
* Bevezetés egy gráfalkalmazás létrehozásába [az SDK-k használatával](create-graph-dotnet.md) 
* További információk a [gráfok támogatásáról](graph-introduction.md) az Azure Cosmos DB-ben

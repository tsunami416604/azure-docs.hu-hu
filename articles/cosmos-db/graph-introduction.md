---
title: Bevezetés az Azure Cosmos DB Gremlin API-ba
description: Ez a cikk azt ismerteti, hogy miként használható az Azure Cosmos DB közel valós idejű adateléréssel nagy méretű gráfok tárolására, lekérdezésére és bejárására az Apache TinkerPop gráflekérdezési nyelve, a Gremlin használatával.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 07/18/2019
ms.author: lbosq
ms.openlocfilehash: 9151b54d7fa0b64a465aa8384cb4bfdb8e72c482
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75500001"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Bevezetés az Azure Cosmos DB használatába: Gremlin API

[Az Azure Cosmos DB](introduction.md) a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása a kritikus fontosságú alkalmazások számára. Ez egy többmodelles adatbázis, és támogatja a dokumentum, kulcs-érték, grafikon, és oszlop-család adatmodellek. Az Azure Cosmos DB Gremlin API-t egy teljes körűen felügyelt adatbázis-szolgáltatás graph adatainak tárolására és működtetésére használják.  

![Az Azure Cosmos DB gráfarchitektúrája](./media/graph-introduction/cosmosdb-graph-architecture.png)

A cikk áttekintést nyújt az Azure Cosmos DB Gremlin API-járól, és elmagyarázza, hogyan használhatja a nagyméretű, több milliárd csúcsot és élet tartalmazó gráfok tárolására. A grafikonok ezredmásodperces késéssel lekérdezése és a diagramszerkezet egyszerű alakítása. Az Azure Cosmos DB Gremlin API-ja az [Apache TinkerPop](https://tinkerpop.apache.org) graph adatbázis szabványon alapul, és a Gremlin lekérdezési nyelvet használja. 

Az Azure Cosmos DB Gremlin API-ja a gráfadatbázis-algoritmusok erejét a jól méretezhető, felügyelt infrastruktúrával kombinálja, hogy egyedi, rugalmas megoldást nyújtson a rugalmasság és a relációs megközelítések hiányával kapcsolatos leggyakoribb adatproblémákra. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Az Azure Cosmos DB gráfadatbázisának jellemzői
 
Az Azure Cosmos DB egy teljes körűen felügyelt gráfadatbázis, amely globális elosztási, rugalmas tárhely- és teljesítményméretezési, automatikus indexelési és lekérdezési képességeket biztosít, valamint beállítható konzisztenciaszinteket és a TinkerPop szabvány támogatását. 

Az Azure Cosmos DB Gremlin API által elérhető differenciált funkciók a következők:

* **Rugalmasan méretezhető átviteli sebesség és tárterület**

  A valós életben a gráfokat egyetlen kiszolgáló kapacitásán felül kell tudni méretezni. Az Azure Cosmos DB támogatja a horizontálisan méretezhető gráfadatbázisok, amelyek a tárolás és a kiosztott átviteli kapacitás gyakorlatilag korlátlan méretű. Ahogy a gráf adatbázis-skálája növekszik, az adatok automatikusan elosztva [gráf particionálás](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Többrégiós replikáció**

  Az Azure Cosmos DB automatikusan replikálhatja a grafikonadatokat bármely Azure-régióban világszerte. A globális replikáció leegyszerűsíti az adatokhoz való globális hozzáférést igénylő alkalmazások fejlesztését. Az Azure Cosmos DB a világ bármely pontján minimalizálja az olvasási és írási késést, és automatikus regionális feladatátvételi mechanizmust is biztosít, amely biztosítja az alkalmazás folytonosságát abban a ritka esetben, ha egy szolgáltatás megszakad egy régióban. 

* **Gyors lekérdezések és átjárások a legszélesebb körben elfogadott gráflekérdezési szabvánnyal**

  Tároljon heterogén csúcspontokat és éleket, és kérdezze le őket egy ismerős Gremlin szintaxison keresztül. Gremlin egy elengedhetetlen, funkcionális lekérdezési nyelv, amely gazdag felületet valósít meg a közös grafikon algoritmusok. 
  
  Az Azure Cosmos DB gazdag valós idejű lekérdezéseket és bejárásokat tesz lehetővé anélkül, hogy sématippeket, másodlagos indexeket vagy nézeteket kellene megadnia. További információk: [Gráfok lekérdezése a Gremlin használatával](gremlin-support.md).

* **Teljes körűen felügyelt gráf-adatbázis**

  Az Azure Cosmos DB használatával nincs szükség az adatbázis és a gép erőforrásainak kezelésére. A legtöbb meglévő gráf adatbázis-platformok vannak kötve a korlátozások az infrastruktúra és gyakran igényelnek nagyfokú karbantartást annak érdekében, hogy annak működését. 
  
  Teljes körűen felügyelt szolgáltatásként a Cosmos DB szükségtelenné teszi a virtuális gépek kezelését, a futásidejű szoftverek frissítését, a horizontális horizontális műveletek vagy a replikáció kezelését, illetve az összetett adatréteg-frissítések kezelését. Minden gráfról automatikus biztonsági mentés készül, és védelmet élveznek a regionális meghibásodásokkal szemben. Ezek a garanciák lehetővé teszik a fejlesztők számára, hogy a gráf-adatbázisok üzemeltetése és kezelése helyett az alkalmazásérték biztosítására összpontosítsanak. 

* **Automatikus indexelés**

  Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli a gráf csúcsain és élein található összes tulajdonságot, és nem vár vagy igényel semmilyen sémát, valamint nem szükséges másodlagos indexek létrehozása sem. További információ [az indexelésről az Azure Cosmos DB-ben.](https://docs.microsoft.com/azure/cosmos-db/index-overview) 

* **Kompatibilitás az Apache TinkerPoppal**

  Az Azure Cosmos DB támogatja a [nyílt forráskódú Apache TinkerPop szabványt.](https://tinkerpop.apache.org/) A Tinkerpop szabvány alkalmazások és kódtárak bőséges ökoszisztémájával rendelkezik, amelyek könnyen integrálhatók az Azure Cosmos DB Gremlin API-jával. 

* **Beállítható konzisztenciaszintek**

  Az Azure Cosmos DB öt jól definiált konzisztenciaszintet biztosít az alkalmazás konzisztenciája és teljesítménye közötti megfelelő kompromisszum eléréséhez. A lekérdezések és olvasási műveletek esetében az Azure Cosmos DB öt különböző konzisztenciaszintet kínál: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késleltetés között. További információk: [Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](consistency-levels.md).

## <a name="scenarios-that-can-use-gremlin-api"></a>A Gremlin API használatának forgatókönyvei
Íme néhány forgatókönyv, ahol az Azure Cosmos DB grafikontámogatása hasznos lehet:

* **Közösségi hálózatok/Ügyfél 365**

  Az ügyfeleivel és ügyfeleinek más személyekkel való interakcióival kapcsolatos adatok kombinálásával személyre szabott élményeket dolgozhat ki, előre jelezheti az ügyfelek viselkedését, vagy összekötheti a hasonló érdeklődési körű személyeket. Az Azure Cosmos DB használatával felügyelhetők a közösségi hálózatok, és nyomon követhetők az ügyfelek preferenciái és adatai.

* **Javaslati motorok**

  Ezt a forgatókönyvet gyakran alkalmazzák a kiskereskedelemben. A termékekkel, a felhasználókkal és a felhasználók interakcióival (például vásárlások, böngészés vagy az egyes cikkek értékelése) kapcsolatos információk kombinálásával testre szabott javaslatokat állíthat össze. Az Azure Cosmos DB alacsony késésű, rugalmas méretezési és natív grafikontámogatása ideális ezekhez a forgatókönyvekhez.

* **Térinformatikai**

  Számos távközlési, logisztikai és utazástervezési alkalmazásban van szükség az egyes helyszínek egy területen belül való megkeresésére, vagy a két helyszín közötti legrövidebb/optimális útvonal megállapítására. Az Azure Cosmos DB természetes választás az ilyen problémákhoz.

* **Eszközök internetes hálózata**

  Ha a hálózatot és az IoT-eszközök közötti kapcsolatot gráfként modellezi, jobban megértheti az eszközök és adategységek állapotát. Azt is megértheti, hogy a hálózat egyes részeinek módosítása milyen potenciális hatással lehet a többi részre.

## <a name="introduction-to-graph-databases"></a>A gráfadatbázisok bemutatása
Az adatok a való világban természetes módon kapcsolódnak egymáshoz. A hagyományos adatmodellezés az entitások külön-külön történő meghatározására és kapcsolatuk futásidőben történő kiszámítására összpontosít. Bár ez a modell megvannak az előnyei, a nagy mértékben összekapcsolt adatok kezelése a korlátai alatt kihívást jelenthet.  

A gráfadatbázis-megközelítés ehelyett a tárolási rétegben lévő kapcsolatra támaszkodik, ami rendkívül hatékony gráflekérési műveletekhez vezet. Az Azure Cosmos DB Gremlin API támogatja a [tulajdonsággráf modell.](https://tinkerpop.apache.org/docs/current/reference/#intro)

### <a name="property-graph-objects"></a>Tulajdonságdiagram-objektumok

A [tulajdonságdiagram](http://mathworld.wolfram.com/Graph.html) [csúcsokból](http://mathworld.wolfram.com/GraphVertex.html) és [élekből](http://mathworld.wolfram.com/GraphEdge.html)álló szerkezet. Mindkét objektum nak tetszőleges számú kulcs-érték párja lehet tulajdonságként. 

* **Csúcspontok** – A csúcspontok különálló entitásokat jelölnek, például egy személyt, egy helyet vagy egy eseményt.

* **Élek** – Az élek a csúcsok közötti kapcsolatokat jelölik. Például egy adott személy ismerhet egy másik személyt, részt vehet egy eseményen, vagy nemrégiben meglátogathatott egy helyszínt. 

* **Tulajdonságok** – A tulajdonságok a csúcsokkal és élekkel kapcsolatos információkat jelölik. A csúcspontokon vagy élekben tetszőleges számú tulajdonság lehet, és a lekérdezésobjektumainak leírására és szűrésére használható. Példa tulajdonságok közé tartozik a csúcspont, amelynek neve és kora, vagy egy él, amely lehet egy időbélyegző és/vagy a súly. 

A gráfadatbázisok gyakran a NoSQL vagy a nem relációs adatbázis-kategóriába tartoznak, mivel nincs függőség a sémátvagy a korlátozott adatmodelltől. A séma hiánya lehetővé teszi a csatlakoztatott struktúrák természetes és hatékony modellezését és tárolását. 

### <a name="gremlin-by-example"></a>Példa a Gremlin használatára
Használjunk egy mintagráfot annak megismeréséhez, hogyan adhatók meg lekérdezések a Gremlinben. Az alábbi ábrán egy üzleti alkalmazás látható, amely felhasználókkal, érdeklődési körökkel és eszközökkel kapcsolatos adatokat kezel egy gráf formájában.  

![Személyeket, eszközöket és érdeklődési köröket tartalmazó mintaadatbázis](./media/gremlin-support/sample-graph.png) 

Ez a grafikon a következő *csúcspont-típusokat* (úgynevezett "címke" a Gremlinben):

- **Emberek**: A grafikon három ember, Robin, Thomas, és Ben
- **Érdekek**: Érdekeik, ebben a példában a játék a labdarúgás
- **Eszközök**: Az emberek által használt eszközök
- **Operációs rendszerek**: Azok az operációs rendszerek, amelyeken az eszközök

Az entitások közötti kapcsolatokat a *edge* következő éltípusokon/címkéken keresztül képviseljük:

- **Tudja:** Például, "Thomas ismeri Robint"
- **Érdekelt**: Az emberek érdekeinek képviseletére a grafikonunkon, például "Ben érdeklődik a labdarúgás iránt"
- **RunsOS**: Laptop fut a Windows operációs rendszer
- **Felhasználás**: Annak beállítása, hogy egy személy melyik eszközt használja. Robin például egy Motorola-telefont használ, amelynek sorozatszáma 77.

Most futtassunk néhány műveletet a gráfra vonatkozóan a [Gremlin Console](https://tinkerpop.apache.org/docs/3.3.2/reference/#gremlin-console) segítségével. Ezek a műveletek tetszőleges platformon is (Java, Node.js, Python vagy .NET) végrehajthatók a Gremlin-illesztők használatával.  Mielőtt áttekintenénk, hogy az Azure Cosmos DB mit támogat, lássunk néhány példát, amelyek segítségével megismerkedhetünk a szintaxissal.

Először is lássuk a CRUD-feladatokat. A következő Gremlin-utasítás beszúrja a „Thomas” csúcspontot a gráfba:

```java
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

A következő Gremlin-utasítás beszúr egy „knows” (ismeri) élt Thomas és Robin közé.

```java
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

Az alábbi lekérdezés visszaadja a „személy” csúcspontokat, az utóneveik fordított ABC-sorrendjében:
```java
:> g.V().hasLabel('person').order().by('firstName', decr)
```

A gráfok különösen akkor hasznosak, ha olyan kérdéseket kell megválaszolnunk, mint például: „Milyen operációs rendszereket használnak Thomas barátai?”. Futtathatja ezt a Gremlin bejárást, hogy ezt az információt a grafikonról beszerezhesse:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```

## <a name="next-steps"></a>További lépések
A gráfok Azure Cosmos DB általi támogatásával kapcsolatos további információkért lásd:

* Első lépésként kezdje [az Azure Cosmos DB-gráfokkal kapcsolatos oktatóanyaggal](create-graph-dotnet.md).
* Ismerje meg [az Azure Cosmos DB-ben kezelt gráfok Gremlinnel való lekérdésének](gremlin-support.md) módját.

---
title: Bevezetés az Azure Cosmos DB Gremlin API-hoz
description: Ez a cikk azt ismerteti, hogy miként használható az Azure Cosmos DB közel valós idejű adateléréssel nagy méretű gráfok tárolására, lekérdezésére és bejárására az Apache TinkerPop gráflekérdezési nyelve, a Gremlin használatával.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 06/25/2019
ms.author: lbosq
ms.openlocfilehash: 126c825106b7844a5fc8a5a3cdbcc7aa6c273b5b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502794"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Bevezetés: az Azure Cosmos DB: Gremlin API

[Az Azure Cosmos DB](introduction.md) alapvető fontosságú alkalmazásokhoz a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Egy többmodelles adatbázis, és támogatja a dokumentum, kulcs-érték, gráf és oszlopos adatmodelleket. Az Azure Cosmos DB Gremlin API segítségével tárolhatja, és a egy teljes körűen felügyelt adatbázis-környezetre, bármilyen méretben graph adatokkal működnek.  

![Az Azure Cosmos DB gráfarchitektúrája](./media/graph-introduction/cosmosdb-graph-architecture.png)

A cikk áttekintést nyújt az Azure Cosmos DB Gremlin API-járól, és elmagyarázza, hogyan használhatja a nagyméretű, több milliárd csúcsot és élet tartalmazó gráfok tárolására. A diagramok lekérdezése ezredmásodperces késéssel, és fejlesztheti tovább könnyedén a graph-struktúrát. Az Azure Cosmos DB Gremlin API alapján a [Apache TinkerPop](https://tinkerpop.apache.org) standard gráfadatbázis, és használja a Gremlin lekérdezési nyelv. 

Az Azure Cosmos DB Gremlin API ötvözi hatékonyan méretezhető, felügyelt infrastruktúra egyedi, rugalmas megoldást kínál a rugalmasság és a relációs megközelítések társított leggyakoribb adatok problémák graph adatbázis algoritmusok hatékonyságát. 

## <a name="features-of-azure-cosmos-db-graph-database"></a>Az Azure Cosmos DB gráfadatbázisának jellemzői
 
Az Azure Cosmos DB egy teljes körűen felügyelt gráfadatbázis, amely globális elosztási, rugalmas tárhely- és teljesítményméretezési, automatikus indexelési és lekérdezési képességeket biztosít, valamint beállítható konzisztenciaszinteket és a TinkerPop szabvány támogatását. 

Azure Cosmos DB Gremlin API kínál egyedi funkciókat a következők:

* **Rugalmasan méretezhető átviteli sebesség és tárterület**

  A valós életben a gráfokat egyetlen kiszolgáló kapacitásán felül kell tudni méretezni. Az Azure Cosmos DB vízszintesen méretezhető gráfadatbázis-adatbázisok, amelyeken a gyakorlatilag korlátlan méretű és a kiosztott átviteli sebesség támogatja. A gráf adatbázis-méretezési csoport növekedésével az adatok automatikusan terjesztésre kerülnek használatával [graph-particionálás](https://docs.microsoft.com/azure/cosmos-db/graph-partitioning).

* **Többrégiós replikáció**

  Az Azure Cosmos DB automatikusan replikálhatja a grafikon adatainak bármelyik Azure-régióban. A replikáció leegyszerűsíti a globális adatokhoz való hozzáférést igénylő alkalmazások fejlesztését. Olvasási késés minimalizálása, valamint Azure Cosmos DB lehetővé teszi a regionális feladatátvétel is biztosíthatja, hogy az alkalmazás azon ritka esetekben egy régióban szolgáltatáskiesésről folytonosságát. 

* **Gyors lekérdezéseket és bejárásokat a legszélesebb körben elfogadott gráf lekérdezés standard**

  Heterogén csúcsokat és éleket tárolhat, és a már ismerős Gremlin-szintaxis használatával kérdezheti le ezeket a dokumentumokat. Gremlin egy imperatív, működési lekérdezési nyelvvel, amelyek egy közös graph algoritmusok megvalósításának részletes felületet is biztosít. 
  
  Az Azure Cosmos DB lehetővé teszi a részletes, valós idejű lekérdezéseket és bejárásokat sémamutatók, másodlagos indexek vagy nézetek megadása nélkül. További információk: [Gráfok lekérdezése a Gremlin használatával](gremlin-support.md).

* **Teljes körűen felügyelt gráfadatbázis**

  Az Azure Cosmos DB használatával nincs szükség az adatbázis és a gép erőforrásainak kezelésére. A legtöbb meglévő graph database-platformokra infrastruktúrára vonatkozó korlátozások vannak kötve, és gyakran igényelnek magas fokú karbantartási működésének biztosítása érdekében. 
  
  Egy teljes körűen felügyelt Microsoft Azure szolgáltatásként nincs szükség virtuális gépek kezelése, futásidejű szoftverek frissítése, a horizontális skálázás vagy a replikáció kezelése vagy az adatrétegek frissítésére összetett nem. Minden gráfról automatikus biztonsági mentés készül, és védelmet élveznek a regionális meghibásodásokkal szemben. Ezek a garanciák lehetővé teszi a fejlesztők számára, hangsúlyt alkalmazás érték üzemeltetése és kezelése az adatbázisok helyett. 

* **Automatikus indexelés**

  Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli a gráf csúcsain és élein található összes tulajdonságot, és nem vár vagy igényel semmilyen sémát, valamint nem szükséges másodlagos indexek létrehozása sem. Tudjon meg többet [indexelése az Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/index-overview). 

* **Az Apache TinkerPop való kompatibilitás érdekében**

  Az Azure Cosmos DB támogatja a [nyílt forráskódú Apache TinkerPop standard](http://tinkerpop.apache.org/). A Tinkerpop standard bőséges ökoszisztémája az alkalmazások és könyvtárak, amelyek egyszerűen integrálható az Azure Cosmos DB Gremlin API-val rendelkezik. 

* **Aprólékosan beállítható konzisztenciaszintek**

  Öt jól meghatározott konzisztenciaszint közül választhat a konzisztencia és a teljesítmény közötti optimális kompromisszum elérése érdekében. A lekérdezések és olvasási műveletek esetében az Azure Cosmos DB öt különböző konzisztenciaszintet kínál: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késleltetés között. További információk: [Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](consistency-levels.md).

## <a name="scenarios-that-can-use-gremlin-api"></a>A Gremlin API használatának forgatókönyvei
Íme néhány forgatókönyv, amelyben az Azure Cosmos DB gráftámogatása hasznosítható:

* Közösségi hálózatok

  Az ügyfeleivel és ügyfeleinek más személyekkel való interakcióival kapcsolatos adatok kombinálásával személyre szabott élményeket dolgozhat ki, előre jelezheti az ügyfelek viselkedését, vagy összekötheti a hasonló érdeklődési körű személyeket. Az Azure Cosmos DB használatával felügyelhetők a közösségi hálózatok, és nyomon követhetők az ügyfelek preferenciái és adatai.

* Javaslati motorok

  Ezt a forgatókönyvet gyakran alkalmazzák a kiskereskedelemben. A termékekkel, a felhasználókkal és a felhasználók interakcióival (például vásárlások, böngészés vagy az egyes cikkek értékelése) kapcsolatos információk kombinálásával testre szabott javaslatokat állíthat össze. Az Azure Cosmos DB kis késleltetésű, rugalmasan méretezhető és natív gráftámogatása ideális ezeknek az interakcióknak a modellezéséhez.

* Térinformatikai

  Számos távközlési, logisztikai és utazástervezési alkalmazásban van szükség az egyes helyszínek egy területen belül való megkeresésére, vagy a két helyszín közötti legrövidebb/optimális útvonal megállapítására. Az Azure Cosmos DB természetes választás az ilyen problémákhoz.

* Eszközök internetes hálózata

  Ha a hálózatot és az IoT-eszközök közötti kapcsolatot gráfként modellezi, jobban megértheti az eszközök és adategységek állapotát. Azt is megértheti, hogy a hálózat egyes részeinek módosítása milyen potenciális hatással lehet a többi részre.

## <a name="introduction-to-graph-databases"></a>Bevezetés a gráf típusú adatbázisokat
Az adatok a való világban természetes módon kapcsolódnak egymáshoz. A hagyományos modellezési külön-külön definiálása entitások és a kapcsolatok futásidőben számítástechnika összpontosít. Amíg ez a modell megvannak a maga előnyei, magas szinten csatlakoztatott adatok kihívást jelenthet a megkötések alapján kezelheti.  

A graph-adatbázis megközelítés támaszkodik megőrzése kapcsolatokat a tárolási rétegben ehelyett amely vezet, nagy hatékonyságú graph adatbeolvasási műveletekkel. Az Azure Cosmos DB Gremlin API támogatja a [tulajdonság graph modell](https://tinkerpop.apache.org/docs/current/reference/#intro).

### <a name="property-graph-objects"></a>Tulajdonság graph-objektumok

Vlastnost [graph](http://mathworld.wolfram.com/Graph.html) olyan struktúra, amely áll, amelyek [csúcspontok](http://mathworld.wolfram.com/GraphVertex.html) és [élek](http://mathworld.wolfram.com/GraphEdge.html). Mindkét objektumok lehetnek tetszőleges számú kulcs-érték párok tulajdonságait. 

* **Csúcspontok** -csúcspontok jelöl különálló entitások, például olyan személy, egy helyen vagy egy eseményt.

* **Élek** – Az élek a csúcsok közötti kapcsolatokat jelölik. Például egy adott személy ismerhet egy másik személyt, részt vehet egy eseményen, vagy nemrégiben meglátogathatott egy helyszínt. 

* **Tulajdonságok** – A tulajdonságok a csúcsokkal és élekkel kapcsolatos információkat jelölik. Tetszőleges számú csúcspontok vagy élek tulajdonságok is lehet, és be, és a lekérdezés-objektumok szűrése a használható. Példa tulajdonságai, amelynek a neve és a korszűrő csúcs, vagy egy edge, amelyekhez időbélyeg és/vagy másik tartalmazza. 

Gráf típusú adatbázisokat általában a nosql-alapú tartalmaz, vagy nem relációs, mivel nincs függőség a séma vagy korlátozott adatmodell adatbázis kategória. Séma hiánya lehetővé teszi, hogy a modellezésre és a természetes módon és hatékony tárolására a összekapcsolt struktúrákkal. 

### <a name="gremlin-by-example"></a>Példa a Gremlin használatára
Használjunk egy mintagráfot annak megismeréséhez, hogyan adhatók meg lekérdezések a Gremlinben. Az alábbi ábrán egy üzleti alkalmazás látható, amely felhasználókkal, érdeklődési körökkel és eszközökkel kapcsolatos adatokat kezel egy gráf formájában.  

![Személyeket, eszközöket és érdeklődési köröket tartalmazó mintaadatbázis](./media/gremlin-support/sample-graph.png) 

A gráfban a következő csúcsponttípusok találhatók (ezeket a Gremlinben „címkéknek” nevezzük):

- Személyek: A diagramnak három ember Robin, Thomas és Ben
- Érdekében: Az érdeklődési ebben a példában a játék futball
- Eszközök: Az eszközök, amelyeket azok, akik használata
- Operációs rendszerek: Az eszközökön futó operációs rendszerek

Ezen entitások kapcsolatait a következő éltípusok/címkék jelzik:

- Tudja, hogy: Például "Thomas tudja Robin"
- Szeretne: Annak a személynek a gráfon, például "Ben olyan iránt labdarúgó" érdekében képviselő
- RunsOS: Hordozható számítógép a Windows operációs rendszer fut.
- Használja: Melyik eszközt használja a személy jelölésére. Robin például egy Motorola-telefont használ, amelynek sorozatszáma 77.

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

A gráfok különösen akkor hasznosak, ha olyan kérdéseket kell megválaszolnunk, mint például: „Milyen operációs rendszereket használnak Thomas barátai?”. A Gremlin bejárási lekérni, hogy adatait a gráf futtathatja:

```java
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Most pedig tekintsük át, mit kínál az Azure Cosmos DB a Gremlin-fejlesztők számára.

## <a name="next-steps"></a>További lépések
A gráfok Azure Cosmos DB általi támogatásával kapcsolatos további információkért lásd:

* Első lépésként kezdje [az Azure Cosmos DB-gráfokkal kapcsolatos oktatóanyaggal](create-graph-dotnet.md).
* Ismerje meg [az Azure Cosmos DB-ben kezelt gráfok Gremlinnel való lekérdésének](gremlin-support.md) módját.

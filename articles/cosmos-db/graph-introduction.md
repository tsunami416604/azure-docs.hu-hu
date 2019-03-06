---
title: Bevezetés az Azure Cosmos DB Gremlin API-hoz
description: Ez a cikk azt ismerteti, hogy miként használható az Azure Cosmos DB közel valós idejű adateléréssel nagy méretű gráfok tárolására, lekérdezésére és bejárására az Apache TinkerPop gráflekérdezési nyelve, a Gremlin használatával.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/05/2018
ms.author: lbosq
ms.openlocfilehash: 10565839148eae0db4bb374939eb3725f6262e60
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452946"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Bevezetés: az Azure Cosmos DB: Gremlin API

Az [Azure Cosmos DB](introduction.md) a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása kritikus fontosságú alkalmazások számára. Többmodelles adatbázis, amely támogatja a dokumentumokat, a kulcs-érték párokat, a gráfokat és az oszlopos adatmodelleket. Az Azure Cosmos DB Gremlin API segítségével gráfadatokat lehet tárolni, és műveleteket lehet rajtuk végezni. A Gremlin API segítségével gráfadatok modellezhetők és járhatók be.

A cikk áttekintést nyújt az Azure Cosmos DB Gremlin API-járól, és elmagyarázza, hogyan használhatja a nagyméretű, több milliárd csúcsot és élet tartalmazó gráfok tárolására. A gráfokat ezredmásodperces késéssel kérdezheti le, és könnyen fejlesztheti a gráfok struktúráját és sémáját. Az Azure Cosmos DB lekérdezéséhez használhatja az [Apache TinkerPop](https://tinkerpop.apache.org) gráfbejárási nyelvet vagy a [Gremlint](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps).

## <a name="what-is-a-graph-database"></a>Mi az a gráfadatbázis?
Az adatok a való világban természetes módon kapcsolódnak egymáshoz. A hagyományos adatmodellezés az entitásokra fókuszál. Számos alkalmazás esetében szükséges az entitások és a kapcsolatok természetes modellezése is.

A [gráf](http://mathworld.wolfram.com/Graph.html) egy [csúcsokból](http://mathworld.wolfram.com/GraphVertex.html) és [élekből](http://mathworld.wolfram.com/GraphEdge.html) álló struktúra. A csúcsok és az élek egyaránt tetszőleges számú tulajdonsággal rendelkezhetnek. 

* **Csúcsok** – A csúcsok diszkrét objektumokat jelölnek, például személyeket, helyeket vagy eseményeket. 

* **Élek** – Az élek a csúcsok közötti kapcsolatokat jelölik. Például egy adott személy ismerhet egy másik személyt, részt vehet egy eseményen, vagy nemrégiben meglátogathatott egy helyszínt. 

* **Tulajdonságok** – A tulajdonságok a csúcsokkal és élekkel kapcsolatos információkat jelölik. Ilyen tulajdonságok lehet például a csúcsok neve vagy kora. Vagy egy él időbélyege és/vagy súlya. Formálisabban ezt a modellt [tulajdonsággráfnak](https://tinkerpop.apache.org/docs/current/reference/#intro) is nevezik. Az Azure Cosmos DB támogatja a tulajdonsággráf modellt.

Például az alábbi mintagráf személyek, mobileszközök, érdeklődési körök és operációs rendszerek közti kapcsolatokat ábrázol:

![Személyeket, eszközöket és érdeklődési köröket tartalmazó mintaadatbázis](./media/graph-introduction/sample-graph.png)

A gráfadatbázisok segítségével a gráfok természetesen és hatékonyan modellezhetők és tárolhatók, így számos forgatókönyvben hasznosnak bizonyulnak. A gráfadatbázisok jellemzően NoSQL-adatbázisok, mivel az ilyen esetekben gyakran van szükség rugalmas sémákra és gyors iterációra.

A gráfadatbázisok által lehetővé tett gyors bejárások kombinálhatók különféle gráfalgoritmusokkal, például mélységi (DFS) és szélességi (BFS) keresésekkel vagy Dijkstra-algoritmusokkal, a legkülönfélébb területekre (például közösségi hálózatok, tartalomkezelés, térinformatika vagy javaslatok kialakítása) tartozó problémák megoldása érdekében.

## <a name="features-of-azure-cosmos-db-graph-database"></a>Az Azure Cosmos DB gráfadatbázisának jellemzői
 
Az Azure Cosmos DB egy teljes körűen felügyelt gráfadatbázis, amely globális elosztási, rugalmas tárhely- és teljesítményméretezési, automatikus indexelési és lekérdezési képességeket biztosít, valamint beállítható konzisztenciaszinteket és a TinkerPop szabvány támogatását.

![Az Azure Cosmos DB gráfarchitektúrája](./media/graph-introduction/cosmosdb-graph-architecture.png)

Az Azure Cosmos DB a következő megkülönböztetett képességeket biztosítja a piacon elérhető többi gráfadatbázishoz képest:

* Rugalmasan méretezhető átviteli sebesség és tárterület

 A valós életben a gráfokat egyetlen kiszolgáló kapacitásán felül kell tudni méretezni. Az Azure Cosmos DB segítségével a gráfok zökkenőmentesen méretezhetők akár több kiszolgálóra is. Emellett a gráfok teljesítménye függetlenül méretezhető a hozzáférési mintáknak megfelelően. Az Azure Cosmos DB által támogatott gráfadatbázisok szinte korlátlanul méretezhető tárterületet és átviteli sebességet biztosítanak.

* Többrégiós replikáció

 Az Azure Cosmos DB transzparensen replikálja a gráfadatokat a fiókhoz társított összes régióba. A replikáció lehetővé teszi a globális adathozzáférést igénylő alkalmazások fejlesztését. Vannak azonban hátrányai a konzisztencia, a rendelkezésre állás és a teljesítmény, valamint a megfelelő garanciák terén. Az Azure Cosmos DB transzparens regionális feladatátvételt kínál többkiszolgálós API-k segítségével. A teljesítmény és a tárterület globális szinten rugalmasan méretezhető.

* Gyors lekérdezések és bejárások a már ismert Gremlin-szintaxissal

 Heterogén csúcsokat és éleket tárolhat, és a már ismerős Gremlin-szintaxis használatával kérdezheti le ezeket a dokumentumokat. Az Azure Cosmos DB az egyidejűséget támogató, zárolásmentes, naplószerkezetű indexelési technológiát alkalmaz a teljes tartalom indexeléséhez. Ez a képesség részletes, valós idejű lekérdezéseket és bejárásokat tesz lehetővé sémamutatók, másodlagos indexek vagy nézetek megadása nélkül. További információk: [Gráfok lekérdezése a Gremlin használatával](gremlin-support.md).

* Teljes körű felügyelet

 Az Azure Cosmos DB használatával nincs szükség az adatbázis és a gép erőforrásainak kezelésére. Teljes körűen felügyelt Microsoft Azure szolgáltatásként nincs szükség virtuális gépek kezelésére, szoftverek telepítésére és konfigurálására, a méretezés kezelésére vagy az összetett adatrétegek frissítésére. Minden gráfról automatikus biztonsági mentés készül, és védelmet élveznek a regionális meghibásodásokkal szemben. Könnyedén elvégezheti egy Azure Cosmos DB-fiók hozzáadását és a kapacitás szükség szerinti kiosztását, így az adatbázis üzemeltetése és kezelése helyett az alkalmazásra összpontosíthat.

* Automatikus indexelés

 Alapértelmezés szerint az Azure Cosmos DB automatikusan indexeli a gráf csúcsain és élein található összes tulajdonságot, és nem vár vagy igényel semmilyen sémát, valamint nem szükséges másodlagos indexek létrehozása sem.

* Kompatibilitás az Apache TinkerPoppal

 Az Azure Cosmos DB natív módon támogatja a nyílt forráskódú Apache TinkerPop szabványt, és integrálható más TinkerPop-kompatibilis rendszerekkel. Így egyszerűen válthat más gráfadatbázisokról (például Titan vagy Neo4j), vagy használhatja az Azure Cosmos DB-t különféle gráfelemző keretrendszerekkel (például Apache Spark GraphX).

* Beállítható konzisztenciaszintek

 Öt jól meghatározott konzisztenciaszint közül választhat a konzisztencia és a teljesítmény közötti optimális kompromisszum elérése érdekében. A lekérdezések és olvasási műveletek esetében az Azure Cosmos DB öt különböző konzisztenciaszintet kínál: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. Ezek a részletes, jól meghatározott konzisztenciaszintek lehetővé teszik, hogy ésszerű kompromisszumot alakítson ki a konzisztencia, a rendelkezésre állás és a késleltetés között. További információk: [Beállítható adatkonzisztencia-szintek az Azure Cosmos DB-ben](consistency-levels.md).

Az Azure Cosmos DB emellett képes többféle modellt (például dokumentumokat és gráfokat) alkalmazni egyazon tárolón/adatbázison belül. Dokumentumtárolók használatával a gráfadatokat a dokumentumokkal együtt tárolhatja. JSON-alapú SQL-lekérdezések és Gremlin-lekérdezések használatával egyaránt lekérdezheti a gráffal egyező adatokat.

## <a name="get-started"></a>Bevezetés

Az Azure Cosmos DB Gremlin API-fiókok létrehozása és az azokhoz való hozzáférés az Azure parancssori felület (CLI), az Azure PowerShell vagy az Azure Portal használatával lehetséges. Miután létrehozott egy fiókot, az abban található gráfadatbázisokhoz a Gremlin API szolgáltatásvégpontjának (`https://<youraccount>.gremlin.cosmosdb.azure.com`) használatával férhet hozzá, amely WebSocket-előteret biztosít a Gremlinhez. TinkerPop-kompatibilis eszközeit, például a [Gremlin konzolt](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) beállíthatja, hogy ehhez a végponthoz kapcsolódjanak, és az alkalmazásokat a Java, a Node.js vagy bármely Gremlin ügyfél-illesztőprogram használatával hozzák létre.

Az alábbi táblázat az Azure Cosmos DB-n használható népszerű Gremlin-illesztőprogramokat foglalja össze:

| Letöltés | Dokumentáció | Első lépések | Támogatott összekötő-verzió |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET on GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Gráf létrehozása a .NET használatával](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Gráf létrehozása a Java használatával](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript a GitHubon](https://github.com/jbmusso/gremlin-javascript) | [Gráf létrehozása a Node.js használatával](create-graph-nodejs.md) | 2.6.0|
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python a GitHubon](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Gráf létrehozása a Python használatával](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP a GitHubon](https://github.com/PommeVerte/gremlin-php) | [Gráf létrehozása a PHP használatával](create-graph-php.md) | 3.1.0 |
| [Gremlin-konzol](https://tinkerpop.apache.org/downloads.html) | [TinkerPop dokumentumok](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Gráf létrehozása a Gremlin-konzol használatával](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="graph-database-design-considerations"></a>Gráfadatbázisok kialakítási szempontjai

Graph tervezés közben a döntést, mint egy saját, nem pedig más csúcspont entitások tulajdonságaként csúcspont egy entitás modellezési következményeket teljesítménnyel és költségekkel. E döntéshozatal alapjául főként az adatok lekérdezésének módja és a modell méretezhetősége szolgál.

Az entitás modelljének megtervezése előtt a következő kérdéseket kell megfontolni:

* Melyek azok az entitások, amelyeket a legtöbb lekérdezésem csúcspontként kapna vissza?

* Melyek azok az információk, amelyeket adatszűrési célból adok a gráfhoz?

* Melyek azok az entitások, amelyek csupán kapcsolatként szolgálnak további, olyan entitásokhoz, amelyeknek az értékeit le szeretném kérdezni?

* Milyen információkat kell a lekérdezésemnek visszaadnia, és ez milyen kérelemegység-költséggel jár?

Vegyük például az alábbi gráfszerkezetet:

![Példa gráfszerkezet-kialakítási elvekre](./media/graph-introduction/graph-design-considerations-example.png)

* A lekérdezésektől függően elképzelhető, hogy a District (Kerület) -> Store (Áruház) kapcsolatot csak a Store (Áruház) csúcsok szűrésére használjuk. Ezt eredményezik például a „keresd meg az egy adott kerülethez tartozó összes áruházat” formátumú lekérdezések. Amennyiben ez a helyzet, érdemes a District (Kerület) entitást egy önálló csúcspontból a Store (Áruház) csúcspont egy tulajdonságává alakítani. 

* Ennek a megközelítésnek az az előnye, hogy az egyes Store (Áruház) csúcspontok lekérésének költségét csúcspontonként három gráfobjektumról (District (Kerület), District (Kerület) -> Store (Áruház), Store (Áruház)) egyetlen Store (Áruház) csúcspontra csökkentheti. Ez teljesítménynövekedéshez és a lekérdezésenkénti költség csökkentéséhez vezethet.

* A Store (Áruház) csúcspont két különböző entitásra (Employee (Alkalmazott) és Product (Termék)) hivatkozik. Ez szükségessé teszi a Store (Áruház) csúcspont meglétét, mert más bejárási módokra is lehetőséget ad.  



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

## <a name="next-steps"></a>További lépések
A gráfok Azure Cosmos DB általi támogatásával kapcsolatos további információkért lásd:

* Első lépésként kezdje [az Azure Cosmos DB-gráfokkal kapcsolatos oktatóanyaggal](create-graph-dotnet.md).
* Ismerje meg [az Azure Cosmos DB-ben kezelt gráfok Gremlinnel való lekérdésének](gremlin-support.md) módját.

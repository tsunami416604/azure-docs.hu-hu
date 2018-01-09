---
title: "Bevezetés az Azure Cosmos DB Graph API-k |} Microsoft Docs"
description: "Ismerje meg, hogyan használhatja Azure Cosmos DB vannak tárolva, a lekérdezés, és nagy diagramjait és kis késésű Apache TinkerPop Gremlin graph lekérdezés nyelvének segítségével haladnak át."
services: cosmos-db
author: luisbosquez
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/05/2017
ms.author: lbosq
ms.openlocfilehash: 14921dbeb0b670e4ec29a224caca07da12bfb82b
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Bevezetés az Azure Cosmos DB: Graph API

[Az Azure Cosmos DB](introduction.md) a globálisan elosztott, multimodel adatbázis-szolgáltatás a Microsoft a kritikus fontosságú alkalmazások. Azure Cosmos-adatbázis a következő szolgáltatásokat, az összes által támogatott biztosítja [iparágvezető SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/):

* [Kulcsrakész globális terjesztés](distribute-data-globally.md)
* [Átviteli sebesség és tárterület a rugalmas méretezést](partition-data.md) világszerte
* Egyjegyű ezredmásodperces késések fordulnak elő az 99th aránya:
* [Öt jól meghatározott konzisztenciaszintek](consistency-levels.md)
* Magas rendelkezésre állású garantált 

Az Azure Cosmos DB [automatikusan indexeli az adatokat](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) anélkül, hogy a felhasználónak sémákat és indexeket kellene kezelnie. Multimodel, és támogatja a dokumentum, a kulcs-érték, a graph és a oszlopos adatmodellekben.

Azt javasoljuk, hogy a következő videó, ahol Kirill Gavrylyuk ismerteti az Azure-Cosmos adatbázis diagramjait használatába megtekintése:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Graphs-with-Azure-Cosmos-DB-Gremlin-API/player]
> 
> 

Az Azure Cosmos DB Graph API biztosítja:

- Modellezési diagramot.
- Könyvtármegkerülési API-k.
- Kulcsrakész globális terjesztési.
- A rugalmas méretezést tárolási és átviteli, legfeljebb 10 ms késések olvasása és a 99th PERCENTILIS, kevesebb mint 15 ms.
- Automatikus indexelés azonnali lekérdezés rendelkezésre állással.
- Aprólékosan beállítható konzisztenciaszintek.
- Átfogó SLA-k, beleértve a rendelkezésre állás 99,99 % SLA fiókokhoz egyetlen régión és fiókokhoz több területi laza konzisztencia- és 99.999 % olvassa el az összes fiókot a több területi adatbázis rendelkezésre állási.

Azure Cosmos DB lekérdezéséhez használja a [Apache TinkerPop](http://tinkerpop.apache.org) átjárás nyelvi diagramot [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), vagy más kompatibilis TinkerPop graph rendszerek, például [Apache Spark GraphX](spark-connector-graph.md).

Ez a cikk áttekintést az Azure Cosmos DB Graph API-t, és ismerteti, hogyan használható az nagy diagramok és a csúcsban milliárd tárolására. A diagramokon lekérdezéseket az ezredmásodperces késési, és a graph struktúra és a séma könnyen fejleszteni.

## <a name="graph-database"></a>Graph-adatbázis
A valós életben megjelenő természetes kapcsolódnak. Hagyományos modellezési entitások összpontosít. Több alkalmazás is szükség van a következő modellre vagy entitásokat és a kapcsolatok modellezésére természetes.

A [graph](http://mathworld.wolfram.com/Graph.html) áll egy struktúra [csúcsban](http://mathworld.wolfram.com/GraphVertex.html) és [szélek](http://mathworld.wolfram.com/GraphEdge.html). Mind a csúcsban, és a szélek tulajdonságok tetszőleges számú tartozhat. Csúcsban jelöl diszkrét objektumok, például egy személy, egy helyen vagy egy eseményt. Szegélyek csúcsban közötti kapcsolatok jelöl. Például egy személy előfordulhat, hogy tudja, egy másik személyre kell részt az esemény és egy helyen nemrég átállították. Tulajdonságok az csúcsban és a vonatkozó információk express. Példa tulajdonságai közé tartozik, amelyen a neve, a kora, és a peremhálózati, amelynek időbélyegzőt és/vagy az egyik súlya csúcspont. Ez a modell több hivatalosan is ismert, egy [tulajdonság graph](http://tinkerpop.apache.org/docs/current/reference/#intro). Azure Cosmos-adatbázis a tulajdonság graph modellt támogatja.

Például a következő minta grafikon azt ábrázolja, felhasználók, mobileszközök, érdeklődési és operációs rendszerek közötti kapcsolatok szerepelnek:

![Személyek, eszközök és érdeklődési mintaadatbázis](./media/graph-introduction/sample-graph.png)

Diagramokat tudományos, technológia és üzleti adatkészletek számos működésének megértése. Graph adatbázisok lehetővé teszik, hogy a modell és az áruházbeli diagramjait természetesen és hatékonyan, ami hasznos, ha több forgatókönyv. Graph adatbázisok jellemzően NoSQL-adatbázisok, mert ezek használatát is gyakran esetekben a szükséges sémák rugalmasságát és gyors iterációs.

Diagramokat egy új és a hatékony módszer modellezési adatok kínálnak. De a tény önmagában nem elegendő miért egy grafikonon adatbázis használata. Számos használati esetek és minták graph traversals érintő diagramjait outperform hagyományos SQL és a NoSQL-adatbázisok által nagyságrenddel. A teljesítménybeli eltérést az további bővíteni, amikor egynél több kapcsolattal, például a "Friend"-az-a-barát áthaladó.

A gyors traversals graph adatbázisok biztosító kombinálhatja graph algoritmusok, például mélysége-első keresési, körének-első keresési vagy Dijkstra tartozó algoritmus, közösségi webhelyek, a tartalom kezelésére, a földrajzi, például különböző tartományokban problémák megoldására és javaslatok.

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Az Azure Cosmos DB bolygónk méretű diagramjait
Azure Cosmos-adatbázis egy teljes körűen felügyelt graph-adatbázist, amely globális terjesztési, rugalmas tárolási és átviteli, automatikus indexeléshez lekérdezés, konzisztenciaszinteket és a TinkerPop szabvány támogatása skálázás kínál.

![Azure Cosmos DB graph-architektúra](./media/graph-introduction/cosmosdb-graph-architecture.png)

Azure Cosmos-adatbázis a következő differenciált szolgáltatásokat a többi graph-adatbázis, a piacon képest biztosítja:

* Rugalmasan méretezhető átviteli sebesség és tárterület

 A valós életben diagramjait egyetlen kiszolgáló kapacitásának felüli kell. Az Azure Cosmos DB méretezheti a diagramok zökkenőmentesen több kiszolgáló között. Az átviteli sebessége a graph-egymástól függetlenül a hozzáférési minták alapján is méretezheti. Azure Cosmos-adatbázis, amely méretezhető, gyakorlatilag korlátlan tárterület mérete és a létesített átviteli sebesség graph-adatbázisokat támogatja.

* Több területi replikáció

 Azure Cosmos DB transzparens módon replikálja a Diagramadatok, amely a fiókjához tartozó minden régióban. Replikáció lehetővé teszi a globális adatok elérését igénylő alkalmazások fejlesztéséhez. Nincsenek mellékhatásokkal konzisztencia, a rendelkezésre állás, és a teljesítmény és a megfelelő garanciák területéhez. Azure Cosmos DB többhelyű API-khoz transzparens regionális feladatátvétel biztosít. Rugalmasan méretezhető átviteli sebesség és tárterület világszerte.

* Gyors lekérdezéseket és ismerős Gremlin szintaxissal traversals

 Heterogén csúcsban és szélek tárolja, és ezeket a dokumentumokat a megszokott Gremlin szintaxis keresztül lekérdezése. Azure Cosmos-adatbázis egy minden tartalom indexeléséhez magas egyidejű, zárolás ingyenes, naplószerkezetű indexelési technológiát használja. Ez a funkció lehetővé teszi, hogy részletes valós idejű lekérdezéseket és traversals sémamutatók, másodlagos indexek vagy nézetek megadása nélkül. További információ: [diagramjait lekérdezése Gremlin használatával](gremlin-support.md).

* Teljes körű felügyelet

 Az Azure Cosmos DB szükségtelenné adatbázis és a gép erőforrásainak kezelésére. Teljes körűen felügyelt Microsoft Azure szolgáltatásként akkor nem kell virtuális gépeket, a központi telepítése és a szoftver konfigurálása, a méretezés kezelésére, vagy az összetett adatrétegek frissítésére kezelésére. Minden graph automatikusan biztonsági másolat és védve legyenek a regionális meghibásodásokkal szemben. Egyszerűen Azure Cosmos DB fiók hozzáadása és mértékben kapacitást kell azt, hogy az alkalmazás üzemeltetése és kezelése az adatbázis helyett összpontosíthat.

* Az automatikus indexeléshez

 Alapértelmezés szerint Azure Cosmos DB automatikusan indexeli a csomópontok és a Graph szélek belül található összes tulajdonság, és nem várt vagy igényel semmilyen sémát, illetve másodlagos indexek létrehozását.

* Apache TinkerPop való kompatibilitás érdekében

 Azure Cosmos-adatbázis natív módon támogatja a nyílt forráskódú Apache TinkerPop szabványt, és más diagramhoz TinkerPop-kompatibilis rendszerek integrálható. Igen, egyszerűen telepítse át egy másik graph-adatbázisból, például Titan vagy Neo4j, vagy Azure Cosmos-Adatbázist kíván használni a graph analytics keretrendszerek, például a [Apache Spark GraphX](spark-connector-graph.md).

* Aprólékosan beállítható konzisztenciaszintek

 Válassza ki a öt jól meghatározott konzisztenciaszintek konzisztencia és a teljesítmény közötti optimális kompromisszumot eléréséhez. A lekérdezések és olvasási műveletek esetében az Azure Cosmos DB öt különböző konzisztenciaszintet kínál: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. A részletes, jól meghatározott konzisztenciaszintek lehetővé teszik, hogy a hang mellékhatásokkal konzisztencia, a rendelkezésre állás és a késleltetés között. További információ: [konzisztenciaszintek hangolható adatokat az Adatbázisba az Azure Cosmos](consistency-levels.md).

Azure Cosmos-adatbázis is használhat, több modellek, például a dokumentum és a graph belül az azonos tárolók adatbázisokat. Egy dokumentum gyűjtemény segítségével Diagramadatok és dokumentumok tárolására. SQL-lekérdezések JSON keresztül és a Gremlin lekérdezések segítségével egy grafikonon ugyanazokat az adatokat lekérdezni.

## <a name="get-started"></a>Bevezetés
Használhatja az Azure parancssori felület (CLI), Azure PowerShell vagy az Azure-portál támogatással rendelkező Graph API Azure Cosmos DB fiókokat létrehozni. Miután létrehozta a fiókok, az Azure-portál például biztosít-e egy végpontot, `https://<youraccount>.gremlin.cosmosdb.azure.com`, biztosít egy WebSocket előtér Gremlin számára. Konfigurálhatja a TinkerPop-kompatibilis eszközök, például a [Gremlin konzol](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console), hogy ehhez a végponthoz kapcsolódni, és a Java, Node.js vagy bármely Gremlin ügyfél-illesztőprogram alkalmazások.

Az alábbi táblázat a népszerű Gremlin illesztőprogramok szemben Azure Cosmos DB használható:

| Letöltés | Dokumentáció |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.js](https://www.npmjs.com/package/gremlin) |[Gremlin-JavaScript a Githubon](https://github.com/jbmusso/gremlin-javascript) |
| [Gremlin konzol](https://tinkerpop.apache.org/downloads.html) |[TinkerPop docs](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

Azure Cosmos-adatbázis is biztosít, amely rendelkezik a Gremlin kiterjesztésmetódusok .NET könyvtár az [Azure Cosmos DB SDK-k](sql-api-sdk-dotnet.md) NuGet útján. A szalagtár "egy folyamaton belüli" Gremlin kiszolgáló csatlakozzon közvetlenül a adatok partíciók használó biztosít.

| Letöltés | Dokumentáció |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

Használatával a [Azure Cosmos DB emulátor](local-emulator.md), használhatja a .NET Graph API-t a fenti fejlesztéséhez és teszteléséhez helyileg Azure-előfizetés létrehozása, és ezzel járó költségeket nélkül. Ha elégedett az alkalmazást az emulátorban alakulását, átválthat a felhőben Azure Cosmos DB fiókkal.

> [!NOTE]
> Gremlin Lekérdezések érvényesítése támogatása [Azure Cosmos DB emulátor](local-emulator.md) csak akkor áll rendelkezésre .NET Graph API-n keresztül.

## <a name="scenarios-for-graph-support-of-azure-cosmos-db"></a>Azure Cosmos DB graph-támogatása forgatókönyvei
Az alábbiakban néhány forgatókönyv graph támogatási Azure Cosmos-adatbázis helyének:

* Közösségi hálózatokkal

 Az ügyfelek és azok interakciók más felhasználókkal kapcsolatos adatok kombinálásával személyre szabott élmény fejlesztése, felhasználói viselkedés előrejelzése vagy személyek csatlakozás másokkal hasonló érdekében. Azure Cosmos-adatbázis segítségével kezelheti a közösségi hálózatokkal és nyomon követheti a felhasználói beállítások és adatok.

* A javaslat motorok

 Ebben a forgatókönyvben a kiskereskedelmi iparági gyakran használják. Termékek és felhasználók, felhasználói interakció megvásárlásáról, keresse meg vagy egy elem értékelése kombinálásával hozhat létre testreszabott javaslatok. A kis késleltetésű, rugalmasan méretezhető és natív Azure Cosmos DB graph-támogatása ezen kapcsolati modellezési ideális.

* Térinformatikai

 Számos alkalmazás távközlési, logisztikai és utazás megtervezése kell érdeklő hely keresése területen, vagy keresse meg a legrövidebb/optimális útvonal két hely között. Azure Cosmos-adatbázis a problémák természetes alkalmasnak.

* Eszközök internetes hálózata

 A hálózati és egy grafikonon modellezve az IoT-eszközök közötti kapcsolatokat az eszközök és az eszközök állapotának jobb megértése hozhat létre. Is is megismerheti, hogyan a hálózat egy részét a módosítások hatással lehet egy másik.

## <a name="next-steps"></a>További lépések
Az Azure Cosmos Adatbázisba graph-támogatással kapcsolatos további tudnivalókért lásd:

* Az első lépései a [Azure Cosmos DB graph oktatóanyag](create-graph-dotnet.md).
* További tudnivalók [lekérdezése az Azure Cosmos Adatbázisba diagramjait Gremlin használatával](gremlin-support.md).

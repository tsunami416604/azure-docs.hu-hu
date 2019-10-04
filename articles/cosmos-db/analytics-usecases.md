---
title: Használati esetek a beépített elemzésekhez Azure Cosmos DB használatával.
description: Megtudhatja, hogyan használhatja a Azure Cosmos DB különböző használati esetekben a beépített elemzéseket.
author: rimman
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 308e4d986fcbda155a7e6992f6efe0b1914bcfc2
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338957"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Használati esetek beépített elemzésekhez Azure Cosmos DB

A következő használati eseteket a beépített Analitika használatával lehet megvalósítani a Azure Cosmos DB Apache Sparkával:

## <a name="htap-scenarios"></a>HTAP-forgatókönyvek

A Azure Cosmos DB beépített elemzései a következőket használhatják:

* A tranzakciók feldolgozása során felmerülő csalás észlelése.
* Ajánlásokat adhat a vásárlóknak az e-kereskedelmi áruházban való böngészés során.
* A riasztási operátorok a kritikus gyártási berendezések közelgő meghibásodását jelentik.
* Gyors, működés közbeni betekintést hozhat létre a valós idejű elemzések közvetlenül az operatív adatain való beágyazásával.

A Azure Cosmos DB támogatja a hibrid tranzakciós/analitikai feldolgozási (HTAP) forgatókönyveket a natív módon beépített Apache Spark használatával. Azure Cosmos DB eltávolítja a hagyományos rendszerekhez tartozó operatív és analitikai elkülönítést.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Globálisan elosztott adatközpont ETL nélkül

A natív módon beépített Apache Sparkával a Azure Cosmos DB gyors, egyszerű és méretezhető megoldást kínál egy globálisan elosztott, egyetlen rendszerképet biztosító adattó létrehozásához. A globálisan elosztott, többmodelles adattípusok miatt nem kell költséges ETL-folyamatokat befektetni, és igény szerinti skálázást végezni, forradalmasítva az adatcsapatok adatcsoportjainak elemzését.

## <a name="time-series-analytics-over-historic-data"></a>Idősorozat-elemzés a korábbi adatértékeken

Bizonyos esetekben előfordulhat, hogy az adott időpontra vonatkozó, a múltban betöltött eseményeken alapuló kérdéseket kell megválaszolnia. Például a CRM-tevékenységek állapotának egy adott napon való lekéréséhez. Ha egy héttel ezelőtt futtatta a jelentést, az állapotok száma az adott időponthoz tartozó tevékenységek állapotának megfelelően alakul. Ha ugyanezt a jelentést jelenleg is futtatja, akkor azoknak a tevékenységeknek a száma, amelyek állapota a mai nap, ami a múlt hét óta módosult, mivel az életciklusuk nyitva van a nyitotttól a bezárásig. Ezért az eset életciklusának minden egyes fázisában jelentést kell készítenie a pillanatképről.

A hagyományos adattárház-forgatókönyvek esetében a pillanatkép fogalma nem lehetséges, mert az adattárházak nem úgy vannak kialakítva, hogy az adatok csak aktuális képet adjanak a szolgáltatásról. A Azure Cosmos DB lehetővé teszi a felhasználók számára az időutazás fogalmának megvalósítását, amely lehetővé teszi az adatelemzések visszamenőleges lekérdezését és futtatását, valamint annak megadását, hogy az adott időpontot hogyan nézett ki az előzményekben. Ez azt jelenti, hogy a felhasználók könnyen megtekinthetik az aktuális és a korábbi nézeteket, és az elemzést is futtathatják.

## <a name="globally-distributed-machine-learning-and-ai"></a>Globálisan elosztott gépi tanulás és AI

Mivel a vállalatok gyorsan növekvő mennyiségű adattal és számos adattípussal és-formátummal foglalkoznak, a mélyebb és pontosabb információk megszerzésének lehetősége szinte lehetetlen lesz a világ petabyte-skáláján. A natív módon beépített Apache Sparkával a Azure Cosmos DB egy globálisan elosztott elemzési platformot biztosít, amely széles körű, gépi tanulási algoritmusokat kínál. Az interaktív Jupyter jegyzetfüzetekkel modelleket és fürtözési képességeket hozhat létre és taníthat. Ezek a képességek lehetővé teszik az igény szerinti, magasan hangolt és automatikusan rugalmas Spark-fürtök kiépítését.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Mélyreható tanulás a többmodelles globálisan elosztott adatforgalomról

A Deep learning ideális módszer big data prediktív elemzési megoldások biztosítására, mivel az adatmennyiség és az összetettség folyamatosan növekszik. A mély tanulással a vállalatok kihasználhatják a strukturálatlan és részben strukturált adatmennyiséget, hogy olyan használati eseteket használjanak, amelyek olyan technikákat használnak, mint például a mesterséges intelligencia, a természetes nyelvi feldolgozás és sok más.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Jelentéskészítés (Power apps integrálása, Power BI)

A Power BI interaktív vizualizációkat biztosít önkiszolgáló üzleti intelligencia képességekkel, lehetővé téve, hogy a végfelhasználók jelentéseket és irányítópultokat hozzanak létre saját maguknak. A beépített Spark-összekötő használatával Power BI Desktop csatlakozhat a Azure Cosmos DB Apache Spark-fürtökhöz. Ez az összekötő lehetővé teszi, hogy közvetlen lekérdezéssel végezze el a feldolgozás kiszervezését Azure Cosmos DB Apache Spark, ami nagyszerű, ha olyan nagy mennyiségű adattal rendelkezik, amelyet nem szeretne betölteni Power BI vagy ha közel valós idejű elemzést szeretne végezni.

## <a name="iot-analytics-at-global-scale"></a>IoT-elemzés globális méretekben

A növekvő hálózati érzékelőkből generált adatok példátlan láthatóságot biztosítanak a korábban átlátszatlan rendszerek és folyamatok számára. A legfontosabb, hogy a jelen torrentben hasznosítható elemzéseket találjon, függetlenül attól, hogy a IoT-eszközök hol vannak elosztva a világon. Azure Cosmos DB lehetővé teszi, hogy a IOT-vállalatok nagy sebességű érzékelőt elemezzenek, és az idősoros adatsorozatok valós időben legyenek a világ bármely pontján. Lehetővé teszi, hogy egy összekapcsolt világ valódi értékét kihasználva jobb felhasználói élményt nyújtson, működési hatékonyságot és új bevételi lehetőségeket biztosítson.

## <a name="stream-processing-and-event-analytics"></a>Adatfolyam-feldolgozás és esemény-elemzés 

A naplófájlokból az érzékelőkbe való adatgyűjtéshez egyre több üzletnek kell megbirkóznia az adatok adatforrásaival. Ezek az adatok állandó adatfolyamban érkeznek, gyakran egyszerre több forrásból. Habár lehetséges, hogy tárolja ezeket az adatstreameket a lemezen, és visszamenőlegesen elemezze őket, esetenként ésszerű vagy fontos lehet a feldolgozásra és az adatfeldolgozásra. A pénzügyi tranzakciókra vonatkozó adatstreamek például valós időben feldolgozhatók a potenciálisan csalárd tranzakciók azonosítására és elutasítására.

## <a name="interactive-analytics"></a>Interaktív elemzés

Azon kívül, hogy az előre definiált lekérdezések statikus irányítópultokat hozzanak létre az értékesítéshez, a termelékenységhez vagy a tőzsdei árakhoz, érdemes lehet interaktív módon felderíteni az adatelemzést. Az interaktív elemzések segítségével kérdéseket tehet fel, megtekintheti az eredményeket, megváltoztathatja az első kérdést a válasz vagy a részletezés mélyebben történő megismerése alapján. A Azure Cosmos DB Apache Spark támogatja az interaktív lekérdezéseket, ha válaszol és alkalmazkodik gyorsan.

## <a name="data-exploration-using-jupyter-notebooks"></a>Adatelemzés Jupyter-jegyzetfüzetek használatával

Ha új adatkészlettel rendelkezik, mielőtt a futó modelleket és teszteket tapasztalja, meg kell vizsgálnia az adatokat. Más szóval a felderítő adatok elemzését kell végrehajtania. Az adatfeltárás számos döntést tud tájékoztatni. Megtalálhatja például az adatokhoz megfelelő módszereket, hogy az adatok megfelelnek-e bizonyos modellezési feltételezéseknek, hogy az adatokat meg kell-e tisztítani, átstrukturált stb. A Azure Cosmos DB natívan beépített Jupyter notebookokkal és Apache Sparkekkel gyors és hatékony felderítő adatelemzést végezhet a tranzakciós és analitikai adatokon.

## <a name="next-steps"></a>További lépések

Ha szeretné megkezdeni ezeket a használati eseteket, látogasson el a következő cikkekre:

* [Beépített Jupyter-jegyzetfüzetek Azure Cosmos DB](cosmosdb-jupyter-notebooks.md)
* [Jegyzetfüzetek engedélyezése az Azure Cosmos-fiókokhoz](enable-notebooks.md)
* [Jegyzetfüzet létrehozása az adatelemzéshez és az adatmegjelenítéshez](create-notebook-visualize-data.md)
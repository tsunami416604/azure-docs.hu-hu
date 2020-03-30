---
title: Használati esetek az Azure Cosmos DB beépített elemzéséhez.
description: Ismerje meg, hogyan használhatja a beépített elemzés az Azure Cosmos DB különböző használati esetekben.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: b9bac14ff47107a4b39f12989685e2e510569ba2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72757076"
---
# <a name="use-cases-for-built-in-analytics-with-azure-cosmos-db"></a>Használati esetek az Azure Cosmos DB beépített elemzéséhez

A következő használati esetek érhetők el az Apache Spark beépített elemzésével az Azure Cosmos DB-ben:

## <a name="htap-scenarios"></a>HTAP-forgatókönyvek

Az Azure Cosmos DB beépített elemzései a következőkre használhatók:

* A csalások felderítése a tranzakciók feldolgozása során.
* Javaslatokat tehet a vásárlóknak az e-kereskedelmi áruházak böngészése közben.
* Figyelmeztesse az üzemeltetőket egy kritikus gyártóberendezés közelgő meghibásodására.
* Gyors, végrehajtható betekintést hozhat létre a valós idejű elemzések közvetlenül a működési adatokba való beágyazásával.

Az Azure Cosmos DB hibrid tranzakciós/analitikai feldolgozási (HTAP) forgatókönyveket támogat a natívan beépített Apache Spark használatával. Az Azure Cosmos DB eltávolítja a hagyományos rendszerek hez kapcsolódó működési és analitikus elkülönítést.

## <a name="globally-distributed-data-lake-without-requiring-any-etl"></a>Globálisan elosztott adattó ETL nélkül

A natívan beépített Apache Spark segítségével az Azure Cosmos DB gyors, egyszerű és méretezhető módot kínál egy globálisan elosztott adattó létrehozásához, amely egyetlen rendszerlemezképet biztosít. A globálisan elosztott többmodelles adatok szükségtelenné teszik a költséges ETL-folyamatokba való befektetést, és igény szerint skálázhatják, forradalmasítva az adatcsoportok adathalmazaik elemzésének módját.

## <a name="time-series-analytics-over-historic-data"></a>Idősorozat-elemzés a múltbeli adatokkal szemben

Bizonyos esetekben előfordulhat, hogy az adatok on-ra vonatkozó kérdésekre kell válaszolnia, mint egy adott időpontban a múltban befejezett eseményekhez képest. Például a CRM-tevékenység állapotainak egy adott időpontban való leszámítása. Ha egy héttel ezelőtt futtatta a jelentést, az állapotok száma az adott időpontban az egyes tevékenységek állapotainak szerint lesz. Ha ma ugyanazt a jelentést futtatjuk, akkor megszámolhatja azon tevékenységeket, amelyek állapota olyan, mint ma, és amelyek a múlt hét óta változhattak, mivel az életciklusuk nyíltról záróra haladnak. Tehát az eset életciklusának minden szakaszában jelentést kell tennie a pillanatképről.

A hagyományos adattárház-forgatókönyvek, a pillanatkép fogalma nem lehetséges, mert az adatraktárak nem úgy tervezték, hogy bele, és az adatok csak egy aktuális nézetet biztosít, hogy mi történik. Az Azure Cosmos DB segítségével a felhasználók nak lehetőségük van az időutazás fogalmának megvalósítására, hogy visszamenőlegesen lekérdezhetik és futtathatják az adatokon lévő elemzéseket, és megkérdezhetik, hogy az adatok hogyan néztek az előzmények egy adott időpontjára. Ez azt jelenti, hogy a felhasználók könnyedén megtekinthetik az adatok aktuális és korábbi nézeteit, és elemzéseket futtathatnak rajta.

## <a name="globally-distributed-machine-learning-and-ai"></a>Globálisan elosztott gépi tanulás és AI

Mivel a vállalkozások gyorsan növekvő mennyiségű adattal és az adattípusok és -formátumok bővülő választékával küzdenek, a mélyebb és pontosabb betekintéslehetősége szinte lehetetlenné válik petabájtos méretekben szerte a világon. A natívan beépített Apache Spark segítségével az Azure Cosmos DB globálisan elosztott elemzési platformot biztosít, amely gépi tanulási algoritmusok széles tárkönyvtárát kínálja. Az interaktív Jupyter-jegyzetfüzetek segítségével modelleket hozhat létre és taníthat be, valamint fürtkezelési képességeket. Ezek a képességek lehetővé teszik, hogy magasan hangolt és automatikusan rugalmas Spark-fürtök igény szerint.

## <a name="deep-learning-on-multi-model-globally-distributed-data"></a>Deep Learning többmodelles globálisan elosztott adatokon

A deep learning ideális módja a big data prediktív elemzési megoldások biztosításának, mivel az adatok mennyisége és összetettsége folyamatosan növekszik. A mély tanulással a vállalkozások kiaknázhatják a strukturálatlan és félig strukturált adatok erejét, hogy olyan használati eseteket nyújtsanak, amelyek kihasználják az olyan technikákat, mint a a a mi, a természetes nyelvi feldolgozás stb.

## <a name="reporting-integrating-with-power-apps-power-bi"></a>Jelentéskészítés (integrálás a Power Apps, Power BI alkalmazásokkal)

A Power BI önkiszolgáló üzletiintelligencia-funkciókkal rendelkező interaktív vizualizációkat biztosít, lehetővé téve a végfelhasználók számára, hogy saját maguk hozzanak létre jelentéseket és irányítópultokat. A beépített Spark-összekötő használatával csatlakoztathatja a Power BI Desktopot az Azure Cosmos DB Apache Spark-fürtjeihez. Ez az összekötő lehetővé teszi, hogy közvetlen lekérdezéssel tehermentesítse a feldolgozást az Apache Spark-ba az Azure Cosmos DB-ben, ami nagyszerű, ha olyan nagy mennyiségű adatáll a power BI-ba, amelyet nem szeretne betölteni a Power BI-ba, vagy ha közel valós idejű elemzést szeretne végezni.

## <a name="iot-analytics-at-global-scale"></a>IoT-elemzés globális szinten

A növekvő hálózati érzékelőkből származó adatok példátlan betekintést nyújtanak a korábban átlátszatlan rendszerekbe és folyamatokba. A legfontosabb, hogy az információözőben használható elemzéseket találjon, függetlenül attól, hogy az IoT-eszközök hol vannak elosztva a világ minden táján. Az Azure Cosmos DB lehetővé teszi az IOT-vállalatok számára, hogy valós időben elemezzék a nagy sebességű érzékelőt és az idősorozat-adatokat a világ bármely pontján. Lehetővé teszi, hogy kihasználja az összekapcsolt világ valódi értékét, hogy jobb ügyfélélményt, működési hatékonyságot és új bevételi lehetőségeket biztosítson.

## <a name="stream-processing-and-event-analytics"></a>Streamelési feldolgozás és eseményelemzés 

A naplófájloktól az érzékelőadatokig a vállalkozásoknak egyre inkább meg kell birkózniuk az "adatfolyamokkal". Ezek az adatok folyamatos adatfolyamban érkeznek, gyakran egyszerre több forrásból. Bár ezeket az adatfolyamokat tárolható a lemezen, és visszamenőlegesen elemezheti őket, néha ésszerű vagy fontos lehet az adatok feldolgozása és az adatok alapján történő cselekvése, amint megérkezik. Például a pénzügyi tranzakciókhoz kapcsolódó adatfolyamok valós időben feldolgozhatók a potenciálisan csalárd tranzakciók azonosítása és elutasítása érdekében.

## <a name="interactive-analytics"></a>Interaktív analitika

Az előre definiált lekérdezések futtatása mellett statikus irányítópultokat is létrehozhat az értékesítési, termelékenységi vagy tőzsdei árakhoz, az adatokat interaktívan is fel kell tárnia. Az interaktív elemzések lehetővé teszik, hogy kérdéseket tegyen fel, megtekinthesse az eredményeket, a válasz alapján módosítsa a kezdeti kérdést, vagy mélyebben fúrjon az eredményekbe. Az Apache Spark az Azure Cosmos DB-ben támogatja az interaktív lekérdezéseket a gyors válaszadással és az adaptálással.

## <a name="data-exploration-using-jupyter-notebooks"></a>Adatok feltárása Jupyter notebookok használatával

Ha új adatkészlettel rendelkezik, mielőtt beleveti magát a modellek és tesztek futtatásába, meg kell vizsgálnia az adatokat. Más szóval feltáró adatelemzést kell végeznie. Az adatfeltárás több döntésről is tájékoztathat. Például olyan részleteket találhat, mint például az adatokon megfelelő módszerek, hogy az adatok megfelelnek-e bizonyos modellezési feltételezéseknek, hogy az adatokat meg kell-e tisztítani, át kell-e szervezni stb. Az Azure Cosmos DB natív beépített Jupyter-jegyzetfüzetei és az Apache Spark használatával gyors és hatékony feltáró adatok elemzésével gyors és hatékony feltáró adatokat elemezhet a tranzakciós és analitikus adatokon.

## <a name="next-steps"></a>További lépések

A használati esetek használatának megkezdéséhez látogasson el a következő cikkekre:

* [Beépített Jupyter-jegyzetfüzetek az Azure Cosmos DB-ben](cosmosdb-jupyter-notebooks.md)
* [Jegyzetfüzetek engedélyezése Az Azure Cosmos-fiókokhoz](enable-notebooks.md)
* [Jegyzetfüzet létrehozása az adatok elemzéséhez és megjelenítéséhez](create-notebook-visualize-data.md)
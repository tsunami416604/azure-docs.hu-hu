---
title: Mi az az Azure Synapse Analytics?
description: Az Azure szinapszis Analytics áttekintése
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 09/12/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c4338152579170bf809577262992f0db9a1a95ff
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "90524946"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Mi az Azure szinapszis Analytics (munkaterületek előzetes verziója)?

[!INCLUDE [preview](includes/note-preview.md)]

A nagyvállalati elemzésnek bármilyen típusú, nyers, finomított vagy nagy mértékben feldolgozható adatmennyiséget kell biztosítania. Ez a szükséges vállalatok összevarrják a big data és az adattárház-technológiákat, például a Sparkot és az SQL-t olyan gazdag adatfolyamatokkal, amelyek a kapcsolódó tárolókban és az adattavakban egyaránt működnek. Az ehhez hasonló megoldások nehezen hozhatók létre, biztonságosak és karbantarthatók. Az Insight Enteprises igénybe vételéhez szükséges összetettségi késések.

Az **Azure szinapszis** egy integrált elemzési szolgáltatás, amely felgyorsítja az adattárházak és a Big Data elemzési rendszerek elemzésének idejét. A legfontosabb, hogy az Azure szinapszis a nagyvállalati adattárházak, a big data használt **Spark** -technológiák, valamint az adatintegrációs **és ETL** /elt által használt **SQL** -technológiák közül a legjobbat egyesíti. A szinapszis egy webalapú **stúdió** , amely egyetlen helyet biztosít a felügyelethez, a monitorozáshoz, a kódoláshoz és a biztonsághoz. A szinapszis a többi Azure-szolgáltatással, például a **PowerBI**, a **CosmosDB**és a **AzureML**való szoros integrációt is tartalmaz.

## <a name="key-features--benefits"></a>A főbb funkciók & előnyök

### <a name="industry-leading-sql"></a>Piacvezető SQL

* A **SZINAPSZIS SQL** egy elosztott lekérdezési rendszer, amely lehetővé teszi, hogy a vállalatok adattárház-és adatvirtualizációs forgatókönyveket alkalmazzanak az adatmérnökök számára ismerős standard T-SQL-élmények használatával. Emellett kibővíti az SQL képességeit a streaming és a gépi tanulási forgatókönyvek kezeléséhez.

* A szinapszis SQL **kiszolgáló** nélküli és **dedikált** erőforrás-modelleket is kínál, amelyek az igényeinek megfelelő felhasználási és számlázási lehetőségeket kínálnak. A kiszámítható teljesítmény és a költséghatékonyság érdekében hozzon létre dedikált SQL-készleteket az SQL-táblákban tárolt adatfeldolgozási teljesítmény fenntartásához. Nem tervezett vagy feltört számítási feladatokhoz használja a mindig elérhető, kiszolgáló nélküli SQL-végpontot.
* A Felhőbeli adatforrásokból származó adatoknak az SQL-táblákba való beépítésére szolgáló beépített **streaming** -funkciók használata
* A AI és az SQL integrálása **gépi tanulási** modellek használatával a [T-SQL előrejelzési függvény](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest) használatával

### <a name="industry-standard-apache-spark"></a>Iparági szabványnak megfelelő Apache Spark

Az **Azure szinapszis Apache Sparke** mélyen és zökkenőmentesen integrálható Apache Spark-a legnépszerűbb nyílt forráskódú Big Data motor, amely adatelőkészítési, adattervezési, ETL-és gépi tanulási szolgáltatásokhoz használatos.

* SparkML-algoritmusokat és AzureML-integrációt biztosító ML-modellek a Linux Foundation Delta Lake beépített támogatásával Apache Spark 2,4.
* Egyszerűsített erőforrás-modell, amely felszabadul, hogy aggódnia kell a fürtök kezelésével kapcsolatban.
* Gyors Spark indítási és agresszív automatikus skálázás.
* A .NET for Spark beépített támogatása lehetővé teszi, hogy egy Spark-alkalmazásban újra felhasználhassa a C#-szaktudást és a meglévő .NET-kódot.

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Az SQL és a Apache Spark együttműködése a Data Lake

Az Azure szinapszis eltávolítja a hagyományos technológiai korlátokat az SQL és a Spark együttes használata között. Az igények és a szaktudás alapján zökkenőmentesen összekeverheti és összehasonlíthatja azokat.

* A megosztott struktúra-kompatibilis metaadatrendszer lehetővé teszi, hogy a (z) rendszer a (z)-ben lévő fájlokban meghatározott táblákat a Spark vagy a kaptár zökkenőmentesen felhasználja.
* Az SQL és a Spark közvetlenül képes felderíteni és elemezni az adattóban tárolt Parquet, CSV-, TSV-és JSON-fájlokat.
* Gyors skálázható betöltés és eltávolítás az SQL és a Spark adatbázisok közötti adattároláshoz

### <a name="built-in-data-integration-via-pipelines"></a>Beépített Adatintegráció az adatcsatornákon keresztül

Az Azure szinapszis beépített, ugyanazzal az adatintegrációs motorral és tapasztalatokkal rendelkezik, mint a Azure Data Factory, ami lehetővé teszi, hogy a szinapszis-elemzések elhagyása nélkül hozzon létre gazdag, méretezhető ETL-folyamatokat.

* Adatok beolvasása 90 és adatforrásból
* Kód – ingyenes ETL adatáramlási tevékenységekkel
* A notebookok, a Spark-feladatok, a tárolt eljárások, az SQL-parancsfájlok és egyebek összehangolása

### <a name="unified-management-monitoring-and-security"></a>Egységes felügyelet, figyelés és biztonság

Az Azure szinapszis egyetlen lehetőséget biztosít a vállalatok számára az elemzési erőforrások kezelésére, a használat és a tevékenységek figyelésére, valamint a biztonság betartatására.

* Felhasználók társítása szerepkörhöz az elemzési erőforrásokhoz való hozzáférés egyszerűbbé tétele érdekében
* Részletes hozzáférés-vezérlés az adatkapcsolaton és a kódban
* Egyetlen irányítópult az erőforrások, a használat és a felhasználók figyeléséhez az SQL és a Spark között

### <a name="synapse-studio"></a>Synapse Studio

A **szinapszis Studio** az a webes felület, amely az adatmérnökökkel együtt mindent összekapcsol, és lehetővé teszi számukra, hogy a teljes megoldás létrehozásához szükséges összes feladatnak egy helyen legyenek.

* Hozzon létre egy végpontok közötti elemzési megoldást egy helyen: betöltés, felfedezés, előkészítés, hangolás, megjelenítés
* Piacvezető termelékenység az adatmérnökök számára az SQL vagy a Spark kód írásához: szerzői műveletek, hibakeresés és teljesítmény optimalizálása
* Integrálás vállalati CI/CD-folyamatokkal

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Azure szinapszis Analytics szolgáltatással](get-started.md)
* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [Az igény szerinti SQL használata](quickstart-sql-on-demand.md)

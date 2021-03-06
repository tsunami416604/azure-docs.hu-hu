---
title: Mi az az Azure Synapse Analytics?
description: Az Azure szinapszis Analytics áttekintése
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/28/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c315dae7e5f02f112dfdfbec02e1ebaaa5e48a9f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445782"
---
# <a name="what-is-azure-synapse-analytics"></a>Mi az az Azure Synapse Analytics?

A nagyvállalati elemzési szolgáltatásnak bármilyen típusú, nyers, finomított vagy nagy teljesítményű adatmennyiségen kell működnie. Ehhez általában a vállalatoknak össze kell fűzve a big data és az adattárház-technológiákat olyan összetett adatfolyamatokban, amelyek a kapcsolati tárolókban és az adattavakban tárolt adatmennyiségek között működnek. Ezeket a megoldásokat nehéz felépíteni, karbantartani és biztonságossá tenni. Az Insight-vállalatok számára szükséges összetettségi késések.

Az **Azure szinapszis** egy integrált elemzési szolgáltatás, amely felgyorsítja az adattárházak és a Big Data rendszerek betekintésének idejét. Az Azure szinapszis a nagyvállalati adattárházban, a big data használt **Spark** -technológiákban, valamint az adatintegrációs és az ETL/elt- **adatcsatornákhoz** használt **SQL** -technológiákat egyesíti. A **szinapszis Studio** egységes felhasználói élményt nyújt a felügyelethez, a monitorozáshoz, a kódoláshoz és a biztonsághoz. A szinapszis szoros integrációt tartalmaz más Azure-szolgáltatásokkal, mint például a **Power bi**, a **CosmosDB** és a **AzureML**.

## <a name="key-features--benefits"></a>A főbb funkciók & előnyök

### <a name="industry-leading-sql"></a>Piacvezető SQL

* A **SZINAPSZIS SQL** egy elosztott lekérdezési rendszer, amely lehetővé teszi a vállalatok számára az adattárházak és az adatvirtualizációs forgatókönyvek megvalósítását standard és ismerős T-SQL-élmények használatával. Emellett kibővíti az SQL képességeit a streaming és a gépi tanulási forgatókönyvek kezeléséhez.

* A szinapszis SQL **kiszolgáló** nélküli és **dedikált** erőforrás-modelleket is kínál, amelyek az igényeinek megfelelő felhasználási és számlázási lehetőségeket kínálnak. A kiszámítható teljesítmény és a költséghatékonyság érdekében hozzon létre dedikált SQL-készleteket az SQL-táblákban tárolt adatfeldolgozási teljesítmény fenntartásához. Nem tervezett vagy feltört számítási feladatokhoz használja a mindig elérhető, kiszolgáló nélküli SQL-végpontot.
* A Felhőbeli adatforrásokból származó adatoknak az SQL-táblákba való beépítésére szolgáló beépített **streaming** -funkciók használata
* A AI és az SQL integrálása **gépi tanulási** modellekkel az adatpontszámhoz a [T-SQL prediktív függvény](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest) használatával

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

Az Azure szinapszis a Azure Data Factoryával azonos adatintegrációs motorral és tapasztalatokkal rendelkezik, így az Azure szinapszis Analytics használata nélkül hozhat létre gazdag, az ETL-alapú adatátviteli folyamatokat.

* Adatok beolvasása 90 és adatforrásból
* Code-Free ETL adatáramlási tevékenységekkel
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

## <a name="engage-with-the-synapse-engineering-team"></a>A szinapszis mérnöki csapatának részvétele

- [Stack overflow](https://stackoverflow.com/questions/tagged/azure-synapse): forduljon a fejlesztési kérdésekhez.
- [A Microsoft Q&egy kérdést tartalmazó oldalt](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html): technikai kérdéseket tehet fel.

## <a name="next-steps"></a>További lépések

* [Ismerkedés az Azure szinapszis Analytics szolgáltatással](get-started.md)
* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [Kiszolgáló nélküli SQL-készlet használata](quickstart-sql-on-demand.md)

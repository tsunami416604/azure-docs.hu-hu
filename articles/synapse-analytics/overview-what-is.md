---
title: Mi az az Azure Synapse Analytics?
description: Az Azure szinapszis Analytics áttekintése
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 96bb624472aa2053599765d5bfedeb96339d0973
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85808046"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Mi az Azure szinapszis Analytics (munkaterületek előzetes verziója)?

[!INCLUDE [preview](includes/note-preview.md)]

Napjainkban a vállalati elemzések nagy léptékű, bármilyen típusú, nyers, finomított vagy nagy teljesítményű adatmennyiséget igényelnek. A múltban az ilyen típusú elemzési megoldások felépítése szükséges a vállalatoktól a big data és az adattárház-technológiák, például a Spark és az SQL összefűzése érdekében. Ezután integrálni kellene őket olyan gazdag adatfolyamatokkal, amelyek a kapcsolódó adattárakban és az adattavakban egyaránt működnek.  

Az ehhez hasonló megoldások nehezen hozhatók létre, konfigurálhatók, biztonságosak és karbantarthatók, ami késlelteti az intelligens betekintés gyors kinyerését.

Az **Azure szinapszis** egy integrált elemzési szolgáltatás, amely az adattárházak és a Big Data elemzési rendszerek teljes skálájának, valamint az adattárházak és a elemzésének időpontját is felgyorsítja. Egyesíti a nagyvállalati adattárházban használt **SQL** -technológiákat, a Big Data elemzésekben használt **Spark** -technológiákat, valamint a tevékenységek és az adatáthelyezési **folyamatokat** .

Az Azure szinapszis egy webes natív **Studio** felhasználói felülettel rendelkezik, amely egyetlen élményt és modellt biztosít a felügyelethez, a monitorozáshoz, a kódoláshoz és a biztonsághoz.

Az Azure szinapszis a legegyszerűbb és leggyorsabb módszert kínálja a vállalatok számára, hogy bármilyen méretű adatelemzést gyűjtsenek az adatokról, amelyek a legismertebbek. Mélyen integrálva van **Power bi** lehetővé teszi, hogy az adatmérnökök olyan elemzési megoldásokat építsenek, amelyek teljes körűen működnek az üzleti intelligencia biztosításához.

Az Azure szinapszis emellett a **AzureML**beépített támogatásával lehetővé teszi a prediktív modell kialakítását és a fejlett elemzést a gépi tanulással.

## <a name="key-features--benefits"></a>A főbb funkciók & előnyök

### <a name="industry-leading-sql"></a>Piacvezető SQL

* A **SZINAPSZIS SQL** egy elosztott lekérdezési rendszer, amely lehetővé teszi, hogy a vállalatok adattárház-és adatvirtualizációs forgatókönyveket alkalmazzanak az adatmérnökök számára ismerős standard T-SQL-élmények használatával. Emellett kibővíti az SQL képességeit a streaming és a gépi tanulási forgatókönyvek kezeléséhez.

* A szinapszis SQL **kiszolgáló** nélküli és **kiépített** erőforrás-modelleket is kínál, és az igényeinek megfelelő használati és számlázási lehetőségeket kínál. A kiszámítható teljesítmény és a költséghatékonyság érdekében kiépítheti a készleteket az SQL-táblákban tárolt adatfeldolgozási teljesítmény fenntartásához. Nem tervezett vagy feltört számítási feladatok esetén használja a kiszolgáló nélküli, mindig elérhető SQL-végpontot.
* A Felhőbeli adatforrásokból származó adatoknak az SQL-táblákba való beépítésére szolgáló beépített **streaming** -funkciók használata
* A AI és az SQL integrálása **gépi tanulási** modellek használatával a T-SQL előrejelzési függvény használatával

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

### <a name="built-in-orchestration-via-pipelines"></a>Beépített előkészítés folyamatokon keresztül

Az Azure szinapszis a Azure Data Factoryával azonos adatintegrációs motorral és tapasztalatokkal rendelkezik, így gazdag adatfolyamatokat hozhat létre külön hangvezérelt motor használata nélkül.

* Az Azure szinapszis és a 90 + helyszíni adatforrások közötti adatáthelyezés
* Jegyzetfüzetek, folyamatok, Spark-feladatok, SQL-parancsfájlok és tárolt eljárások összehangolása
* Kód – ingyenes ETL adatáramlási tevékenységekkel

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

## <a name="next-steps"></a>Következő lépések

* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [A szinapszis Studio használata](quickstart-synapse-studio.md)
* [SQL-készlet létrehozása](quickstart-create-sql-pool-portal.md)
* [Az igény szerinti SQL használata](quickstart-sql-on-demand.md)
* [Apache Spark-készlet létrehozása](quickstart-create-apache-spark-pool-portal.md)

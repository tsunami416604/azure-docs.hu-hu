---
title: Mi az az Azure Synapse Analytics?
description: Az Azure Synapse Analytics áttekintése
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 65f6c6627093358f8cbc66055bb9b16561f7c610
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424663"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Mi az Azure Synapse Analytics (munkaterületek előzetes verzió)?

[!INCLUDE [preview](includes/note-preview.md)]

A vállalati elemzéshez ma a vállalati elemzéshez nagy léptékű működésre van szükség bármilyen típusú adaton, legyen az nyers, finomított vagy magasan válogatott. A múltban az ilyen típusú elemzési megoldások létrehozása szükséges vállalatok összefűzése big data és adattárház technológiák, mint a Spark és az SQL. Ezután integrálniuk kell őket a bővített adatfolyamatokba, amelyek a relációs tárolókban és az adattavakban lévő adatok között működnek.  

Az ilyen megoldásokat nehéz megépíteni, konfigurálni, biztonságossá és karbantartani, ami késlelteti az intelligens betekintés gyors kinyerését.

**Az Azure Synapse** egy integrált elemzési szolgáltatás, amely felgyorsítja az időt, hogy betekintést nyerjen az összes adatból bármilyen mértékben, az adatraktárak és a big data-elemzési rendszerek között. Egyesíti **a** vállalati adattárház, a big data-elemzésben használt **Spark-technológiák,** valamint a tevékenységek és az adatmozgások vezényléséhez használt folyamatok legjobb sql-technológiáit. **Pipelines** 

Az Azure Synapse webnatív **Studio** felhasználói élményt nyújt, amely egyetlen felhasználói élményt és modellt biztosít a felügyelethez, a figyeléshez, a kódoláshoz és a biztonsághoz.

Az Azure Synapse a legegyszerűbb és leggyorsabb módot biztosítja a vállalat számára, hogy bármilyen méretű adatokról betekintést gyűjtsön a leginkább ismert elemzésekkel. Mélyen integrálható a **Power BI-val,** lehetővé téve az adatmérnökök számára, hogy olyan elemzési megoldásokat dolgozzanak ki, amelyek az üzleti intelligencia biztosításához teljesen működőképesek. 

Emellett az Azure Synapse az **AzureML**beépített támogatásán keresztül megkönnyíti a prediktív modellfejlesztést és a fejlett elemzéseket a gépi tanulással.

## <a name="key-features--benefits"></a>A legfontosabb funkciók & előnyei

### <a name="industry-leading-sql"></a>Iparágvezető SQL

* **A Synapse SQL** egy elosztott lekérdezési rendszer, amely lehetővé teszi a vállalatok számára, hogy adatraktározási és adatvirtualizációs forgatókönyveket valósítsanak meg az adatmérnökök számára ismerős szabványos T-SQL-élmények használatával. Emellett kibővíti az SQL streamelési és gépi tanulási forgatókönyvek kezelésére is.

* A Synapse SQL **kiszolgáló nélküli** és kiépített **erőforrásmodelleket** is kínál, amelyek az igényeinek megfelelő fogyasztási és számlázási lehetőségeket kínálnak. A kiszámítható teljesítmény és a költségek, a készlet készlet eket az SQL-táblákban tárolt adatok feldolgozási teljesítményének lefoglalásához. Nem tervezett vagy nem tervezett számítási feladatokhoz használja a kiszolgáló nélküli, mindig elérhető SQL-végpontot.
* Beépített **streamelési** lehetőségek használata a felhőbeli adatforrásokból származó adatok SQL-táblákba való lehívásához
* A a mi-t integrálhatja az SQL-ből a **Gépi tanulási** modellek használatával az adatok pontozásához a T-SQL PREDICT függvény használatával

### <a name="industry-standard-apache-spark"></a>Az iparágban szabványos Apache Spark

**A Synapse Spark** mélyen és zökkenőmentesen integrálja az Apache Sparkot – a legnépszerűbb nyílt forráskódú big data motort, amelyet az adatok előkészítéséhez, az adattervezéshez, az ETL-hez és a gépi tanuláshoz használnak.

* A SparkML-algoritmusokkal és az Apache Spark 2.4-es AzureML-integrációval rendelkező ML-modellek beépített támogatással rendelkeznek a Linux Foundation Delta Lake számára.
* Egyszerűsített erőforrásmodell, amely leszabadítja Önt a fürtök kezelésétől.
* Gyors Spark-indítás és agresszív automatikus skálázás.
* A .NET for Spark beépített támogatása lehetővé teszi a C# szakértelem és a spark-alkalmazásokmeglévő .NET-kódjának újbóli felhasználását.

### <a name="interop-of-sql-and-spark-on-your-data-lake"></a>Az SQL és a Spark interopja a Data Lake-en

Az Azure Synapse megszünteti a hagyományos technológiai akadályokat az SQL és a Spark együtt való használata között. Zökkenőmentesen keverheti és párosíthatja az Ön igényeinek és szakértelmének megfelelően.

* A megosztott Hive-kompatibilis metaadat-rendszer lehetővé teszi, hogy a Data Lake-ben lévő fájlokon definiált táblákat zökkenőmentesen használja fel a Spark vagy a Hive.
* Az SQL és a Spark közvetlenül felfedezheti és elemezheti a Parquet, CSV, TSV és JSON fájlokat a data lake-ben.
* Gyorsméretezhető be- és eltávolítás az SQL- és Spark-adatbázisok közötti adatműveletekhez

### <a name="built-in-orchestration-via-pipelines"></a>Beépített vezénylés ifolyamatokon keresztül

Az Azure Synapse beépített, ugyanazzal az adatintegrációs motorral és felhasználói élményekkel érkezik, mint az Azure Data Factory, így különálló vezénylési motor használata nélkül hozhat létre gazdag adatfolyamatokat.

* Adatok áthelyezése a Synapse és a 85+ helyszíni adatforrásközött
* Jegyzetfüzetek, folyamatok, Spark-feladatok, SQL-parancsfájlok, tárolt eljárások vezénylése
* Kódmentes ETL adatáramlási tevékenységekkel

### <a name="unified-management-monitoring-and-security"></a>Egységes felügyelet, figyelés és biztonság

Az Azure Synapse egyetlen lehetőséget biztosít a vállalatok számára az elemzési erőforrások kezeléséhez, a használat és a tevékenységek figyeléséhez, valamint a biztonság érvényesítéséhez.

* Felhasználók hozzárendelése szerepkörhöz az elemzési erőforrásokhoz való hozzáférés egyszerűsítése érdekében
* Részletes hozzáférés-vezérlés az adatokon és a kódokon
* Egyetlen irányítópult az erőforrások, a használat és a felhasználók sql- és Spark-beli figyelésére

### <a name="synapse-studio"></a>Szinapszis Stúdió

**A Synapse Studio** a web-natív élmény, amely mindent összeköt az adatmérnökök számára, lehetővé téve számukra, hogy egy helyen végezzenek el minden feladatot, amire szükségük van a teljes megoldás létrehozásához.

* Hozzon létre egy teljes körű elemzési megoldást egy helyen: betöltés, felfedezés, előkészítés, vezénylés, vizualizálás
* Iparágvezető hatékonyság az SQL- vagy Spark-kódot író adatmérnökök számára: szerzői, hibakeresés iés teljesítményoptimalizálás
* Integrálás vállalati CI/CD folyamatokkal

## <a name="next-steps"></a>További lépések

* [Munkaterület létrehozása](quickstart-create-workspace.md)
* [A Synapse Studio használata](quickstart-synapse-studio.md)
* [SQL-készlet létrehozása](quickstart-create-sql-pool.md)
* [Sql igény szerinti használata](quickstart-sql-on-demand.md)
* [Apache Spark-készlet létrehozása](quickstart-create-apache-spark-pool.md)

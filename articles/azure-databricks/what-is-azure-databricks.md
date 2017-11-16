---
title: Mi az Azure Databricks? | Microsoft Docs
description: "Mi az Azure Databricks, és hogyan mindez Spark Databricks meg az Azure megismerése. Az Azure Databricks egy Apache Spark-alapú analytics platform, a Microsoft Azure cloud services platform optimalizálva."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: nitinme
ms.openlocfilehash: 7ced38cda2669cf03e51f50fbbbeea0344da9277
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="what-is-azure-databricks"></a>Mi az Azure Databricks?

Az Azure Databricks egy Apache Spark-alapú analytics platform, a Microsoft Azure cloud services platform optimalizálva. Az Apache Spark on is megtervezni, Databricks integrálva van egy kattintással telepítő, zökkenőmentes munkafolyamatok és adatszakértőkön, az adatok mérnökök és az üzleti elemzők együttműködése az interaktív munkaterületeket Azure.

![Mi az Azure Databricks? ] (./media/what-is-azure-databricks/azure-databricks-overview.png "Mi az Azure Databricks?")

## <a name="apache-spark-based-analytics-platform"></a>Apache Spark-alapú analytics platform

Az Azure Databricks a teljes nyílt forráskódú Apache Spark-fürt technológiák és képességeit foglalja magában. A Spark on Azure Databricks összetevői a következők:

![Az Apache Spark on Azure Databricks](./media/what-is-azure-databricks/apache-spark-ecosystem-databricks.png "az Apache Spark on Azure Databricks")

* **Spark SQL és DataFrames**: Spark SQL a strukturált adatok használata Spark-modult. Egy DataFrame megnevezett oszlopok csoportosítva adatok elosztott gyűjteménye. Az fogalmilag egy relációs adatbázisban, vagy az R vagy Python adatok keret táblájába.

* **Adatfolyam-**: valós idejű adatfeldolgozással és elemzések analitikai és interaktív alkalmazások számára. Integrálható a HDFS, Flume és Kafka.

* **MLib**: közös tanulási algoritmusok és segédprogramok, beleértve a besorolás, regressziós, fürtözés, együttműködést szűrés, granularitása csökkentése érdekében, valamint az alapul szolgáló optimalizálási primitívek álló Machine Learning könyvtár.

* **GraphX**: diagramok és a graph kiszámításának széles köre használja az adatok feltárása a kognitív analytics eset.

* **Spark mag API**: R, SQL, Python, Scala és Java támogatását is magában foglalja.

## <a name="apache-spark-in-azure-databricks"></a>Az Apache Spark on Azure Databricks

Az Azure Databricks azáltal, hogy egy nulla-kezelési felhőalapú platform, amely tartalmazza a Spark képességeire építkezik:

- Teljes körűen felügyelt Spark-fürtök
- A feltárására és a képi megjelenítés egy interaktív munkaterülete
- A kedvenc Spark-alapú alkalmazások – áttekintés egy platform

### <a name="fully-managed-apache-spark-clusters-in-the-cloud"></a>Teljes körűen felügyelt Apache Spark-fürtök a felhőben

Az Azure Databricks biztonságos és megbízható éles környezetben a felhőben, felügyelt és a Spark-szakértők által támogatott rendelkezik. A következőket teheti:

* Fürtök létrehozása másodpercben.
* Automatikus skálázási dinamikusan fürtök felfelé és lefelé kiszolgáló nélküli fürtökkel együtt, és megoszthatja őket csapatok. 
* Fürtök programozott módon használja a REST API-k használatával. 
* Védett adatok integrációs szolgáltatásai segítségével sparkra, amelyek lehetővé teszik az adatok nélkül központosítás egységes. 
* A legújabb Apache Spark-funkciókat az egyes kiadások azonnali eléréséhez.

### <a name="databricks-runtime"></a>Databricks futásidejű
A Databricks futtatási Apache Spark épül, és az Azure felhőalapú natív módon lett tervezve. 

Az a **kiszolgáló nélküli** beállítás, Azure Databricks teljesen kivonatolja infrastruktúra összetettségét és a szükséges speciális szakértelem beállítását és az adatok infrastruktúra konfigurálása. A kiszolgáló nélküli beállításnak a kutatók gyorsan csapatként többször adatok segítségével.

Adatok szakemberek számára, akik termelési feladatok teljesítményének érdeklik, Azure Databricks biztosít a Spark motor, amely gyorsabb és performant különböző optimalizálás az i/o-réteg és a feldolgozási réteg (Databricks I/O) keresztül.

### <a name="workspace-for-collaboration"></a>Együttműködés a munkaterülete

Együttműködési és integrált környezetet, keresztül Azure Databricks megkönnyíti a adatok prototípusának és Spark-alkalmazások futó adatvezérelt feltárása.

* Határozza meg az adatok használata egyszerű adatok feltárása.
* A folyamat állapotát a jegyzetfüzeteket R, Python, Scala vagy SQL-dokumentum.
* Adato néhány kattintással, és Matplotlib, ggoplot vagy d3 ismerős eszközökkel.
* Interaktív irányítópultokat használatával dinamikus jelentések létrehozása.
* Válassza a Spark, és egyidejűleg az adatok kezeléséhez.

## <a name="enterprise-security"></a>A vállalati biztonsági

Az Azure Databricks biztosít a vállalati szintű Azure Active Directory-integráció, szerepköralapú vezérlők és az adatok és az üzleti védő SLA-k többek között az Azure biztonsági.

* Azure Active Directory integrációja lehetővé teszi a teljes Azure-alapú megoldások Azure Databricks segítségével futtathatja.
* Azure Databricks szerepkör alapú hozzáférés lehetővé teszi, hogy a minden részletre kiterjedő felhasználói engedélyeinek notebookok, a fürtök, a feladatok és az adatok.
* Vállalati szintű SLA-k. 

## <a name="integration-with-azure-services"></a>Integráció az Azure-szolgáltatásokkal

Azure Databricks mélyen jól integrálható az Azure adatbázisok és a tárolók: az SQL Data Warehouse, a Cosmos DB, a Data Lake Store és a Blob Storage tárolóban. 

## <a name="integration-with-power-bi"></a>Integráció a Power bi-ban
A Power BI gazdag integrálásán keresztül Azure Databricks lehetővé teszi, hogy észlelni és megoszthatja az impactful elemzések gyors és egyszerű. Más Üzletiintelligencia-eszközök, például a Tableau szoftver JDBC-/ ODBC fürt végpontjaira keresztül is használhatja.

## <a name="next-steps"></a>Következő lépések

* [Gyors üzembe helyezés: Spark feladatot futtatni, Azure Databricks kiszolgálón](quickstart-create-databricks-workspace-portal.md)
* [A Spark-fürtök használata](https://docs.azuredatabricks.net/user-guide/clusters/index.html)
* [Notebookok használata](https://docs.azuredatabricks.net/user-guide/notebooks/index.html)
* [A Spark-feladatok létrehozása](https://docs.azuredatabricks.net/user-guide/jobs.html)

 










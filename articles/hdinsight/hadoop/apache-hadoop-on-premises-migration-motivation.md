---
title: A helyszíni Apache Hadoop-fürtök áttelepítése Azure HDInsight - Motiváció és előnyök
description: Ismerje meg, a Motiváció és áttelepíteni a helyszíni Hadoop-fürtöket az Azure HDInsight-kedvezmények.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: efaedccde854d8cd7fa777aa9457db7203ce833a
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50221900"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>A helyszíni Apache Hadoop-fürtök áttelepítése Azure HDInsight - Motiváció és előnyök

Ez a cikk egy sorozat az ajánlott eljárások a migrálás első helyszíni: az Azure HDInsight az Apache Hadoop ökoszisztéma központi telepítések. A cikksorozat olyan személyek, akik felelősek a tervezési, telepítési és az Azure HDInsight az Apache Hadoop-megoldások áttelepítése. A szerepkörök, amelyek előfordulhat, hogy ezek a cikkek közé tartozik a felhőben dolgozó tervezők, a Hadoop-rendszergazdák és a fejlesztési és üzemeltetési mérnökök. A szoftverfejlesztők, az adatmérnökök és az adatelemzők kell is élvezhetik a magyarázat, különböző típusú fürtök munkát a felhőben.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Miért az Azure HDInsight áttelepítése

Az Azure HDInsight a Hadoop-összetevők felhőalapú terjesztett a [Hortonworks Data Platform(HDP)](https://hortonworks.com/products/data-center/hdp/). Az Azure HDInsight segítségével könnyen, gyorsan és költséghatékonyan dolgozható fel nagy mennyiségű adat. HDInsight a legnépszerűbb nyílt forráskódú keretrendszerekkel például tartalmazza:

- Apache Hadoop
- Apache Spark
- Az Apache Hive LLAP-
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="advantages-that-azure-hdinsight-offers-over-on-premises-hadoop"></a>Milyen előnyökkel jár az Azure HDInsight kínál a helyszíni Hadoop

- **Alacsony költség** -költségeket is csökkentette [igény szerinti fürtök létrehozása](../hdinsight-hadoop-create-linux-clusters-adf.md) , és csak azért kell fizetnie kell fizetni. Függetlenített számítás és tárolás rugalmasságot biztosít azáltal adatmennyiség független a fürt méretét.
- **Fürt létrehozása az automatikus** automatizált – fürt létrehozása előtt minimális beállítása és konfigurációja. Igény szerinti fürtök Automation is használható.
- **A felügyelt hardveres és konfigurációs** – nem kell aggódnia a fizikai hardver- vagy infrastruktúra-HDInsight-fürthöz. Csak adja meg a fürt konfigurációját, és az Azure állítja be.
- **Könnyen méretezhető** -HDInsight lehetővé teszi, hogy [méretezési](../hdinsight-administer-use-portal-linux.md) felfelé vagy lefelé számítási feladatokhoz. Azure gondoskodik az adatok újraelosztás és a számítási feladatok kiegyenlítése adatfeldolgozási feladatok megszakítása nélkül.
- **Globális elérhetőség** -HDInsight érhető el további [régiók](https://azure.microsoft.com/regions/services/) , mint bármely más big data-elemzési ajánlat. Az Azure HDInsight elérhető az Azure Governmentben, Kínában, és Németországban is, így megfelelhet a vállalati igényeknek a főbb szuverén területeken.
- **Biztonságos és megfelelő** -HDInsight lehetővé teszi, hogy az a vállalati adategységek védelmét [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md), [titkosítási](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md), és integrációs szolgáltatásaival [Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md). HDInsight továbbá megfelel a legnépszerűbb iparági és kormányzati [megfelelőségi szabvány](https://azure.microsoft.com/overview/trusted-cloud).
- **Verziókezelés egyszerűsített** – Azure HDInsight Hadoop-ökoszisztéma összetevők verziója kezeli, és naprakészen tartja őket. Szoftverfrissítések rendszerint összetett folyamat a helyszíni üzemelő példányok esetében.
- **Kisebb fürtök összetevői között kevesebb függőségekkel adott feladatokra optimalizált** – egy tipikus helyszíni Hadoop-beállítást használja, amely számos célra szolgál egy fürtön. Az Azure HDInsight, a munkaterhelés-specifikus fürtök hozható létre. Adott munkaterhelés-fürtök létrehozása során megszűnnek a fenntartása bonyolultsága nő az egy fürtön.
- **Termelékenység** -használható különböző eszközöket biztosít a Hadoop és Spark a választott fejlesztési környezetben.
- **Az egyéni eszközöket vagy harmadik féltől származó alkalmazások bővíthetőség** -HDInsight-fürtök telepített összetevők ki lehet terjeszteni, és segítségével az más big data-megoldások is integrálhatók [egykattintásos](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)  az Azure Marketplace-beli telepítéseit.
- **Könnyű kezelés felügyeleti, feladatütemezési és figyelési** – Azure HDInsight integrálható [Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) , amellyel követheti az összes fürt egységes felületet biztosít.
- **Integráció más Azure szolgáltatásokkal** -HDInsight egyszerűen integrálható más népszerű Azure-szolgáltatások például a következőket:

    - Az Azure Data Factory (ADF)
    - Azure Blob Storage
    - 2. generációs Azure Data Lake Storage
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Önjavító tárolószervezéssel folyamatokkal és összetevőkkel** -HDInsight folyamatosan ellenőrzi az infrastruktúra és a nyílt forráskódú összetevők a saját figyelési infrastruktúra használatával. Kritikus hibák, például a nyílt forráskódú összetevőkkel és elérhetetlensége automatikusan is helyreállítja. Riasztások aktiválódnak az Ambari, ha bármely nyílt Forráskódú összetevő nem sikerült.

További információkért tekintse meg a cikket [Mi az Azure HDInsight és a Hadoop-technológiák](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Áttelepítés tervezési folyamat

Az Azure HDInsight Hadoop-fürtöket a helyszíni áttelepítés tervezése ajánlott az alábbi lépéseket:

1. Ismerje meg a jelenlegi helyszíni üzemelő példány és a topológiák.
2. Ismerje meg a jelenlegi projekt hatókörének, az ütemtervek és a csapat szaktudását.
3. Ismerje meg az Azure-követelményeknek.
4. A részletes terv az ajánlott eljárások alapján hozhatja létre.

## <a name="next-steps"></a>További lépések

Olvassa el az oktatóanyag-sorozatban a következő cikkben:

- [A helyszíni Azure HDInsight Hadoop-áttelepítési architektúra ajánlott eljárásai](apache-hadoop-on-premises-migration-best-practices-architecture.md)
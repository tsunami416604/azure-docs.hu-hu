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
ms.openlocfilehash: f899c2fb871ee528219bd48e94de62746626447f
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416120"
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

## <a name="gathering-details-to-prepare-for-a-migration"></a>Az áttelepítés előkészítése részletek gyűjtése

Ez a szakasz tartalmazza a sablon kérdőíveket kapcsolatos fontos információk összegyűjtése segítségével:

- A helyszíni üzembe helyezés
- Projekt részletei
- Azure-követelmények

### <a name="on-premises-deployment-questionnaire"></a>A helyszíni üzembe helyezés kérdőív

| **Kérdés** | **Példa** | **Válasz** |
|---|---|---|
|**A témakör**: **környezet**|||
|Fürt terjesztési típusa|A Hortonworks, Cloudera, MapR| |
|Fürt terjesztési verziója|HDP 2.6.5, A CDH 5.7.|
|Big Data-ökoszisztéma összetevőket|HDFS, Yarn, Hive, LLAP, Impala, Kudu, HBase, Spark, a MapReduce, a Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, sólyom, Zeppelin, R|
|Fürttípusok|Hadoop, Spark, összefüggő Kafka, Storm, a Solr|
|Fürtök száma|4|
|Fő csomópontok száma|2|
|A feldolgozó csomópontok száma|100|
|Edge-csomópontok száma| 5|
|Teljes lemezterület|100 TB|
|Fő csomópont-konfiguráció|m/y, processzor, lemez, stb.|
|Csomópont-konfigurációt|m/y, processzor, lemez, stb.|
|Peremhálózati csomópont-konfiguráció|m/y, processzor, lemez, stb.|
|HDFS-titkosítás?|Igen|
|Magas rendelkezésre állás|HDFS magas rendelkezésre ÁLLÁSÚ, magas rendelkezésre ÁLLÁSÚ Metaadattár|
|Vész-helyreállítási / biztonsági mentése|Biztonsági mentési fürt?|  
|Fürt függő rendszerek|Az SQL Server, Teradata, Power bi-ban, a mongodb-hez|
|Külső integráció|A tableau, a GridGain, Qubole, az Informatica, Splunk|
|**A témakör**: **biztonsági**|||
|Szegélyhálózat-alapú biztonság|Tűzfalak|
|Fürt-hitelesítés és engedélyezés|Az Active Directory, az Ambari, Cloudera kezelő, hitelesítés nélkül|
|HDFS hozzáférés-vezérlés|  Kézi, ssh felhasználók|
|Hive-hitelesítés és engedélyezés|SENTRY, az LDAP, Kerberos, Ranger AD|
|Naplózás|Ambari, a Cloudera-kezelő, a Ranger|
|Figyelés|Grafit, összegyűjtött, statsd, Telegraf, InfluxDB|
|Riasztások kezelése|Kapacitor, Prometheus, Datadoggal|
|Adatok megőrzési időtartama| három év, 5 év|
|Fürt-rendszergazdák|Egyetlen rendszergazdája, több rendszergazda|

### <a name="project-details-questionnaire"></a>Projekt részletei kérdőív

|**Kérdés**|**Példa**|**Válasz**|
|---|---|---|
|**A témakör**: **számítási feladatok és gyakorisága**|||
|MapReduce-feladatok|10 feladatok – naponta kétszer||
|Hive-feladatok|100 feladat – az óránként||
|Spark-feladatok batch|50 feladatok – 15 percenként||
|Spark Streamelési feladatok|5 feladat--át 3 percenként||
|Strukturált Streamelés a feladatok|5 feladat – percenként||
|Gépi Tanulási modell betanítási feladatok|2 feladat – egyszer a hét||
|Programozási nyelvek|Python, Scala, Java||
|Parancsfájlkészítés|Python-rendszerhéj||
|**A témakör**: **adatok**|||
|Adatforrások|Egybesimított fájlokba, Json, a Kafka, a relációsadatbázis-kezelő rendszer||
|Adatkoordinálás|Az Oozie-munkafolyamatok, légmozgás||
|A memória-keresések|Az Apache Ignite, redis Cache||
|Adatcélok|HDFS, RDBMS, Kafka, MPP ||
|**A témakör**: **metaadatai**|||
|Hive-adatbázis típusa|MySQL, Postgres||
|Nem. a Hive-metaadattárak|2||
|Nem. a Hive-táblák|100||
|Nem. Ranger-házirendek|20||
|Nem. az Oozie-munkafolyamatok|100||
|**A témakör**: **skála**|||
|Többek között a replikációs adatok mennyisége|100 TB||
|Napi Adatbetöltési kötet|50 GB||
|Adatok növekedési aránya|évenként 10 %||
|Fürt csomópontjai növekedésének aránya|5 %-os, évenként
|**A témakör**: **fürt kihasználtsága**|||
|Átlagos CPU-% foglalt|60%||
|Átlagos memória %-át használja|75 %-os||
|Használt lemezterület|75 %-os||
|Átlagos hálózati % foglalt|25%
|**A témakör**: **személyzet**|||
|Nem. a rendszergazdák|2||
|Nem. a fejlesztők számára|10||
|Nem. a végfelhasználók számára|100||
|Képességek|Hadoop, Spark||
|Nem. rendelkezésre álló erőforrások áttelepítése munkája alapján|2||
|**A témakör**: **korlátozások**|||
|Aktuális korlátozások|Túl magas a késés||
|Aktuális kihívásai|Egyidejűségi hiba||

### <a name="azure-requirements-questionnaire"></a>Azure-követelmények kérdőív

|**A témakör**: **infrastruktúra** |||
|---|---|---|
|**Kérdés**|**Példa**|**Válasz**|
| Elsődleges régió|USA keleti régiója||
|Virtuális hálózatok közötti előnyben részesített?|Igen||
|Magas rendelkezésre ÁLLÁS / Vészhelyreállítás szükség?|Igen||
|Integráció más felhőalapú szolgáltatásokkal?|ADF, CosmosDB||
|**A témakör**: **Adatáthelyezés**  |||
|Kezdeti betöltési elsőbbségi|A DistCp, a Data box, ADF, WANDisco||
|Adatok átvitele a különbözeti|A DistCp AzCopy||
|Folyamatos növekményes adatátvitel|A DistCp, a sqoop használatával||
|**A témakör**: **Monitorozás és riasztások** |||
|Azure figyelési és riasztási Vs integrálása külső figyelést|Az Azure Monitorozás és riasztások||
|**A témakör**: **biztonsági beállítások** |||
|A magán- és védett adatfolyamat?|Igen||
|Tartományhoz csatlakoztatott fürtöt (ESPP)?|     Igen||
|A helyszíni AD-Szinkronizáló a felhőbe?|     Igen||
|Nem. az AD-felhasználók szinkronizálása?|          100||
|OK szinkronizálja a jelszavakat a felhőbe?|    Igen||
|Csak felhőbeli felhasználók?|                 Igen||
|A többtényezős hitelesítés szükséges?|                       Nem|| 
|Adatok engedélyezési követelményei?|  Igen||
|Szerepköralapú hozzáférés-vezérlés?|        Igen||
|Naplózás szükséges?|                  Igen||
|Adattitkosítás inaktív?|          Igen||
|Adattitkosítás átvitel közben?|       Igen||
|**A témakör**: **Re-architektúra beállítások** |||
|Egyetlen fürthöz a vs adott fürttípusokat|Meghatározott fürttípusokat||
|Közösen elhelyezett vagy távoli tárolást?|Távtároló||
|Kisebb fürt méretét, az adat távolról?|Kisebb fürt mérete||
|Egy nagy fürt, hanem több kisebb fürtöt használ?|Több kisebb fürtökkel||
|Egy távoli metaadattár használni?|Igen||
|Különböző fürtök között megosztást metaadattárakat?|Igen||
|Számítási feladatok deconstruct?|Cserélje le a Hive-feladatok a Spark-feladatok||
|Adatok előkészítése az ADF használatával?|Nem||
|HDInsight vs Hortonworks Data Platform az IaaS?|HDInsight||

## <a name="next-steps"></a>További lépések

Olvassa el az oktatóanyag-sorozatban a következő cikkben:

- [A helyszíni Azure HDInsight Hadoop-áttelepítési architektúra ajánlott eljárásai](apache-hadoop-on-premises-migration-best-practices-architecture.md)
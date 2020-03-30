---
title: 'Előnyök: Áttelepítés a helyszíni Apache Hadoop áttelepíteni az Azure HDInsight'
description: Ismerje meg a helyszíni Hadoop-fürtök Azure HDInsightba való áttelepítésének motivációját és előnyeit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 2440b93629416ea73fcf211cbe7bf5a3b72ab2e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267322"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Telepítse a helyszíni Apache Hadoop-fürtöket az Azure HDInsightba – motiváció és előnyök

Ez a cikk az első a helyszíni Apache Hadoop eco-system telepítések Azure HDInsightba való áttelepítésével kapcsolatos gyakorlati tanácsokról szóló sorozatban. Ez a cikksorozat azoknak szól, akik felelősek az Apache Hadoop-megoldások tervezéséért, üzembe helyezéséért és áttelepítésért az Azure HDInsightban. A szerepkörök, amelyek ezek a cikkek előnyeit felhőalapú építészek, Hadoop rendszergazdák és DevOps-mérnökök. A szoftverfejlesztőknek, adatmérnököknek és adatszakértőknek is ki kell használják a különböző típusú fürtök felhőbeli működésének magyarázatát.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Miért érdemes áttérni az Azure HDInsightra?

Az Azure HDInsight a Hadoop-összetevők felhőalapú elosztása. Az Azure HDInsight segítségével könnyen, gyorsan és költséghatékonyan dolgozható fel nagy mennyiségű adat. A HDInsight a legnépszerűbb nyílt forráskódú keretrendszereket tartalmazza, például:

- Apache Hadoop
- Apache Spark
- Apache Hive LLAP-mal
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Az Azure HDInsight előnyei a helyszíni Hadoopkal szemben

- **Alacsony költség** – A költségek csökkenthetők, ha [igény szerinti fürtöket hoz létre,](../hdinsight-hadoop-create-linux-clusters-adf.md) és csak a használt ért fizet. A leválasztott számítási és tárolási szolgáltatás rugalmasságot biztosít azáltal, hogy az adatkötetet a fürt méretétől függetlenül tartja.

- **Automatikus fürtlétrehozása** – Az automatikus fürtlétrehozása minimális telepítést és konfigurálást igényel. Az automatizálás igény szerinti fürtökhöz használható.

- **Felügyelt hardver és konfiguráció** – Nem kell aggódnia a HDInsight-fürttel rendelkező fizikai hardver vagy infrastruktúra miatt. Csak adja meg a fürt konfigurációját, és az Azure beállítja azt.

- **Könnyen méretezhető** – a HDInsight lehetővé teszi a számítási feladatok fel- és [leskálázásához.](../hdinsight-administer-use-portal-linux.md) Az Azure gondoskodik az adatok újraelosztásáról és a számítási feladatok újraelosztásáról az adatfeldolgozási feladatok megszakítása nélkül.

- **Globális elérhetőség** – A HDInsight több [régióban](https://azure.microsoft.com/regions/services/) érhető el, mint bármely más big data-elemzési ajánlat. Az Azure HDInsight elérhető az Azure Governmentben, Kínában, és Németországban is, így megfelelhet a vállalati igényeknek a főbb szuverén területeken.

- **Biztonságos és megfelelő** – A HDInsight lehetővé teszi a vállalati adateszközök védelmét az [Azure Virtuális Hálózattal,](../hdinsight-plan-virtual-network-deployment.md) [a titkosítással](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)és az [Azure Active Directoryval](../domain-joined/hdinsight-security-overview.md)való integrációval. A HDInsight megfelel a legnépszerűbb iparági és kormányzati [megfelelőségi szabványoknak](https://azure.microsoft.com/overview/trusted-cloud)is.

- **Egyszerűsített verziókezelés** – Az Azure HDInsight kezeli a Hadoop ökoszisztémás rendszer összetevőinek verzióját, és naprakészen tartja azokat. A szoftverfrissítések általában összetett folyamat a helyszíni központi telepítések esetében.

- **Kisebb fürtök optimalizált adott számítási feladatok kevesebb függőségek között az összetevők** – Egy tipikus helyszíni Hadoop telepítő használ egyetlen fürt, amely számos célt szolgál. Az Azure HDInsight segítségével számítási feladatokra vonatkozó fürtök hozhatók létre. Fürtök létrehozása adott számítási feladatok hoz létre megszünteti a bonyolultsága fenntartása egyetlen fürt egyre összetettebb.

- **Termelékenység** – A Hadoop és a Spark különböző eszközeit használhatja az előnyben részesített fejlesztői környezetben.

- **Bővíthetőség egyéni eszközökkel vagy külső alkalmazásokkal** – a HDInsight-fürtök telepíthető összetevőkkel bővíthetők, és az Azure Market-helyről származó egyetlen kattintásos telepítésekkel is integrálhatók a többi big [data-megoldással.](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)

- **Egyszerű felügyelet, felügyelet és figyelés** – Az Azure HDInsight integrálható [az Azure Monitor naplóival,](../hdinsight-hadoop-oms-log-analytics-tutorial.md) hogy egyetlen felületet biztosítson, amellyel az összes fürtje figyelheti.

- **Integráció más Azure-szolgáltatásokkal** – A HDInsight könnyen integrálható más népszerű Azure-szolgáltatásokkal, például a következőkkel:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - 2. generációs Azure Data Lake Storage
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Öngyógyító folyamatok és összetevők** – A HDInsight folyamatosan ellenőrzi az infrastruktúrát és a nyílt forráskódú összetevőket a saját felügyeleti infrastruktúrájával. Emellett automatikusan helyreállítja a kritikus hibákat, például a nyílt forráskódú összetevők és csomópontok elérhetetlenségét. Riasztások aktiválódnak ambari, ha bármely OSS-összetevő sikertelen.

További információt a [Mi az Azure HDInsight és az Apache Hadoop technológiai verem](../hadoop/apache-hadoop-introduction.md)című cikkben talál.

## <a name="migration-planning-process"></a>Áttelepítési tervezési folyamat

A következő lépések a helyszíni Hadoop-fürtök Azure HDInsightba való áttelepítésének megtervezéséhez ajánlottak:

1. Ismerje meg az aktuális helyszíni üzembe helyezést és topológiákat.
2. Ismerje meg az aktuális projekthatókört, ütemterveket és csapatszakértelmet.
3. Ismerje meg az Azure követelményeit.
4. A bevált gyakorlatok alapján részletes tervet készíthet.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Részletek összegyűjtése az áttelepítésre való felkészüléshez

Ez a szakasz sablonkérdőíveket tartalmaz, amelyek segítenek fontos információkat gyűjteni a következőkről:

- A helyszíni telepítés
- Projekt részletei
- Azure-követelmények

### <a name="on-premises-deployment-questionnaire"></a>Helyszíni központi telepítési kérdőív

| **Kérdés** | **Példa** | **Válasz** |
|---|---|---|
|**Téma**: **Környezet**|||
|Fürtterjesztési verzió|HDP 2.6.5, CDH 5.7|
|Big Data ökoszisztémás rendszer összetevői|HDFS, Fonal, Hive, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Sólyom, Zeppelin, R|
|Fürttípusok|Hadoop, Spark, Confluent Kafka, Vihar, Solr|
|Fürtök száma|4|
|Fő csomópontok száma|2|
|Dolgozó csomópontok száma|100|
|Peremcsomópontok száma| 5|
|Teljes lemezterület|100 TB|
|Fő csomópont konfigurációja|m / y, cpu, lemez, stb|
|Adatcsomópontok konfigurációja|m / y, cpu, lemez, stb|
|Peremcsomók konfigurációja|m / y, cpu, lemez, stb|
|HDFS titkosítás?|Igen|
|Magas rendelkezésre állás|HDFS HA, Metastore HA|
|Katasztrófa utáni helyreállítás / Biztonsági mentés|Tartalék fürt?|  
|Fürttől függő rendszerek|SQL Server, Teradata, Power BI, MongoDB|
|Harmadik féltől származó integrációk|Tabló, GridGain, Qubole, Informatica, Splunk|
|**Téma**: **Biztonság**|||
|Kerületi biztonság|Tűzfalak|
|Fürthitelesítés & engedélyezés|Active Directory, Ambari, Cloudera Manager, Nincs hitelesítés|
|HDFS hozzáférés-vezérlés|  Kézi, ssh felhasználók|
|Hive-hitelesítés & engedélyezés|Őrszem, LDAP, AD kerberos, Ranger|
|Naplózás|Ambari, Cloudera Navigator, Ranger|
|Figyelés|Grafit, gyűjtött, statsd, Telegraf, InfluxDB|
|Riasztások kezelése|Kapacitor, Prometheus, Adatkutya|
|Adatmegőrzés időtartama| 3 év, 5 év|
|Fürtrendszergazdák|Egyetlen rendszergazda, több rendszergazda|

### <a name="project-details-questionnaire"></a>Projekt részletei kérdőív

|**Kérdés**|**Példa**|**Válasz**|
|---|---|---|
|**Téma**: **Munkaterhelések és gyakoriság**|||
|MapReduce feladatok|10 állás - naponta kétszer||
|Hive-feladatok|100 munkahely - óránként||
|Szikra kötegelt feladatok|50 munkahely - 15 percenként||
|Spark Streaming feladatok|5 állás - 3 percenként||
|Strukturált streamelési feladatok|5 munkahely - percenként||
|ML modell képzési feladatok|2 állás - hetente egyszer||
|Programozási nyelvek|Python, Scala, Jáva||
|Parancsprogramok használata|Rendszerhéj, Python||
|**Téma**: **Adatok**|||
|Adatforrások|Lapos fájlok, Json, Kafka, RDBMS||
|Adatvezénylés|Oozie munkafolyamatok, Légáramlás||
|A memóriakeresések|Apache Ignite, Redis||
|Adatcélok|HDFS, RDBMS, Kafka, MPP ||
|**Téma**: **Meta adatok**|||
|Hive DB típusa|Mysql, Postgres||
|Hive metaraktárak száma|2||
|Hive-táblák száma|100||
|Ranger-házirendek száma|20||
|Oozie-munkafolyamatok száma|100||
|**Téma**: **Méretezés**|||
|Adatmennyiség, beleértve a replikációt|100 TB||
|Napi lenyelési mennyiség|50 GB||
|Az adatok növekedési üteme|10% évente||
|Fürtcsomópontok növekedési üteme|5% évente
|**Téma**: **Fürthasználat**|||
|Átlagos felhasznált CPU%|60%||
|Átlagos felhasznált memória%|75%||
|Felhasznált lemezterület|75%||
|Átlagos használt hálózati %|25%
|**Téma**: **Személyzet**|||
|Rendszergazdák száma|2||
|Fejlesztők száma|10||
|Végfelhasználók száma|100||
|Készségek|Hadoop, Szikra||
|A migrációs erőfeszítésekhez rendelkezésre álló erőforrások száma|2||
|**Téma**: **Korlátozások**|||
|Aktuális korlátozások|A késleltetés magas||
|Jelenlegi kihívások|Egyidejűségi probléma||

### <a name="azure-requirements-questionnaire"></a>Azure-követelmények kérdőíve

|**Téma**: **Infrastruktúra** |||
|---|---|---|
|**Kérdés**|**Példa**|**Válasz**|
| Előnyben részesített régió|USA keleti régiója||
|VNet preferált?|Igen||
|HA / DR szükséges?|Igen||
|Integráció más felhőszolgáltatásokkal?|ADF, KozmoszDB||
|**Téma**: **Adatmozgás**  |||
|Kezdeti terhelési beállítás|DistCp, Adat doboz, ADF, WANDisco||
|Adatátviteli delta|DistCp, AzCopy||
|Folyamatos növekményes adatátvitel|DistCp, Sqoop||
|**Téma**: **Monitoring & Alerting** |||
|Az Azure Monitoring & alerting vs harmadik fél figyelésének integrálása|Az Azure Monitoring & riasztás használata||
|**Téma**: **Biztonsági beállítások** |||
|Privát és védett adatfolyamat?|Igen||
|Tartományhoz csatlakozott fürt (ESP)?|     Igen||
|Helyszíni AD szinkronizálás a felhővel?|     Igen||
|A szinkronizálandó AD-felhasználók száma?|          100||
|Rendben van a jelszavak felhőbe való szinkronizálása?|    Igen||
|Csak felhőalapú felhasználók?|                 Igen||
|MFA szükséges?|                       Nem|| 
|Adatengedélyezési követelmények?|  Igen||
|Szerepköralapú hozzáférés-vezérlés?|        Igen||
|Naplózásszükséges?|                  Igen||
|Adattitkosítás inaktív helyen?|          Igen||
|Adattitkosítás átvitel közben?|       Igen||
|**Téma**: **Re-Architecture beállítások** |||
|Egyfürt vs adott fürttípusok|Adott fürttípusok||
|Közös enyv tároló vs távtároló?|Távtároló||
|Kisebb a fürtméret az adatok távoli tárolásaként?|Kisebb fürtméret||
|Egyetlen nagy fürt helyett több kisebb fürtöt használ?|Több kisebb fürt használata||
|Távoli metaboltot használ?|Igen||
|Megosztja a metaboltokat a különböző fürtök között?|Igen||
|Deconstruct számítási feladatok?|Hive-feladatok lecserélése Spark-feladatokkal||
|Használja az ADF-et az adatvezényléshez?|Nem||

## <a name="next-steps"></a>További lépések

Olvassa el a sorozat következő cikkét:

- [A helyszíni architektúra ajánlott eljárások az Azure HDInsight Hadoop-áttelepítéshez](apache-hadoop-on-premises-migration-best-practices-architecture.md)

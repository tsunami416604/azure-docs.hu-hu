---
title: Azure HDInsight – biztonság és a fejlesztési és üzemeltetési eljárások helyszíni Apache Hadoop-fürtök áttelepítése
description: Ismerje meg, biztonsági és a fejlesztési és üzemeltetési áttelepítése a helyszíni Hadoop-fürtöket az Azure HDInsight ajánlott eljárásait.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 1a1cf731678ef7678b740020a4d61725f9a2b32a
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50221903"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Azure HDInsight – biztonság és a fejlesztési és üzemeltetési eljárások helyszíni Apache Hadoop-fürtök áttelepítése

Ez a cikk az Azure HDInsight rendszerben javaslatok a biztonsági és a fejlesztési és üzemeltetési biztosít. Ez azt egy olyan sorozat részét, amely ajánlott eljárásokat, amelyek segítik az Azure HDInsight áttelepítése a helyszíni Apache Hadoop-rendszerekhez biztosít.

## <a name="use-the-enterprise-security-package-to-secure-and-govern-the-cluster"></a>A vállalati biztonsági csomag segítségével biztonságos, és szabályozhatja a fürt

A vállalati biztonsági csomag (ESP) támogatja az Active Directory-alapú hitelesítés, a több felhasználó támogatása és a szerepköralapú hozzáférés-vezérlés. A választott ESP beállítással a HDInsight-fürt az Active Directory-tartományhoz csatlakozik, és a vállalati rendszergazda konfigurálhatja a szerepköralapú hozzáférés-vezérlés (RBAC) Hive-biztonsághoz az Apache Ranger segítségével. A rendszergazda is naplózhatja az alkalmazottak és bármely változtatást a hozzáférés-vezérlési házirendeket adatelérési.

ESP funkciókat jelenleg előzetes verzióban és csak a következő fürttípusokat elérhető: az Apache Hadoop, az Apache Spark, az Apache HBase, Apache Kafka és az Apache interaktív lekérdezés.

Használja az alábbi lépéseket a tartományhoz csatlakoztatott HDInsight-fürt üzembe helyezéséhez:

- Üzembe helyezése az Azure Active Directory (AAD) a tartománynév átadásával
- Az Azure Active Directory Domain Services (AAD-DS) telepítése
- A szükséges virtuális hálózat és alhálózat létrehozása
- AAD DS kezelheti a virtuális hálózat egy virtuális gép üzembe helyezése
- A virtuális gép csatlakoztatása a tartományhoz
- Telepítse az AD és a DNS-eszközök
- Kérje meg az AAD Tartományi rendszergazdát, hozzon létre egy szervezeti egység (OU)
- LDAPS-t engedélyezése AAD Tartományi
- Hozzon létre egy szolgáltatásfiókot az Azure Active Directoryban a delegált olvasási és írási rendszergazdai engedéllyel a szervezeti Egységhez való, hogy képes kezelni. Ez a szolgáltatás fiók ezután gépek csatlakoztatása a tartományhoz, és helyezze el a gép rendszerbiztonsági tagok belül a szervezeti Egységhez. Azt is létrehozhat egyszerű szolgáltatást lekéri a szervezeti Egységet a fürt létrehozásakor megadott.

    > [!Note]
    > A szolgáltatásfiókot kell lennie az AD tartományi rendszergazdai fiók

- Az alábbi paraméterek beállításával a HDInsight ESP-fürt üzembe helyezése:
    - **Tartománynév**: A tartomány nevét, amely az Azure Active Directory tartományi szolgáltatások van társítva.
    - **Tartományi felhasználónév**: A fiók, amelyet az előző szakaszban, például az Azure AD DS-tartományvezérlő által felügyelt tartományban: `hdiadmin@contoso.onmicrosoft.com`. A tartományi felhasználó a rendszergazda a HDInsight-fürt lesz.
    - **Tartományi jelszó**: a szolgáltatásfiók jelszava.
    - **Szervezeti egység**:, például a HDInsight-fürt a használni kívánt szervezeti egység megkülönböztető nevére: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Ha a szervezeti egység nem létezik, a HDInsight-fürt próbál létrehozni a szervezeti Egységet a jogosultságokat a szolgáltatási fiók használatával.
    - **LDAPS URL-cím**: például `ldaps://contoso.onmicrosoft.com:636`.
    - **Hozzáférési felhasználói csoport**: A biztonsági csoportok, amelynek felhasználói szeretné szinkronizálni a fürthöz, például: `HiveUsers`. Ha meg szeretné határozni a több felhasználói csoportot, válassza el őket pontosvesszővel (;). A csoport a címtárban az ESP-fürt létrehozása előtt léteznie kell.

További információkért tekintse át a következő cikkeket:

- [Hadoop-biztonság használatába a tartományhoz csatlakoztatott HDInsight-fürtök bemutatása](../domain-joined/apache-domain-joined-introduction.md)
- [Az Azure tartományhoz csatlakoztatott Hadoop-fürtök tervezése a HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [A tartományhoz csatlakoztatott HDInsight-fürt konfigurálása Azure Active Directory Domain Services használatával](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Egy HDInsight-fürtön az Azure Active Directory-felhasználók szinkronizálása](../hdinsight-sync-aad-users-to-cluster.md)
- [A tartományhoz csatlakoztatott HDInsight Hive-házirendek konfigurálása](../domain-joined/apache-domain-joined-run-hive.md)
- [Az Apache Oozie futtathat a tartományhoz csatlakoztatott HDInsight Hadoop-fürtöket](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security-management"></a>Teljes körű vállalati biztonsági felügyeleti megvalósítása

A végpontok közötti vállalati biztonsági hajthatók végre a következő vezérlőknek a használatát:

- **A magán- és védett adatfolyamat** (szegélyhálózat-alapú biztonság):
    - Szegélyhálózat-alapú szintű biztonsági elérhető Azure-beli virtuális hálózatok, hálózati biztonsági csoportok és az átjárószolgáltatás

- **Hitelesítés és engedélyezés az adatok eléréséhez**
    - Az Azure Active Directory Domain Services használatával a tartományhoz HDI-fürt létrehozása. (A vállalati biztonsági csomag)
    - Adja meg a fürt erőforrásai szerepkörön alapuló hozzáférés AD-felhasználók az Ambari használatával
    - Az Apache Ranger segítségével értékre beállított hozzáférési Alkalmazásvezérlési házirendeket a Hive a tábla vagy oszlop / sorszinthez.
    - A fürthöz SSH-hozzáférés csak a rendszergazda korlátozható.

- **Naplózás**
    - Megtekintheti és jelentheti az összes hozzáférést a HDInsight-fürt erőforrásainak és az adatokat.
    - Megtekintheti és jelentheti a hozzáférés-vezérlési házirendeket minden módosítás

- **Titkosítás**
    - Transzparens kiszolgálóoldali titkosítás, a Microsoft által kezelt kulcsok vagy felhasználó által kezelt kulcsok használata.
    - Az átvitel során titkosítás ügyféloldali titkosítás, a https és a TLS használatával

További információkért tekintse át a következő cikkeket:

- [Az Azure virtuális hálózatok áttekintése](../../virtual-network/virtual-networks-overview.md)
- [Az Azure hálózati biztonsági csoportok áttekintése](../../virtual-network/security-overview.md)
- [Az Azure virtuális hálózatok közötti társviszony](../../virtual-network/virtual-network-peering-overview.md)
- [Az Azure storage biztonsági útmutatóját](../../storage/common/storage-security-guide.md)
- [Az Azure Storage Service Encryption inaktív](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Használja a monitorozás és riasztások

További információkért tekintse meg a cikket:

[Azure Monitor – áttekintés](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Fürtök frissítése

A HDInsight legújabb a legújabb funkciók előnyeinek kihasználása érdekében rendszeresen frissíteni. A következő lépések segítségével a fürt frissítése a legújabb verzióra:

- Hozzon létre egy új tesztelési HDI-fürt használata a legújabb elérhető HDI-verziója.
- Tesztelje az új fürtön, győződjön meg arról, hogy a feladatok és a számítási feladatok a várt módon működik.
- Feladatok vagy alkalmazások vagy munkaterhelések szükség szerint módosítsa.
- Készítsen biztonsági másolatot a fürtcsomópontokon helyileg tárolt átmeneti adatok.
- Törölje a meglévő fürtből.
- A legújabb verziójú HDInsight-fürt létrehozása a ugyanazon virtuális hálózat alhálózat, mint az előző fürtben alapértelmezett adat- és metaadat-tároló használatával.
- Átmeneti az adatok, amelyek készült biztonsági másolat importálása.
- Indítási feladatok/folytatni a használatával az új fürtön.

További információkért tekintse meg a cikket: [új verzióra frissítése HDInsight-fürt](../hdinsight-upgrade-cluster.md)

## <a name="patch-cluster-operating-systems"></a>Javítás az operációs rendszerek fürtbe

Felügyelt Hadoop-szolgáltatás HDInsight gondoskodik az operációs rendszer, a HDInsight-fürtök által használt virtuális gépek javítása.

További információkért tekintse meg a cikket: [operációs rendszer javításai a HDInsight](../hdinsight-os-patching.md)

## <a name="post-migration"></a>Áttelepítés utáni

1. **Javíthatja az alkalmazások** – iteratív végezze el a szükséges módosításokat a feladatok, folyamatok és parancsprogramok
2. **Tesztek végrehajtása** – iteratív működési Futtatás és a teljesítmény tesztek
3. **Optimalizálja** - teljesítmény problémák elhárítása a fenti teszt eredményei alapján, és erősítse meg a teljesítménnyel kapcsolatos fejlesztések, majd tesztelje újra.

## <a name="appendix-gathering-details-to-prepare-for-a-migration"></a>A függelék: adatgyűjtési részleteit az áttelepítés előkészítése

Ez a szakasz tartalmazza a sablon kérdőíveket kapcsolatos fontos információk összegyűjtése segítségével:

- A helyszíni telepítéshez.
- Projekt részletei.
- Azure-követelményeknek.

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
|HDI vs HDP IaaS-on?|HDI||

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction)
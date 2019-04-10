---
title: Az Azure HDInsight-fürtökhöz való használatra tárolási lehetőségek összehasonlítása
description: A tárolási típusok és azok működéséről az Azure HDInsight áttekintése.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: ac1a0e4eadc0b84fdd2a170c2e0f6e0a2f2af3a4
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361777"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtökhöz való használatra tárolási lehetőségek összehasonlítása

Több különböző Azure storage-szolgáltatások HDInsight-fürtök létrehozásakor választhat:

* Azure Storage
* 2. generációs Azure Data Lake Storage
* 1. generációs Azure Data Lake Storage

Ez a cikk ezek tárolási típusok és a hozzájuk tartozó egyedi funkciók áttekintést nyújt.

A következő táblázat összefoglalja az Azure Storage szolgáltatás, amely a különböző HDInsight-verziók támogatottak:

| Társzolgáltatás | Fióktípus | Namespace típusa | Támogatott szolgáltatások | Támogatott teljesítményszintek | Támogatott elérési szint | HDInsight Version (HDInsight-verzió) | Fürttípus |
|---|---|---|---|---|---|---|---|
|2. generációs Azure Data Lake Storage| Általános célú V2 | Hierarchikus (fájlrendszer) | Blob | Standard | Gyakori, ritka elérésű, archív tárolási szint | 3.6. + | Összes |
|Azure Storage| Általános célú V2 | Objektum | Blob | Standard | Gyakori, ritka elérésű, archív tárolási szint | 3.6. + | Összes |
|Azure Storage| Általános célú V1 | Objektum | Blob | Standard | – | Összes | Összes |
|Azure Storage| Blob Storage | Objektum | Blob | Standard | Gyakori, ritka elérésű, archív tárolási szint | Összes | Összes |
|1. generációs Azure Data Lake Storage| – | Hierarchikus (fájlrendszer) | – | N/A | – | Csak 3.6. | A HBase kivételével |

Az Azure Storage elérési szint további információkért lásd: [Azure Blob storage: Prémium (előzetes verzió), a gyors Elérésűre, ritka elérésű és archív tárolási szintek](../storage/blobs/storage-blob-storage-tiers.md)

Létrehozhat egy fürtöt, az elsődleges és a nem kötelező másodlagos storage szolgáltatás különböző kombinációjával. A következő táblázat összefoglalja a fürt a HDInsight által jelenleg támogatott tárolási konfigurációk:

| HDInsight Version (HDInsight-verzió) | Elsődleges tároló | A másodlagos tárhelyen | Támogatott |
|---|---|---|---|
| 3.6 & 4.0 | Standard Blob | Standard Blob | Igen |
| 3.6 & 4.0 | Standard Blob | 2. generációs Data Lake Storage | Nem |
| 3.6 & 4.0 | Standard Blob | 1. generációs Data Lake Storage | Igen |
| 3.6 & 4.0 | Data Lake Storage Gen2 * | 2. generációs Data Lake Storage | Igen |
| 3.6 & 4.0 | Data Lake Storage Gen2 * | Standard Blob | Igen |
| 3.6 & 4.0 | 2. generációs Data Lake Storage | 1. generációs Data Lake Storage | Nem |
| 3.6 | 1. generációs Data Lake Storage | 1. generációs Data Lake Storage | Igen |
| 3.6 | 1. generációs Data Lake Storage | Standard Blob | Igen |
| 3.6 | 1. generációs Data Lake Storage | 2. generációs Data Lake Storage | Nem |
| 4.0 | 1. generációs Data Lake Storage | Bármelyik | Nem |

* = Ez lehet egy vagy több Data Lake Storage Gen2-fiókok esetében, mindaddig, amíg azok az összes beállítás az azonos felügyelt identitás fürt elérésére használhat.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Az Azure Data Lake Storage Gen2 használata az Azure HDInsight az Apache Hadoop-keretrendszerrel

Az Azure Data Lake Storage Gen2 idő szükséges alapvető funkcióit, az Azure Data Lake Storage Gen1, és egyesíti azokat az Azure Blob storage-bA. Ilyen például egy fájlrendszer, amely kompatibilis a Hadoop-, Azure Active Directory (Azure AD), és a POSIX-alapú hozzáférés-vezérlési listák (ACL). Ez a kombináció kihasználhatja az Azure Data Lake Storage Gen1 teljesítményét a rétegezést és az adatok életciklus-felügyelet a Blob Storage használatát teszi lehetővé.

További információ az Azure Data Lake Storage Gen2: [Bevezetés az Azure Data Lake Storage Gen2-re](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 fő funkciói

* **Kompatibilis a Hadoop-hozzáférés:** Az Azure Data Lake Storage Gen2 kezelheti és adatok eléréséhez, ugyanúgy, mint az a Hadoop elosztott fájlrendszer (HDFS). Minden Apache Hadoop-környezetekben, beleértve az Azure HDInsight és az Azure Databricks az Azure Blob File System (ABFS) illesztőprogram érhető el. A Data Lake Storage Gen2 adataihoz hozzáférést ABFS használatával.

* **Felülbírálja a POSIX-engedélyek:** A Data Lake Gen2 biztonsági modell támogatja az ACL-JEI és POSIX engedélyekkel együtt néhány további pontossággal megadott Data Lake Storage Gen2-re. Beállítások felügyeleti eszközöket és keretrendszereket, mint például az Apache Hive és az Apache Spark keresztül konfigurálhatók.

* **Költséghatékonyság:** Data Lake Storage Gen2 költséghatékony tárolási kapacitás és a tranzakciók érhetők el. Szolgáltatások, például az Azure Blob storage életciklus alacsonyabb költségek módosításával díjszabása nem módosul, az adatok életciklusa továbbítanak.

* **Kompatibilitás a Blob storage-eszközökkel, keretrendszerek és alkalmazások:** Data Lake Storage Gen2 továbbra is az eszközöket, keretrendszerek és alkalmazások a Blob Storage széles választékának működik.

* **Optimalizált illesztőprogram:** A ABFS illesztőprogram kifejezetten a big data-elemzés optimalizáltuk. Az elosztott fájlrendszerbeli (DFS) végpont, a megfelelő REST API-k végzetesnek dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Az Azure Data Lake Storage általános 2 újdonságai

#### <a name="managed-identities-for-secure-file-access"></a>Felügyelt identitások számára biztonságos hozzáférést

Az Azure HDInsight fürt védelméhez az Azure Data Lake Storage Gen2 fájlok felügyelt identitásokat használ. Felügyelt identitások érhetők el az Azure Active Directory által biztosított Azure-szolgáltatások automatikusan kezelt hitelesítő adatok vannak beállítva. Ezekkel a hitelesítő adatokkal történő hitelesítéshez minden olyan szolgáltatás, amely támogatja az Active Directory-hitelesítés használható. Felügyelt identitások használatával nincs szükség, hogy kódírásra vagy konfigurálásra fájlban tárolhatja a hitelesítő adatokat.

További információkért lásd: [felügyelt identitások az Azure-erőforrások](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Az Azure Blob fájlrendszer illesztőprogramja

Az Apache Hadoop-alkalmazások natív módon várhatóan adatokat olvasnak és írnak a helyi lemezes tárterületet. Olyan Hadoop fájlrendszer illesztőprogramja ABFS például lehetővé teszi a Hadoop-alkalmazások által működni a Hadoop-fájl rendszerműveletekről rendszeres emulálni a felhőalapú tárolással működjön. Az illesztőprogram alakítja át azokat a parancsokat, amely a tényleges felhőalapú tárolási platform együttműködik a műveletekkel alkalmazástól kapott.

Korábban a Hadoop fájlrendszer illesztőprogramja összes rendszer fájlműveletek alakítani az ügyféloldalon Azure Storage REST API-hívások, és meghív a REST API-t. Az ügyféloldali átalakítás, azonban több REST API eredményezett meghívja a rendszer egyetlen fájl művelet például egy fájl átnevezése. ABFS át lett helyezve a Hadoop-fájl rendszer logikai némelyike az ügyféloldalról a kiszolgálói oldalon. Az Azure Data Lake Storage Gen2 API most a Blob API-val párhuzamosan fog futni. Az áttelepítés javítja a teljesítményt, mert most már általános Hadoop fájlrendszerműveletek hajtható végre egy REST API-hívással.

További információkért lásd: [az Azure Blob fájlrendszer illesztőprogram (ABFS): Egy dedikált Azure Storage-illesztőprogram hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Az Azure Data Lake Storage általános 2 URI-séma 

Az Azure Data Lake Storage Gen2 fájlok elérése az Azure Storage a HDInsight egy új URI-séma használja:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Az URI-séma SSL titkosított hozzáférést biztosít (`abfss://` előtag) és a nem titkosított hozzáférést (`abfs://` előtag). Használat `abfss` amikor csak lehetséges, akkor is, ha ugyanabban a régióban, az Azure-ban lévő adatokat éri el.

`<FILE_SYSTEM_NAME>` az elérési útját a fájlrendszerben, Data Lake Storage Gen2 azonosítja.

`<ACCOUNT_NAME>` az Azure Storage-fiók neve azonosítja. Szükség van a teljes tartománynévre (FQDN-re).

`<PATH>` a fájl vagy könyvtár HDFS elérési útja neve van.

Ha az értékek `<FILE_SYSTEM_NAME>` és `<ACCOUNT_NAME>` nincs megadva, az alapértelmezett fájlrendszer szolgál. A fájlokat a rendszer az alapértelmezett fájlrendszert a relatív elérési utat vagy abszolút elérési útnak. Ha például a `hadoop-mapreduce-examples.jar` fájlt, amely együttműködik a HDInsight-fürtök a következő útvonalak egyikét használva lehet hivatkozni:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> A Fájlnév `hadoop-examples.jar` HDInsight 2.1-es és 1.6-os verziójának fürtökben. Ha HDInsight-on kívül fájlokkal dolgozik, látni fogja, hogy legtöbb segédprogram nem ismeri fel a ABFS formázhatja, de ehelyett például az egy alapvető elérési út formátumot várhatóan `example/jars/hadoop-mapreduce-examples.jar`.

További információkért lásd: [használata az Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage egy robusztus általános célú tárolómegoldás, amely zökkenőmentesen integrálható a HDInsight szolgáltatás. A HDInsight egy blobtárolót használhat az Azure Storage-ben a fürt alapértelmezett fájlrendszereként. HDFS felületen a HDInsight összetevők teljes készlete működhet közvetlenül a strukturált vagy strukturálatlan adatokon a BLOB.

Javasoljuk, hogy az alapértelmezett fürttárolóhoz és az üzleti adatokat külön storage tárolók használatával elkülönítheti a HDInsight-naplók és a saját üzleti adatok ideiglenes fájlokat. Emellett ajánlott törölni az alapértelmezett blob tároló, amely tartalmazza az alkalmazás és a rendszer naplóit, tárolási költségek csökkentése érdekében minden használat után. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

Ha úgy dönt, hogy a tárfiók biztonságos a **tűzfalak és virtuális hálózatok** korlátozásai **kiválasztott hálózatok**, ügyeljen arra, hogy a kivétel engedélyezése **engedélyezése a Microsoft megbízható szolgáltatások...**  úgy, hogy a HDInsight képes hozzáférni a tárfiókhoz.

### <a name="hdinsight-storage-architecture"></a>HDInsight tároló-architektúra

A következő ábra az Azure Storage a HDInsight-architektúra absztrakt nézetét nyújtja:

![Hogyan Hadoop-fürtök használatával a HDFS API-val érik el és tárolják a strukturált és strukturálatlan adatok Blob storage-ban bemutató ábra. Ez](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "HDInsight tároló-architektúra")

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer a teljes URI használatával érhető el, például:

    hdfs://<namenodehost>/<path>

HDInsight keresztül is elérheti az Azure Storage szolgáltatásban tárolt adatok. A szintaxist a következőképpen történik:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Ha egy Azure Storage-fiók használata a HDInsight-fürtök, vegye figyelembe a következő alapelveket:

* **A storage-fiókokban fürthöz csatlakozó tárolók:** Mivel a fiók neve és kulcsa társítva a fürt létrehozása során, akkor ezeket a tárolókban lévő blobokhoz teljes hozzáféréssel rendelkezik.

* **Nyilvános tárolók vagy nyilvános blobok a storage-fiókok, amelyek *nem* csatlakozik a fürthöz:** Rendelkezik olvasási engedéllyel a tárolókban lévő blobokat.
  
  > [!NOTE]  
  > Nyilvános tárolók engedélyezése a tárolóban elérhető összes BLOB listáját és a tároló metaadatait is lekérheti. A nyilvános blobok esetén csak akkor érheti el a blobokat, ha ismeri a pontos URL-t. További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](../storage/blobs/storage-manage-access-to-resources.md) foglalkozó témakört.

* **A storage-fiókok, amelyek személyes tárolók *nem* csatlakozik a fürthöz:** Nem férhet hozzá a tárolókban lévő blobokat, kivéve, ha a WebHCat-feladatok elküldésekor határozza meg a tárfiókot. 

A létrehozási folyamat során meghatározott tárfiókok és a kulcsaik a %HADOOP_HOME%/conf/core-site.xml fájlban találhatók a fürtcsomópontokon. Alapértelmezés szerint a HDInsight a core-site.xml fájlban meghatározott tárfiókok használja. Ez a beállítás használatával módosíthatja [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Több WebHCat-feladatok, többek között az Apache Hive, MapReduce, az Apache Hadoop-Stream és az Apache Pig, képes továbbítani a storage-fiókok és a velük metaadatok leírását. (Ez a jelenleg igaz a Pig a storage-fiókokkal, de a metaadatok nem.) További információkért lásd: [egy HDInsight-fürtöt használ, alternatív storage-fiókok és metaadattárakat](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

A blobok a strukturált és strukturálatlan adatokhoz használhatók. Blobtárolók kulcs/érték párok, és rendelkezik a könyvtár-hierarchia tárolhatja az adatokat. Azonban a kulcs neve úgy tűnjön, mintha a fájl könyvtárszerkezetben tárolt perjel karaktert (/) tartalmazhatnak. Ha például egy blob kulcsa lehet `input/log1.txt`. Nem tényleges `input` könyvtár létezik, de a törtvonallal a kulcs nevét, mert a kulcs néz ki egy fájl elérési útját.

### <a id="benefits"></a>Az Azure Storage előnyei
Számítási fürtök és tárolási erőforrások, amelyek ugyanarra a szalagra nem rendelkezik implicit teljesítmény költségeket. Ezeket a díjakat a rendszer az, ahogyan a számítási fürtök az Azure-régión belül a tárfiók erőforrásainak közelében való létrehozása problémák elhárításáról. Ebben a régióban a számítási csomópontok hatékonyan hozzáféréssel az adatokat a hálózaton belüli Azure Storage.

Ha az adatok az Azure Storage, HDFS helyett, kap számos előnnyel jár:

* **Adatok újbóli használata és megosztása:** Az adatok a HDFS-ben a számítási fürtön belül találhatók. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Az adatok az Azure Storage, ezzel szemben az elérhető lesz a HDFS API-k vagy a Blob storage REST API-kon keresztül. Ezzel az elrendezéssel fokozott miatt (beleértve más HDInsight fürtöket) alkalmazások és eszközök nagyobb készlete használható előállításához és fogyasztásához az adatokat.

* **Adatarchiválás:** Ha az Azure Storage-ban tároljuk, felhasználói adatok elvesztése nélkül biztonságosan törölhető a törölje a számításhoz használt HDInsight fürtöket.

* **Adattárolási költség:** Az elosztott Fájlrendszerbeli adatok a hosszú távú tárolása költségesebb, mint az adatok tárolása az Azure Storage-ban, mert a számítási fürt költsége nagyobb, mint az Azure Storage költsége. Ezenkívül mivel az adatok nem kell újból betölteni minden számítási fürt létrehozásakor, menti az Adatbetöltési költségeket is.

* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a méretezési csoport, amely a fürthöz létrehozott csomópontok száma határozza meg. A lépték módosítása bonyolultabb, mint a rugalmas skálázás az Azure Storage automatikusan léptékezési képességekre támaszkodna lehet.

* **Georeplikáció:** Az Azure Storage georeplikált is lehet. Bár a georeplikációt biztosít földrajzi helyreállítást és adatredundanciát, a georeplikált helyre feladatátvevő súlyosan érinti a teljesítményt, és előfordulhat, hogy további költségekkel. Így dönt a georeplikáció, használja, és csak ha az adat értékét indokolja, hogy a további költségeket.

Bizonyos MapReduce-feladatok és csomagok előfordulhat, hogy köztes eredményeket hozhatnak létre, amelyet nem szeretne az Azure Storage-ban. Ebben az esetben az adatok tárolása a helyi HDFS választhat. HDInsight elosztott Fájlrendszerbeli használja több ilyen köztes eredményhez a Hive-feladatokban és egyéb folyamatokban.

> [!NOTE]  
> A legtöbb HDFS parancs (például `ls`, `copyFromLocal`, és `mkdir`) az Azure Storage-ban várt módon működik. Csak azok a parancsok, mint például a natív HDFS implementációra (más nevezzük az elosztott Fájlrendszerbeli), jellemző `fschk` és `dfsadmin`, eltérő viselkedéssel megjelenítése az Azure Storage-ban.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Az Azure Data Lake Storage Gen1 áttekintése

Az Azure Data Lake Storage Gen1 egy vállalati szintű nagy kapacitású adattár a big Data típusú adatok adatelemzési számítási feladatokhoz. Az Azure Data Lake, rögzítheti az bármilyen méretű, típusú és betöltési sebesség helyen történő műveleti és felderítési jellegű adatokat.

Hozzáférés Data Lake Storage Gen1 () hadoopból érhető el egy HDInsight-fürttel a WebHDFS-kompatibilis REST API-k használatával. Data Lake Storage Gen1 célja, hogy engedélyezze a tárolt adatok elemzése, és az adatelemzési forgatókönyvekben teljesítményre van hangolva. Beépített, alapvető fontosságú a való életből vett vállalati használati esetek funkciót tartalmaz. Ezek a képességek közé tartozik a biztonsági, kezelhetőség, méretezhetőség, megbízhatóság és rendelkezésre állási.

Az Azure Data Lake Storage Gen1 további információkért tekintse meg a részletes [áttekintése az Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Data Lake Storage Gen1 főbb képességei a következők:

### <a name="compatibility-with-hadoop"></a>Kompatibilitási Hadoop-keretrendszerrel

Data Lake Storage Gen1 egy Apache Hadoop-fájlrendszer, amely kompatibilis a HDFS- és a Hadoop környezetben működik.  A meglévő HDInsight-alkalmazások vagy szolgáltatások, amelyek a WebHDFS API-val könnyen integrálhatók a Data Lake Storage Gen1. Data Lake Storage Gen1 továbbá elérhetővé tesz egy WebHDFS-kompatibilis REST-felület alkalmazásokhoz.

A Data Lake Storage Gen1 tárolt adatok könnyen elemezhetők a Hadoop elemzési keretrendszerek, például a MapReduce vagy struktúrát. Az Azure HDInsight-fürtök kell létrehozni és konfigurálni a Data Lake Storage Gen1 tárolt adatok közvetlen elérésére.

### <a name="unlimited-storage-petabyte-files"></a>Korlátlan tárterület, petabájtnyi fájlok

Data Lake Storage Gen1 korlátlan tárterületet biztosít, és különféle elemzési adatok tárolására alkalmas. A fiókok méretének, a fájlok méretét vagy a data lake-ben tárolt adatok mennyisége nem készletként. Egyes fájlok mérete kilobájtban, több petabájtnyi, így a Data Lake Storage Gen1 bármilyen típusú adat tárolásához kiváló választás terjedhet. Adatok azáltal, hogy több példányt tartósan tárolja, és az adatok mennyi ideig tárolhatók a data Lake esetében korlátlan.

### <a name="performance-tuning-for-big-data-analytics"></a>Teljesítmény-finomhangolási big Data-elemzés

Data Lake Storage Gen1 épül, lekérdezéséhez és kielemzéséhez nagy mennyiségű adat teljesítményt igénylő nagy méretű elemzési rendszerek futtatására. A data lake több egyéni tárolókiszolgáló kiszolgálóra osztja el egy fájl részeit. Elemzett adatokat, amikor a telepítő javítja az olvasás átviteli sebességét párhuzamosan a fájl olvasásakor.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Vállalati felkészültségét: Magas rendelkezésre állású és biztonságos

Data Lake Storage Gen1 biztosít az iparági szabványnak megfelelő rendelkezésre állás és megbízhatóság. Adatok adatvagyonának tartós tárolását: redundáns példányszámmal megvédje a váratlan meghibásodások esetén. Vállalatok számára fontos része annak a létező adatplatform, használhatja saját megoldások Data Lake Storage Gen1.

Data Lake Storage Gen1 is biztosít a tárolt adatok nagyvállalati szintű biztonságát. További információkért lásd: [az adatok védelme az Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Rugalmas datové struktury

Data Lake Storage Gen1 is tárol adatokat a natív formátumban, ez, előzetes átalakítás nélkül. Data Lake Storage Gen1 nem igényli egy séma, az adatok betöltése előtt. Az egyéni elemzési keretrendszer értelmezi az adatokat, és meghatározza a sémát az elemzés idején. Tetszőleges méretű és formátumú fájlokat képes tárolni, mivel a Data Lake Storage Gen1 strukturált, félig strukturált és strukturálatlan adatok kezelésére képes.

Data Lake Storage Gen1 tárolókból adatok olyan lényegében mappák és fájlok. SDK-k, az Azure Portalon és az Azure PowerShell használatával kezelheti a tárolt adatokat. Mindaddig, amíg a tároló adatait üzembe ezeket az adaptereket és a megfelelő tárolók használatával, bármilyen típusú adatot tárolhat. Data Lake Storage Gen1 végrehajtására nem kezeli különleges módon az adatokat a tárolt adatok típusa alapján.

## <a name="DataLakeStoreSecurity"></a>A Data Lake Storage Gen1 Adatbiztonság
A Data Lake Storage Gen1 használja az Azure Active Directory hitelesítési és felhasználási hozzáférést listák (ACL) való hozzáférés kezelése szabályozhatja az adatokhoz.

| **Szolgáltatás** | **Leírás** |
| --- | --- |
| Authentication |Data Lake Storage Gen1 együttműködik az Azure Active Directory (Azure AD) identitás- és hozzáférés-kezelés a Data Lake Storage Gen1 tárolt összes adatot. Az integráció miatt Data Lake Storage Gen1 számos előnyt biztosít, az összes Azure AD-funkciók. Ezek a funkciók közé tartozik a többtényezős hitelesítés, a feltételes hozzáférés, szerepköralapú hozzáférés-vezérlés, Alkalmazáshasználat monitorozását, biztonsági figyelés és riasztások, és így tovább. Data Lake Storage Gen1 támogatja az OAuth 2.0 protokollt a REST-felületen belüli hitelesítéshez. Lásd: [hitelesítés az Azure Data Lake Storage Gen1 belül az Azure Active Directoryval](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Hozzáférés-vezérlés |Data Lake Storage Gen1 hozzáférés-vezérlést biztosít a WebHDFS protokoll által elérhetővé tett POSIX-stílusú engedélyek támogatásával. A hozzáférés-vezérlési listák a gyökérkönyvtáron, az alkönyvtárakon és az egyes fájlokon is engedélyezhetők. A hozzáférés-vezérlési listák Data Lake Storage Gen1 kontextusában működéséről további információkért lásd: [hozzáférés-vezérlés a Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Titkosítás |Data Lake Storage Gen1 is biztosít a fiókban tárolt adatok titkosítását. A Data Lake Storage Gen1 fiók létrehozásakor megadhatja a titkosítási beállításokat. Ha szeretné, hogy titkosítja az adatokat, vagy választhatja a titkosítás nélkül. További információkért lásd: [titkosítást a Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). A titkosítással kapcsolatos konfigurációk megadására útmutatásért lásd: [Azure Data Lake Storage Gen1 használatának első lépései az Azure portal használatával](../data-lake-store/data-lake-store-get-started-portal.md). |

A Data Lake Storage Gen1 adatok védelmével kapcsolatos további tudnivalókért lásd: [az Azure Data Lake Storage Gen1 tárolt adatok védelme](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Az alkalmazásokat, amelyek kompatibilisek a Data Lake Storage Gen1
Data Lake Storage Gen1 kompatibilis a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőkkel. Emellett egyéb Azure-szolgáltatásokkal is jól integrálható.  Ha többet szeretne megtudni, hogyan használható Data Lake Storage Gen1, mind a nyílt forráskódú összetevőkkel és az egyéb Azure-szolgáltatások az alábbi hivatkozásokat követve.

* Lásd: [nyílt forráskódú big data-alkalmazások, amelyek együttműködnek az Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) működik együtt a Data Lake Storage Gen1 nyílt forráskódú alkalmazások listáját.
* Lásd: [integrálása az Azure Data Lake Storage Gen1 más Azure-szolgáltatásokkal](../data-lake-store/data-lake-store-integrate-with-other-services.md) megérteni a Data Lake Storage Gen1 használata más Azure-szolgáltatások széles körének forgatókönyvek engedélyezéséhez.
* Lásd: [használata Azure Data Lake Storage Gen1 a big data-követelményekhez](../data-lake-store/data-lake-store-data-scenarios.md) megtudhatja, hogyan használható a Data Lake Storage Gen1 tölt be adatot, adatok feldolgozása, letöltése és vizualizációja – egyéb felhasználási helyzetek.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 file system (adl://)
A Hadoop-környezetekben (egy HDInsight-fürtön elérhető), Data Lake Storage Gen1 szolgáltatáshoz keresztül férhet hozzá az új fájlrendszer, a AzureDataLakeFilesystem (adl: / /). A teljesítmény, az alkalmazások és szolgáltatások, amelyek használják az adl: / /, hogy nem érhető el a WebHDFS-ben is lehet optimalizálni. Ennek eredményeképpen Data Lake Storage Gen1 használatakor, rugalmasan bármelyik elérhető a legjobb teljesítmény érdekében a javasolt adl használatával: / / vagy fenntarthatja a meglévő kód továbbra is a WebHDFS API közvetlen használja. Az Azure HDInsight teljes mértékben kihasználhatja a AzureDataLakeFilesystem a legjobb teljesítményt biztosítja a Data Lake Storage Gen1 vesz igénybe.

A Data Lake Storage Gen1-adatok elérése a következő használatával:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

A Data Lake Storage Gen1 az adatokkal való további információkért lásd: [a tárolt adatokra vonatkozó műveletek](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Data Lake Storage Gen2-re](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)

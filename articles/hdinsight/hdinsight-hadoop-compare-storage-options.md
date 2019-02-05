---
title: Az Azure HDInsight-fürtökhöz való használatra tárolási lehetőségek összehasonlítása
description: A tárolási típusok és azok működéséről az Azure HDInsight áttekintése.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 7f113587dfabd66461a9bcfbde18a0178c6608f0
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733545"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtökhöz való használatra tárolási lehetőségek összehasonlítása

Az Azure HDInsight-felhasználók választhatnak, hogy néhány különböző tárolási lehetőségek HDInsight-fürtök létrehozása során:

* 2. generációs Azure Data Lake Storage
* Azure Storage
* 1. generációs Azure Data Lake Storage

Ez a cikk ezek a különböző tárolási típusok és a hozzájuk tartozó egyedi funkciók áttekintése.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Az Azure Data Lake Storage Gen2 használata az Azure HDInsight az Apache Hadoop-keretrendszerrel

További információ az Azure Data Lake Storage Gen2: [Bevezetés az Azure Data Lake Storage Gen2-re](../storage/blobs/data-lake-storage-introduction.md).

Az Azure Data Lake Storage Gen2 idő szükséges alapvető funkcióit az Azure Data Lake Storage Gen1 például egy kompatibilis a Hadoop-fájlrendszer, az Azure Active Directory és a POSIX-alapú hozzáférés-listákat (ACL) vezérlőelemet, és integrálja őket az Azure Blob Storage-bA. Ez a kombináció lehetővé teszi kihasználhatja az Azure Data Lake Storage Gen1 teljesítményét is használata a Blob Storage-rétegezést és az adatok életciklus-kezelése során.

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 fő funkciói

* Hadoop-kompatibilis hozzáférés: Az Azure Data Lake Storage Gen2 lehetővé teszi, hogy kezelését, és ugyanúgy, mint az a Hadoop elosztott fájlrendszer (HDFS) adatok eléréséhez. Az Azure Blob fájlrendszer (ABFS) illesztőprogram összes Apache Hadoop-környezetekben, beleértve az Azure HDInsight és az Azure Databricks a Data Lake Storage Gen2 adataihoz hozzáférést érhető el.

* Felülbírálja a POSIX-engedélyek: A Data Lake Gen2 biztonsági modell támogatja az ACL-JEI és POSIX engedélyekkel együtt néhány további pontossággal megadott Data Lake Storage Gen2-re. Beállítások felügyeleti eszközöket és keretrendszereket, mint például az Apache Hive és az Apache Spark keresztül konfigurálhatók.

* Költséghatékony: Data Lake Storage Gen2 költséghatékony tárolási kapacitás és a tranzakciók érhetők el. Szolgáltatások, például az Azure Blob storage életciklus alacsonyabb költségek segítségével módosításával díjszabása nem módosul, az adatok életciklusa továbbítanak.

* A Blob storage-eszközökkel, keretrendszerek és alkalmazások működését: Data Lake Storage Gen2 továbbra is az eszközöket, keretrendszerek és alkalmazások a Blob Storage jelenleg létező széles választékának működik.

* Optimalizált illesztőprogram: A ABFS illesztőprogram kifejezetten a big data-elemzés optimalizáltuk. A megfelelő REST API-k az elosztott fájlrendszerbeli végpont végzetesnek dfs.core.windows.net.

### <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Az Azure Data Lake Storage általános 2 kapcsolatos újdonságok

#### <a name="managed-identities-for-secure-file-access"></a>Felügyelt identitások számára biztonságos hozzáférést

Az Azure HDInsight fürt védelméhez az Azure Data Lake Storage Gen2 fájlok felügyelt identitásokat használ. Felügyelt identitások érhetők el az Azure Active Directory által biztosított Azure-szolgáltatások automatikusan kezelt hitelesítő adatok vannak beállítva. Ezekkel a hitelesítő adatokkal történő hitelesítéshez minden olyan szolgáltatás, amely támogatja az AD-hitelesítés használható. Felügyelt identitások használatával nincs szükség, hogy kódírásra vagy konfigurálásra fájlban tárolhatja a hitelesítő adatokat.

További információkért lásd: [Mi az Azure-erőforrások felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-filesystem-abfs-driver"></a>Az Azure Blob fájlrendszer (ABFS) illesztőprogram

Az Apache Hadoop-alkalmazások natív módon várhatóan adatokat olvasnak és írnak a helyi lemezes tárterületet. Egy Hadoop-fájlrendszer illesztőprogram ABFS például lehetővé teszi a Hadoop-alkalmazások által működni a Hadoop-fájl rendszerműveletekről rendszeres emulálni a felhőalapú tárolással működjön. Az illesztőprogram alakítja át azokat a parancsokat, amely a tényleges felhőalapú tárolási platform együttműködik a műveletekkel alkalmazástól kapott.

Korábban a Hadoop-fájlrendszer illesztőprogram lenne minden fájlrendszerműveletek átalakítása az Azure Storage REST API-hívásokat az ügyféloldalon, és ezután meghívja a REST API-t. Az ügyféloldali átalakítás, azonban több REST API eredményezett egy egyetlen fájlrendszer-műveletet, például egy fájl átnevezése szükségessé teszi. ABFS át lett helyezve a Hadoop-fájlrendszer logika némelyike az ügyfél oldaláról a kiszolgáló- és az Azure Data Lake Storage Gen2 API most már a Blob API-val párhuzamosan fut. Az áttelepítés javítja a teljesítményt, mert most Hadoop gyakori fájlrendszerműveletre hajtható végre egy REST API-hívással.

További információkért lásd: [az Azure Blob fájlrendszer illesztőprogram (ABFS): Egy dedikált Azure Storage-illesztőprogram hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="azure-data-lake-storage-gen-2-uri-scheme"></a>Az Azure Data Lake Storage általános 2 URI-séma

Az Azure Data Lake Storage Gen2 elérnek egy fájlt az Azure BLOB storage a HDInsight egy új URI-séma használ:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Az URI-séma SSL titkosított hozzáférést biztosít (`abfss://` előtag) és a nem titkosított hozzáférést (`abfs://` előtag). Azt javasoljuk, `abfss` amikor csak lehetséges, akkor is, ha ugyanabban a régióban, az Azure-ban lévő adatokat éri el.

`<FILE_SYSTEM_NAME>` az elérési útját a fájlrendszerben, Data Lake Storage Gen2 azonosítja.

`<ACCOUNT_NAME>` az Azure Storage-fiók neve azonosítja. Szükség van a teljes tartománynévre (FQDN-re).

A `<PATH>` a fájl vagy könyvtár HDFS elérési út.

Ha az értékek `<FILE_SYSTEM_NAME>` és `<ACCOUNT_NAME>` nincs megadva, az alapértelmezett fájlrendszer szolgál. Az alapértelmezett fájlrendszeren tárolt fájlok esetén relatív elérési utat vagy abszolút elérési utat használhat. Ha például a `hadoop-mapreduce-examples.jar` fájlt, amely együttműködik a HDInsight-fürtök a következő útvonalak egyikét használva lehet hivatkozni:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> A Fájlnév `hadoop-examples.jar` HDInsight 2.1-es és 1.6-os verziójának fürtökben. Ha HDInsight-on kívül fájljain végzett munka, legtöbb segédprogram nem ismeri fel a ABFS formázhatja, és ehelyett például az egy alapvető elérési út formátumot várhatóan `example/jars/hadoop-mapreduce-examples.jar`.

További információkért lásd: [használata az Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="use-azure-storage"></a>Az Azure Storage használata

Az Azure Blob Storage egy robusztus, általános célú tárolómegoldás, amely zökkenőmentesen integrálható a HDInsight eszközzel. A HDInsight egy blobtárolót használhat az Azure Storage-ben a fürt alapértelmezett fájlrendszereként. A Hadoop elosztott fájlrendszer (HDFS) felületen keresztül a HDInsight összetevők teljes készlete működhet közvetlenül a strukturált vagy strukturálatlan adatokon a Blob Storage tárolóban.

> [!WARNING]  
> Az Azure Storage-fiók létrehozása során számos beállítás áll rendelkezésre. A következő táblázat információkat nyújt arról, hogy milyen beállítások támogatottak a HDInsight használatakor:

| Tárfiók típusa | Támogatott szolgáltatások | Támogatott teljesítményszintek | Támogatott elérési szint |
|----------------------|--------------------|-----------------------------|------------------------|
| Általános célú V2   | Blob               | Standard                    | Gyakori és ritka elérésű, archív *    |
| Általános célú V1   | Blob               | Standard                    | –                    |
| Blob Storage         | Blob               | Standard                    | Gyakori és ritka elérésű, archív *    |

Nem ajánlott, hogy üzleti adatokat tárolni az alapértelmezett blob tároló használata. Az alapértelmezett blobtárolót ajánlatos törölni minden egyes használat után. Az alapértelmezett tároló tartalmazza, az alkalmazás- és naplókat. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

Az alapértelmezett fájlrendszerként egy blobtároló több fürt használata nem támogatott.
 
 > [!NOTE]  
 > Az archivált adatok hozzáférési szintje egy kapcsolat nélküli réteg egy több órás késése és a HDInsight segítségével nem ajánlott. További információkért lásd: [archivált adatok hozzáférési szintje](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="hdinsight-storage-architecture"></a>HDInsight tároló-architektúra
A következő ábra az Azure Storage-ot használó HDInsight tároló-architektúra absztrakt nézetét nyújtja:

![A Hadoop-fürtök a HDFS API-val érik el és tárolják a strukturált és strukturálatlan adatokat a Blob Storage-ban.](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "HDInsight-tárarchitektúra")

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer a teljes URI használatával érhető el, például:

    hdfs://<namenodehost>/<path>

Emellett a HDInsight lehetővé teszi az Azure Storage tárolóban tárolt adatok elérését. A szintaxis a következő:

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Az alábbiakban néhány szempont olvasható Azure Storage-fiók és HDInsight-fürtök együttes használatával kapcsolatban.

* **A storage-fiókokban fürthöz csatlakozó tárolók:** Mivel a fiók neve és kulcsa társítva a fürt létrehozása során, akkor ezeket a tárolókban lévő blobokhoz teljes hozzáféréssel rendelkezik.

* **Nyilvános tárolók vagy nyilvános blobok a storage-fiókok, amelyek nem csatlakoznak a fürthöz:** Rendelkezik olvasási engedéllyel a tárolókban lévő blobokat.
  
  > [!NOTE]  
  > A nyilvános tárolók esetén a tárolóban elérhető összes blob listáját és a tároló metaadatait is lekérheti. A nyilvános blobok esetén csak akkor érheti el a blobokat, ha ismeri a pontos URL-t. További információkért lásd: [tárolókhoz és blobokhoz való hozzáférés kezelése](../storage/blobs/storage-manage-access-to-resources.md).

* **A storage-fiókok fürthöz nem csatlakozó személyes tárolók:** Nem férhet hozzá a tárolókban lévő blobokat, kivéve, ha a WebHCat-feladatok elküldésekor határozza meg a tárfiókot. Ennek a magyarázatát a cikk későbbi részében találja.

A létrehozási folyamat során meghatározott tárfiókok és a kulcsaik a %HADOOP_HOME%/conf/core-site.xml fájlban találhatók a fürtcsomópontokon. A HDInsight alapértelmezett viselkedése a core-site.xml fájlban meghatározott tárfiókok használata. Ez módosítható használatának beállítása [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Több WebHCat-feladatok, többek között az Apache Hive, MapReduce, az Apache Hadoop-Stream és az Apache Pig, képes továbbítani a storage-fiókok és a velük metaadatok leírását. (Ez jelenleg a tárfiókokkal működik a Pig-feladatokkal, a metaadatokkal nem.) További információkért lásd: [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (HDInsight-fürtök használata alternatív tárfiókokkal és metaadattárakkal).

A blobok a strukturált és strukturálatlan adatokhoz használhatók. A blobtárolók kulcs/érték párokként tárolnak adatokat, és nincs könyvtár-hierarchia. A perjel karakter ( / ) azonban használható a kulcsnévben, hogy úgy tűnjön, mintha a fájl könyvtárszerkezetben lenne tárolva. Ha például egy blob kulcsa lehet `input/log1.txt`. Nem tényleges `input` könyvtár létezik, de mivel jelen van a perjel karakter a kulcs nevét, egy fájl elérési útját megjelenésének rendelkezik.

### <a id="benefits"></a>Az Azure Storage előnyei
A számítási fürtök és tárolási erőforrások nem egy helyre helyezésével járó teljesítményköltségeket csökkenti a számítási fürtöknek a tárfiók erőforrásainak közelében való létrehozása az Azure-régión belül, ahol a nagysebességű hálózat hatékonnyá teszi a számítási csomópontok számára az Azure Storage-ban lévő adatok elérését.

Több előnye is van annak, ha az adatokat a HDFS helyett az Azure Blob Storage tárolóban tárolja:

* **Adatok újbóli használata és megosztása:** Az adatok a HDFS-ben a számítási fürtön belül találhatók. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Az Azure Storage-adatok a HDFS API-k vagy a Blob Storage REST API-kon keresztül érhetők el. Így az alkalmazások (beleértve más HDInsight fürtöket) és eszközök nagyobb készlete használható az adatok előállításához és fogyasztásához.
* **Adatarchiválás:** Adatok az Azure storage tárolóban végzett tárolása lehetővé teszi, hogy a, hogy biztonságosan felhasználói adatok elvesztése nélkül törölje a számításhoz használt HDInsight fürtöket.
* **Adattárolási költség:** Az elosztott Fájlrendszerbeli adatok a hosszú távú tárolása költségesebb, mint az adatok tárolása az Azure storage-ban, mert a számítási fürt költsége nagyobb, mint az Azure storage költsége. Ezenkívül mivel az adatok nem kell újból betölteni minden számítási fürt létrehozásakor, is menti Adatbetöltési költségeket.
* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a méretezési csoport, amely a fürthöz létrehozott csomópontok száma határozza meg. A lépték módosítása bonyolultabb folyamattá válhat, mintha az Azure Blob Storage tárolóban automatikusan elérhető rugalmas léptékezési képességekre támaszkodna.
* **Georeplikáció:** Az Azure storage georeplikált is lehet. Bár ez földrajzi helyreállítást és adatredundanciát biztosít, a georeplikált helyre végzett feladatátvétel súlyos hatással van a teljesítményre, és további költségekkel járhat. Így azt javasoljuk, hogy válassza körültekintően a georeplikációt, és csak akkor, ha az adatok megérik a további költségeket.

Bizonyos MapReduce-feladatok és csomagok olyan köztes eredményeket hozhatnak létre, amelyeket nem érdemes az Azure Blob Storage tárolóban tárolni. Ebben az esetben a helyi HDFS-ben is tárolhatja az adatokat. Valójában a HDInsight a DFS-t használja több ilyen köztes eredményhez a Hive-feladatokban és egyéb folyamatokban.

> [!NOTE]  
> A legtöbb HDFS parancs (például `ls`, `copyFromLocal` és `mkdir`) továbbra is a várt módon működik. Csak azok a parancsok, mint például a natív HDFS implementációra (más nevezzük az elosztott Fájlrendszerbeli), jellemző `fschk` és `dfsadmin`, eltérő viselkedéssel megjelenítése az Azure storage-ban.

## <a name="use-azure-data-lake-storage-gen1"></a>Használja az Azure Data Lake Storage Gen1

### <a name="overview"></a>Áttekintés

További információ az Azure Data Lake Storage Gen1: [áttekintése az Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Az Azure Data Lake Storage Gen1 egy vállalati szintű kapacitású adattár a big Data típusú adatok adatelemzési számítási feladatokhoz. Az Azure Data Lake lehetővé teszi mindenféle méretű, típusú és feldolgozási sebességű adatok egy helyen történő műveleti és felderítési jellegű feldolgozását.

Data Lake Storage Gen1 (egy HDInsight-fürtön érhető el) hadoopból érhető el a WebHDFS-kompatibilis REST API-k használatával. Ez a tárolt adatok elemzése lehetővé teszi, hogy, és a nagy teljesítményt nyújtson az adatelemzési forgatókönyvekben. Minden olyan vállalati szintű képességet tartalmaz – biztonság, kezelhetőség, méretezhetőség, megbízhatóság és rendelkezésre állás –, amelyek elengedhetetlenek a valós vállalati alkalmazások esetében.

![Az Azure Data Lake Storage](./media/hdinsight-hadoop-compare-storage-options/data-lake-store-concept.png "HDInsight tároló-architektúra")

Data Lake Storage Gen1 főbb képességei közé az alábbi.

#### <a name="built-for-hadoop"></a>Hadoop-kompatibilis

Data Lake Storage Gen1 egy Apache Hadoop-fájlrendszer kompatibilis a Hadoop elosztott fájlrendszer (HDFS) és a Hadoop környezetben működik.  A meglévő HDInsight-alkalmazások vagy szolgáltatások, amelyek a WebHDFS API-val könnyen integrálhatók a Data Lake Storage Gen1. Data Lake Storage Gen1 továbbá elérhetővé tesz egy WebHDFS-kompatibilis REST-felület alkalmazásokhoz

A Data Lake Storage Gen1 tárolt adatok könnyen elemezhetők a Hadoop elemzési keretrendszerek, például a MapReduce vagy struktúrát. A Microsoft Azure HDInsight-fürtök kell létrehozni és konfigurálni a Data Lake Storage Gen1 tárolt adatok közvetlen elérésére.

#### <a name="unlimited-storage-petabyte-files"></a>Korlátlan tárterület, petabájtnyi fájlok

Data Lake Storage Gen1 korlátlan tárterületet biztosít, és különféle elemzési adatok tárolására alkalmas. Nincsenek megkötések a fiókok méretének, a fájlméret, vagy a data lake-ben tárolt adatok mennyiségének tekintetében. Az egyéni fájlok mérete a kilobájtoktól a petabájtokig terjedhet, így bármilyen típusú adat tárolásához kiváló választás. A számos másolatnak köszönhetően az adatok megbízhatóan, valamint korlátlan ideig tárolhatók a data lake-ben.

#### <a name="performance-tuned-for-big-data-analytics"></a>A teljesítmény a big data koncepción alapuló adatelemzéshez lett igazítva

Data Lake Storage Gen1 az nagy méretű lekérdezéséhez és kielemzéséhez nagy mennyiségű adat teljesítményt igénylő elemző rendszerek futtatására lett tervezve. A data lake több egyéni tárolókiszolgáló között osztja el egy fájl részeit. Ez javítja az olvasás átviteli sebességét a fájl adatelemzés céljából történő párhuzamos beolvasásakor.

#### <a name="enterprise-ready-highly-available-and-secure"></a>Enterprise-ready: Magas rendelkezésre állású és biztonságos

Data Lake Storage Gen1 biztosít az iparági szabványnak megfelelő rendelkezésre állás és megbízhatóság. Adatvagyonának tartós tárolását a redundáns másolatok teszik lehetővé, amelyek védelmet biztosítanak a váratlan meghibásodások esetén. Vállalatok számára fontos része annak a létező adatplatform, használhatja saját megoldások Data Lake Storage Gen1.

Data Lake Storage Gen1 is biztosít a nagyvállalati szintű biztonsággal, a tárolt adatok számára. További információkért lásd: [az adatok védelme az Azure Data Lake Storage Gen1](#DataLakeStoreSecurity).

#### <a name="all-data"></a>Minden adat

Data Lake Storage Gen1 is tárol adatokat adatát natív formátumban, ez, módosítás vagy előzetes átalakítás nélkül. Data Lake Storage Gen1 igényli egy séma előtt az adatok betöltése, így az egyéni elemzési keretrendszer maga értelmezheti az adatokat, és definiálhat egy sémát az elemzés idején. Tetszőleges méretű és formátumú fájlok tárolásának lehetővé teszi a Data Lake Storage Gen1 strukturált, félig strukturált és strukturálatlan adatok kezelésére.

Data Lake Storage Gen1 tárolókból adatok olyan lényegében mappák és fájlok. A tárolt adatok SDK-k, az Azure portal és az Azure Powershell használatával kezelheti. Mindaddig, amíg a fenti felületeken keresztül és a megfelelő tárolók használatával helyezi el adatait a tárolóban, bármilyen típusú adatot tárolhat. Data Lake Storage Gen1 nem kezeli különleges módon az adatokat a tárolt adatok típusa alapján hajt végre.

### <a name="DataLakeStoreSecurity"></a>Az adatok védelme az Data Lake Storage Gen1
Data Lake Storage Gen1 használja az Azure Active Directory a hitelesítéshez és hozzáférés-listákat (ACL) való hozzáférés kezelése szabályozhatja az adatokhoz.

| Szolgáltatás | Leírás |
| --- | --- |
| Authentication |Data Lake Storage Gen1 együttműködik a Data Lake Storage Gen1 tárolt összes adat identitás- és hozzáférés-kezelés az Azure Active Directory (AAD). Eredményeként az integrációt, a Data Lake Storage Gen1 előnyöket AAD összes funkcióját, többek között a multi-factor authentication szolgáltatás, a feltételes hozzáférés, a szerepköralapú hozzáférés-vezérlés, a Alkalmazáshasználat monitorozását, biztonsági figyelést és riasztást stb. Data Lake Storage Gen1 támogatja az OAuth 2.0 protokollt a REST-felületen belüli hitelesítéshez. Lásd: [hitelesítés a Data Lake Storage Gen1](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Hozzáférés-vezérlés |Data Lake Storage Gen1 hozzáférés-vezérlést biztosít a WebHDFS protokoll által elérhetővé tett POSIX-stílusú engedélyek támogatásával. A hozzáférés-vezérlési listák a gyökérkönyvtáron, az alkönyvtárakon és az egyes fájlokon is engedélyezhetők. A hozzáférés-vezérlési listák Data Lake Storage Gen1 összefüggésben működéséről további információkért lásd: [hozzáférés-vezérlés a Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Titkosítás |Data Lake Storage Gen1 is biztosít a fiókban tárolt adatok titkosítását. A Data Lake Storage Gen1 fiók létrehozásakor megadhatja a titkosítási beállításokat. Dönthet úgy, hogy titkosítja az adatokat, vagy választhatja a titkosítás nélküli lehetőséget. További információkért lásd: [titkosítást a Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Útmutatás a titkosítással kapcsolatos konfigurációk megadására: [Azure Data Lake Storage Gen1 használatának első lépései az Azure portal használatával](../data-lake-store/data-lake-store-get-started-portal.md). |

További információ az adatok védelme az Data Lake Storage Gen1 szeretne? Kövesse az alábbi hivatkozásokat.

* A Data Lake Storage Gen1 biztonságos adatainak kapcsolatos utasításokért lásd: [az adatok védelme az Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

### <a name="applications-compatible-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1 kompatibilis alkalmazások
Data Lake Storage Gen1 kompatibilis a Hadoop-ökoszisztéma legtöbb nyílt forráskódú összetevőkkel. Emellett egyéb Azure-szolgáltatásokkal is jól integrálható.  Ha többet szeretne megtudni, hogyan használható Data Lake Storage Gen1, mind a nyílt forráskódú összetevőkkel és az egyéb Azure-szolgáltatások az alábbi hivatkozásokat követve.

* Lásd: [alkalmazások és szolgáltatások kompatibilis az Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) Data Lake Storage Gen1 együttműködésre képes nyílt forráskódú alkalmazások listáját.
* Lásd: [integrálása más Azure szolgáltatásokkal](../data-lake-store/data-lake-store-integrate-with-other-services.md) megérteni, hogyan Data Lake Storage Gen1 használható más Azure-szolgáltatások széles körének forgatókönyvek engedélyezéséhez.
* Lásd: [Data Lake Storage Gen1 használatára vonatkozó forgatókönyvek](../data-lake-store/data-lake-store-data-scenarios.md) megtudhatja, hogyan használható a Data Lake Storage Gen1 tölt be adatot, adatok feldolgozása, letöltése és vizualizációja – egyéb felhasználási helyzetek.

### <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Mi a Data Lake Storage Gen1 fájlrendszer (adl: / /)?
Data Lake Storage Gen1 elérhető keresztül az új fájlrendszer, a AzureDataLakeFilesystem (adl: / /), a Hadoop-környezetekben (a HDInsight-fürt érhető el). Az adl:// használatával az alkalmazások és szolgáltatások további teljesítmény-optimalizálásokat hasznosíthatnak, amelyek jelenleg nem érhetők el a WebHDFS-ben. Eredményeképpen Data Lake Storage Gen1 rugalmasságot biztosít, amellyel a legjobb teljesítmény érdekében az ajánlott beállítással, adl: / / vagy fenntarthatja a meglévő kód továbbra is a WebHDFS API közvetlen használja. Az Azure HDInsight teljes mértékben kihasználja az a legjobb teljesítményt biztosítja a Data Lake Storage Gen1 AzureDataLakeFilesystem.

Férhet hozzá az adataihoz, a Data Lake Storage Gen1 használatával `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`. A Data Lake Storage Gen1 az adatokkal való további információkért lásd: [a tárolt adatok tulajdonságainak megtekintése](../data-lake-store/data-lake-store-get-started-portal.md#properties)



## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Data Lake Storage Gen2-re](../storage/blobs/data-lake-storage-introduction.md).
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)
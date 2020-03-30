---
title: Az Azure HDInsight-fürtökkel használható tárolási lehetőségek összehasonlítása
description: Áttekintést nyújt a tárolási típusokról és az Azure HDInsight-val való munkájukról.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 98d71434ac9e3f712be0cbd8c505b7d5a537e7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79095546"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtökkel használható tárolási lehetőségek összehasonlítása

HdInsight-fürtök létrehozásakor választhat néhány különböző Azure storage-szolgáltatás közül:

* Azure Storage
* 2. generációs Azure Data Lake Storage
* 1. generációs Azure Data Lake Storage

Ez a cikk áttekintést nyújt ezekről a tárolási típusokról és azok egyedi szolgáltatásairól.

Az alábbi táblázat összefoglalja a HDInsight különböző verzióival támogatott Azure Storage-szolgáltatásokat:

| Tárolási szolgáltatás | Fiók típusa | Névtér típusa | Támogatott szolgáltatások | Támogatott teljesítményszintek | Támogatott hozzáférési szintek | HDInsight verzió | Fürt típusa |
|---|---|---|---|---|---|---|---|
|2. generációs Azure Data Lake Storage| Általános célú V2 | Hierarchikus (fájlrendszer) | Blob | Standard | Forró, Hűvös, Archívum | 3,6+ | A Spark 2.1 és 2.2 kivételével|
|Azure Storage| Általános célú V2 | Objektum | Blob | Standard | Forró, Hűvös, Archívum | 3,6+ | Összes |
|Azure Storage| Általános célú V1 | Objektum | Blob | Standard | N/A | Összes | Összes |
|Azure Storage| Blob-tároló** | Objektum | Blob blokkolása | Standard | Forró, Hűvös, Archívum | Összes | Összes |
|1. generációs Azure Data Lake Storage| N/A | Hierarchikus (fájlrendszer) | N/A | N/A | N/A | 3.6 Csak | Minden, kivéve a HBase |

**HdInsight-fürtök esetén csak a másodlagos tárfiókok lehetnek BlobStorage típusúak, és a Lapblob nem támogatott tárolási lehetőség.

Az Azure Storage-fióktípusokkal kapcsolatos további információkért tekintse meg az [Azure storage-fiók áttekintése című témakört.](../storage/common/storage-account-overview.md)

Az Azure Storage hozzáférési szintjeiről további információt az [Azure Blob storage: Premium (előzetes verzió), a gyakori elérésű, a ritka elérésű és az archív tárolási szintek című témakörben talál.](../storage/blobs/storage-blob-storage-tiers.md)

Fürtöt az elsődleges és választható másodlagos tároláshoz különböző szolgáltatáskombinációk használatával hozhat létre. Az alábbi táblázat a HDInsight által jelenleg támogatott fürttárolási konfigurációkat foglalja össze:

| HDInsight verzió | Elsődleges tárolás | Másodlagos tárolás | Támogatott |
|---|---|---|---|
| 3,6 & 4,0 | Általános célú V1 , Általános célú V2 | Általános célú V1 , Általános célú V2, BlobStorage(Blokkblobok) | Igen |
| 3,6 & 4,0 | Általános célú V1 , Általános célú V2 | 2. generációs Data Lake Storage | Nem |
| 3,6 & 4,0 | Data Lake Storage Gen2* | 2. generációs Data Lake Storage | Igen |
| 3,6 & 4,0 | Data Lake Storage Gen2* | Általános célú V1 , Általános célú V2, BlobStorage(Blokkblobok) | Igen |
| 3,6 & 4,0 | 2. generációs Data Lake Storage | 1. generációs Data Lake Storage | Nem |
| 3.6 | 1. generációs Data Lake Storage | 1. generációs Data Lake Storage | Igen |
| 3.6 | 1. generációs Data Lake Storage | Általános célú V1 , Általános célú V2, BlobStorage(Blokkblobok) | Igen |
| 3.6 | 1. generációs Data Lake Storage | 2. generációs Data Lake Storage | Nem |
| 4.0 | 1. generációs Data Lake Storage | Bármelyik | Nem |
| 4.0 | Általános célú V1 , Általános célú V2 | 1. generációs Data Lake Storage | Nem |

*=Ez lehet egy vagy több Data Lake Storage Gen2-fiók, feltéve, hogy mindegyik úgy van beállítva, hogy ugyanazt a felügyelt identitást használja a fürthozzáféréshez.

> [!Note] 
> A Data Lake Storage Gen2 elsődleges tárolója nem támogatott a Spark 2.1 vagy 2.2 fürtök esetében. 

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Az Azure Data Lake Storage Gen2 használata az Apache Hadoop segítségével az Azure HDInsightban

Az Azure Data Lake Storage Gen2 az Azure Data Lake Storage Gen1 alapvető funkcióit veszi igénybe, és integrálja azokat az Azure Blob storage-ba. Ezek a funkciók közé tartozik a fájlrendszer, amely kompatibilis a Hadoop, az Azure Active Directory (Azure AD) és a POSIX-alapú hozzáférés-vezérlési listák (ACLs). Ez a kombináció lehetővé teszi, hogy kihasználja az Azure Data Lake Storage Gen1 teljesítményét, miközben a Blob storage rétegezési és adatéletciklus-kezelését is használja.

Az Azure Data Lake Storage Gen2 szolgáltatásról az [Azure Data Lake Storage Gen2 – Bevezetés](../storage/blobs/data-lake-storage-introduction.md)című témakörben talál további információt.

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Az Azure Data Lake Storage Gen2 alapvető funkciói

* **A Hadoop-al kompatibilis hozzáférés:** Az Azure Data Lake Storage Gen2-ben ugyanúgy kezelheti és érheti el az adatokat, mint a Hadoop elosztott fájlrendszer (HDFS) használatával. Az Azure Blob File System (ABFS) illesztőprogram jattson az összes Apache Hadoop környezetben, beleértve az Azure HDInsight és az Azure Databricks. Az ABFS használatával a Data Lake Storage Gen2-ben tárolt adatok eléréséhez.

* **PoSIX engedélyek egy része:** A Data Lake Gen2 biztonsági modellje támogatja az ACL és a POSIX engedélyeket, valamint néhány további, a Data Lake Storage Gen2-re jellemző extra részletességet. A beállítások konfigurálhatók felügyeleti eszközökön vagy keretrendszereken, például az Apache Hive-n és az Apache Sparkon keresztül.

* **Költséghatékonyság:** A Data Lake Storage Gen2 alacsony költségű tárolókapacitást és tranzakciókat kínál. Az olyan funkciók, mint például az Azure Blob tárolási életciklusa, csökkentik a költségeket azáltal, hogy az adatok életciklusa során módosítják a számlázási díjakat.

* **Kompatibilitás a Blob-tárolóeszközökkel, keretrendszerekkel és alkalmazásokkal:** A Data Lake Storage Gen2 továbbra is számos eszközzel, keretrendszerrel és alkalmazással működik a Blob storage számára.

* **Optimalizált illesztőprogram:** Az ABFS-illesztőprogram kifejezetten a big data-elemzéshez van optimalizálva. A megfelelő REST API-k az elosztott fájlrendszer (DFS) végponton keresztül kerülnek felszínre, dfs.core.windows.net.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Az Azure Data Lake Storage Gen 2 újdonságai

#### <a name="managed-identities-for-secure-file-access"></a>Felügyelt identitások a biztonságos fájlhozzáféréshez

Az Azure HDInsight felügyelt identitások használatával biztosítja a fürt hozzáférést az Azure Data Lake Storage Gen2 fájljaihoz. A felügyelt identitások az Azure Active Directory egyik szolgáltatása, amely automatikusan felügyelt hitelesítő adatokat biztosít az Azure-szolgáltatások számára. Ezek a hitelesítő adatok az Active Directory-hitelesítést támogató szolgáltatások hitelesítésére használhatók. Felügyelt identitások használata nem igényel hitelesítő adatokat kód- vagy konfigurációs fájlokban tárolni.

További információ: [Felügyelt identitások az Azure-erőforrásokhoz.](../active-directory/managed-identities-azure-resources/overview.md)

#### <a name="azure-blob-file-system-driver"></a>Azure Blob fájlrendszer-illesztőprogram

Az Apache Hadoop alkalmazások natív módon elvárják az adatok olvasását és írását a helyi lemeztárolóból. A Hadoop fájlrendszer-illesztőprogram, például az ABFS lehetővé teszi, hogy a Hadoop-alkalmazások a normál Hadoop fájlrendszer-műveletek emulálásával működjenek együtt a felhőalapú tárolással. Az illesztőprogram az alkalmazástól kapott parancsokat olyan műveletekké alakítja, amelyeket a tényleges felhőtárolási platform megért.

Korábban a Hadoop fájlrendszer-illesztőprogram az összes fájlrendszer-műveletet az Ügyféloldalon az Azure Storage REST API-hívásokra konvertálta, majd meghívta a REST API-t. Ez az ügyféloldali átalakítás azonban több REST API-t eredményezett egyetlen fájlrendszer-művelethez, például egy fájl átnevezéséhez. Az ABFS a Hadoop fájlrendszer logikájának egy részét az ügyféloldalról a kiszolgálóoldalra helyezte át. Az Azure Data Lake Storage Gen2 API most párhuzamosan fut a Blob API-val. Ez az áttelepítés javítja a teljesítményt, mert mostantól a Hadoop fájlrendszer gyakori műveletei egyetlen REST API-hívással hajthatók végre.

További információ: [The Azure Blob Filesystem driver (ABFS): A dedikált Azure Storage-illesztőprogram hadoop.](../storage/blobs/data-lake-storage-abfs-driver.md)

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-séma az Azure Data Lake Storage Gen 2-hez 

Az Azure Data Lake Storage Gen2 új URI-sémát használ az Azure Storage-ban lévő fájlok HDInsightból való eléréséhez:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Az URI-séma SSL-titkosítású hozzáférést biztosít.

`<FILE_SYSTEM_NAME>`azonosítja a Fájlrendszer Data Lake Storage Gen2 elérési útját.

`<ACCOUNT_NAME>`azonosítja az Azure Storage-fiók nevét. Szükség van a teljes tartománynévre (FQDN-re).

`<PATH>`a HDFS fájl- vagy könyvtárelérési útja.

Ha a `<FILE_SYSTEM_NAME>` `<ACCOUNT_NAME>` megadott és a nincs érték, akkor a rendszer az alapértelmezett fájlrendszert használja. Az alapértelmezett fájlrendszerben lévő fájlokhoz használjon relatív elérési utat vagy abszolút elérési utat. A HDInsight-fürtökhöz tartozó `hadoop-mapreduce-examples.jar` fájlra például az alábbi elérési utak egyikével lehet hivatkozni:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> A fájlnév `hadoop-examples.jar` a HDInsight 2.1-es és 1.6-os verzióiban található. Ha a HDInsight-on kívüli fájlokkal dolgozik, azt fogja találni, hogy a legtöbb segédprogram nem ismeri fel `example/jars/hadoop-mapreduce-examples.jar`az ABFS formátumot, hanem alapvető elérési útformátumot vár, például .

További információ: [Az Azure Data Lake Storage Gen2 URI használata.](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage egy robusztus általános célú tárolási megoldás, amely zökkenőmentesen integrálható a HDInsightdal. A HDInsight egy blobtárolót használhat az Azure Storage-ben a fürt alapértelmezett fájlrendszereként. Egy HDFS-felületen keresztül a HDInsight összetevőinek teljes készlete közvetlenül működhet a blobként tárolt strukturált vagy strukturálatlan adatokon.

Azt javasoljuk, hogy az alapértelmezett fürttárolóhoz és az üzleti adatokhoz különtárolótárolókat használjon a HDInsight-naplók és az ideiglenes fájlok elkülönítéséhez a saját üzleti adataitól. Azt is javasoljuk, hogy minden használat után a tárolási költségek csökkentése után az alapértelmezett blob tároló, amely tartalmazza az alkalmazás és a rendszer naplók. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

Ha úgy dönt, hogy a tárfiókot a **tűzfalak és** a virtuális hálózatok korlátozások **a kiválasztott hálózatokon,** győződjön meg róla, hogy a kivétel engedélyezése megbízható **Microsoft-szolgáltatások ,** hogy a HDInsight hozzáférhet a tárfiókhoz.

### <a name="hdinsight-storage-architecture"></a>HDInsight tároló-architektúra

Az alábbi ábra az Azure Storage HDInsight-architektúrájának absztrakt nézetét tartalmazza:

![HDInsight tárolási architektúra](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "HDInsight tárolási architektúra")

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer a teljes URI használatával érhető el, például:

    hdfs://<namenodehost>/<path>

A HDInsight segítségével az Azure Storage-ban is hozzáférhet az adatokhoz. A szintaxis a következő:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Az Azure Storage-fiók HDInsight-fürtökkel való használatakor vegye figyelembe az alábbi alapelveket:

* **Fürthöz csatlakozó tárolók a tárfiókokban:** Mivel a fiók neve és kulcsa társítva van a fürttel a létrehozás során, teljes hozzáféréssel rendelkezik a tárolókban lévő összes blobhoz.

* **Nyilvános tárolók vagy nyilvános blobok a fürthöz *nem* kapcsolódó tárfiókokban:** Csak olvasási engedéllyel rendelkezik a tárolókban lévő blobokhoz.
  
  > [!NOTE]  
  > Nyilvános tárolók lehetővé teszi, hogy egy listát az összes blobok, amelyek a tárolóban elérhető, és a tároló metaadatok beszerzése. A nyilvános blobok esetén csak akkor érheti el a blobokat, ha ismeri a pontos URL-t. További információt a [Névtelen olvasási hozzáférés kezelése a tárolókhoz és blobokhoz című témakörben talál.](../storage/blobs/storage-manage-access-to-resources.md)

* **Fürthöz *nem* kapcsolódó tárolók magántárolói:** A tárolókban lévő blobok csak akkor férhetnek hozzá a tárolókban, ha a WebHCat-feladatok elküldésekor definiálja a tárfiókot. 

A létrehozási folyamat során meghatározott tárfiókok és a kulcsaik a %HADOOP_HOME%/conf/core-site.xml fájlban találhatók a fürtcsomópontokon. Alapértelmezés szerint a HDInsight a core-site.xml fájlban definiált tárfiókokat használja. Ezt a beállítást az [Apache Ambari segítségével módosíthatja.](./hdinsight-hadoop-manage-ambari.md)

Több WebHCat-feladat, köztük az Apache Hive, a MapReduce, az Apache Hadoop streaming és az Apache Pig, hordozhatja a tárfiókok és a metaadatok leírását. (Ez jelenleg a tárfiókkal rendelkező Pig esetében igaz, metaadatokesetén azonban nem.) További információ: [HDInsight-fürt használata alternatív tárfiókokkal és metaáruházakkal.](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)

A blobok a strukturált és strukturálatlan adatokhoz használhatók. A Blob-tárolók kulcs-/értékpárokként tárolják az adatokat, és nem rendelkeznek címtárhierarchiával. A kulcsnév azonban tartalmazhat perjel karaktert ( / ), hogy úgy tűnjön, mintha egy fájl egy könyvtárstruktúrában lenne tárolva. Például egy blob kulcsa `input/log1.txt`lehet. Nincs `input` tényleges könyvtár, de a kulcsnevében lévő perjel miatt a kulcs fájlelérési útnak tűnik.

### <a name="benefits-of-azure-storage"></a><a id="benefits"></a>Az Azure Storage előnyei
A nem közös helyen található számítási fürtök és tárolási erőforrások implicit teljesítményköltségekkel járnak. Ezeket a költségeket mérséklődik a számítási fürtök az Azure-régión belüli tárfiók-erőforrások közelében történő létrehozásának módja. Ebben a régióban a számítási csomópontok hatékonyan hozzáférhetnek az adatokhoz az Azure Storage-on belüli nagy sebességű hálózaton keresztül.

Ha az adatokat az Azure Storage-ban tárolja a HDFS helyett, számos előnnyel jár:

* **Adatok újbóli használata és megosztása:** A HDFS-ben az adatok a számítási fürtön belül találhatóak. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Ezzel szemben az Azure Storage-ban tárolt adatok a HDFS API-kon vagy a Blob storage REST API-kon keresztül érhetők el. Ezen elrendezés miatt az alkalmazások (beleértve más HDInsight-fürtöket is) és eszközök nagyobb készlete használható az adatok előállításához és felhasználásához.

* **Adatarchiválás:** Ha az adatokat az Azure Storage tárolja, a hdinsight-fürtök számítási biztonságosan törölhetők felhasználói adatok elvesztése nélkül.

* **Adattárolási költség:** Az adatok dfs-ben való hosszú távú tárolása költségesebb, mint az adatok tárolása az Azure Storage-ban, mivel a számítási fürt költsége magasabb, mint az Azure Storage költsége. Emellett mivel az adatokat nem kell újratölteni minden számítási fürt létrehozásakor, az adatbetöltési költségeket is megtakaríthatja.

* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a léptéket a fürthöz létrehozott csomópontok száma határozza meg. A méretezés módosítása bonyolultabb lehet, mint az Azure Storage-ban automatikusan megkapandó rugalmas skálázási képességekre támaszkodva.

* **Georeplikáció:** Az Azure Storage georeplikált lehet. Bár a georeplikáció földrajzi helyreállítást és adatredundanciát biztosít, a georeplikált helyre történő feladatátvétel súlyosan befolyásolja a teljesítményt, és további költségekkel járhat. Ezért körültekintően válassza a georeplikációt, és csak akkor, ha az adatok értéke indokolja a többletköltséget.

Bizonyos MapReduce feladatok és csomagok köztes eredményeket hozhatnak létre, amelyeket nem szeretne tárolni az Azure Storage-ban. Ebben az esetben választhatja az adatokat a helyi HDFS-ben tárolhatja. A HDInsight a DFS-t használja a Hive-feladatokban és más folyamatokban elért több köztes eredményhez.

> [!NOTE]  
> A legtöbb HDFS-parancs `ls` `copyFromLocal`(például , és `mkdir`) a várt módon működik az Azure Storage-ban. Csak a natív HDFS-implementációra jellemző parancsok (amelyet DFS-nek neveznek), például `fschk` és `dfsadmin`a , különböző viselkedést mutatnak az Azure Storage-ban.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Az Azure Data Lake storage gen1 áttekintése

Az Azure Data Lake Storage Gen1 egy nagyvállalati szintű, nagyméretű tárház big data-elemzési számítási feladatokhoz. Az Azure Data Lake használatával bármilyen méretű, típusú és betöltési sebességű adatokat rögzíthet egy helyen a működési és feltáró elemzések hez.

A Data Lake Storage Gen1 elérése a Hadoopból (hdinsight-fürtesetén érhető el) a WebHDFS-kompatibilis REST API-k használatával. A Data Lake Storage Gen1 a tárolt adatok elemzésének engedélyezésére szolgál, és az adatelemzési forgatókönyvek ben a teljesítményre van hangolva. A beépített rendszer tartalmazza azokat a képességeket, amelyek elengedhetetlenek a valós vállalati használati esetekhez. Ezek közé tartozik a biztonság, a kezelhetőség, a méretezhetőség, a megbízhatóság és a rendelkezésre állás.

Az Azure Data Lake Storage Gen1 szolgáltatásról az [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md)részletes áttekintése című témakörben olvashat bővebben.

A Data Lake Storage Gen1 legfontosabb képességei a következők.

### <a name="compatibility-with-hadoop"></a>Kompatibilitás a Hadoop-mal

A Data Lake Storage Gen1 egy Apache Hadoop fájlrendszer, amely kompatibilis a HDFS-sel, és együttműködik a Hadoop ökoszisztémával.  A WebHDFS API-t használó meglévő HDInsight-alkalmazások vagy -szolgáltatások könnyen integrálhatók a Data Lake Storage Gen1 szolgáltatással. A Data Lake Storage Gen1 egy WebHDFS-kompatibilis REST-felületet is elérhetővé teszi az alkalmazások számára.

A Data Lake Storage Gen1-ben tárolt adatok könnyen elemezhetők a Hadoop analitikus keretrendszerek, például a MapReduce vagy a Hive használatával. Az Azure HDInsight-fürtök kiépíthetők és konfigurálhatók úgy, hogy közvetlenül hozzáférjenek a Data Lake Storage Gen1-ben tárolt adatokhoz.

### <a name="unlimited-storage-petabyte-files"></a>Korlátlan tárterület, petabájtnyi fájlok

A Data Lake Storage Gen1 korlátlan tárhelyet biztosít, és számos adat tárolására alkalmas az elemzéshez. Nem korlátozza a fiókméretet, a fájlméretet vagy az adattóban tárolható adatok mennyiségét. Az egyes fájlok mérete a kilobájttól a petabájtig terjedhet, így a Data Lake Storage Gen1 nagyszerű választás bármilyen típusú adat tárolására. Az adatok tárolása tartósan több másolat készítésével történik, és nincs korlátozva, hogy az adatok mennyi ideig tárolhatók az adattóban.

### <a name="performance-tuning-for-big-data-analytics"></a>Teljesítményhangolás big data-elemzéshez

A Data Lake Storage Gen1 nagyméretű analitikus rendszerek futtatására készült, amelyek nagy mennyiségű adat lekérdezéséhez és elemzéséhez nagy mennyiségű adat lekérdezéséhez és elemzéséhez szükséges. A data lake a fájl egyes részeit több egyedi tárolókiszolgálóra is elosztja. Adatok elemzésekor ez a beállítás javítja az olvasási átviteli szintet, ha a fájlt párhuzamosan olvassa be.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Vállalati használatra való felkészültség: Magas rendelkezésre állás és biztonság

A Data Lake Storage Gen1 iparági szabványoknak megfelelő elérhetőséget és megbízhatóságot biztosít. Az adatelemek tárolása tartósan: a redundáns másolatok nem várt hibák ellen. A vállalatok a Data Lake Storage Gen1-et a meglévő adatplatformjuk fontos részeként használhatják a megoldásaikban.

A Data Lake Storage Gen1 nagyvállalati szintű biztonságot is nyújt a tárolt adatok számára. További információ: [Adatok védelme az Azure Data Lake Storage Gen1](#DataLakeStoreSecurity)alkalmazásban.

### <a name="flexible-data-structures"></a>Rugalmas adatstruktúrák

A Data Lake Storage Gen1 bármilyen adatot tárolhat natív formátumban, ahogy van, előzetes átalakítások nélkül. A Data Lake Storage Gen1 nem igényel sémát az adatok betöltése előtt. Az egyes analitikus keretrendszer értelmezi az adatokat, és meghatározza a sémát az elemzés időpontjában. Mivel tetszőleges méretű és formátumú fájlokat képes tárolni, a Data Lake Storage Gen1 képes kezelni a strukturált, félstrukturált és strukturálatlan adatokat.

Data Lake Storage Gen1 tárolók adatok lényegében mappák és fájlok. A tárolt adatok on sdks, az Azure Portal és az Azure PowerShell használatával működik. Mindaddig, amíg az adatokat az áruházba ezek a felületek és a megfelelő tárolók használatával helyezi el, bármilyen típusú adatot tárolhat. A Data Lake Storage Gen1 nem végez speciális adatkezelést az általa tárolt adatok típusa alapján.

## <a name="data-security-in-data-lake-storage-gen1"></a><a name="DataLakeStoreSecurity"></a>Adatbiztonság a Data Lake Storage Gen1 szolgáltatásban
A Data Lake Storage Gen1 az Azure Active Directoryt használja a hitelesítéshez, és hozzáférés-vezérlési listákat (ACL-eket) használ az adatokhoz való hozzáférés kezeléséhez.

| **Szolgáltatás** | **Leírás** |
| --- | --- |
| Hitelesítés |A Data Lake Storage Gen1 integrálható az Azure Active Directoryval (Azure AD) a Data Lake Storage Gen1-ben tárolt összes adat identitás- és hozzáférés-kezeléséhez. Az integráció miatt a Data Lake Storage Gen1 az Azure AD összes funkciójának előnyeit élvezi. Ezek közé tartozik a többtényezős hitelesítés, a feltételes hozzáférés, a szerepköralapú hozzáférés-vezérlés, az alkalmazáshasználat figyelése, a biztonsági figyelés és riasztás, és így tovább. A Data Lake Storage Gen1 támogatja az OAuth 2.0 protokollt a REST-csatolón belüli hitelesítéshez. Lásd: [Hitelesítés az Azure Data Lake Storage Gen1 szolgáltatásban az Azure Active Directory használatával](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Hozzáférés-vezérlés |A Data Lake Storage Gen1 a WebHDFS protokoll által elérhetővé tett POSIX-stílusú engedélyek támogatásával biztosít hozzáférés-vezérlést. A hozzáférés-vezérlési listák a gyökérkönyvtáron, az alkönyvtárakon és az egyes fájlokon is engedélyezhetők. Az ACL-ek működéséről a Data Lake Storage Gen1 környezetében további információt a [Hozzáférés-vezérlés a Data Lake Storage Gen1 programban című témakörben talál.](../data-lake-store/data-lake-store-access-control.md) |
| Titkosítás |A Data Lake Storage Gen1 titkosítást is biztosít a fiókban tárolt adatokhoz. A titkosítási beállításokat a Data Lake Storage Gen1 fiók létrehozásakor adja meg. Választhatja az adatok titkosítását, vagy választhatja a nem titkosítást. További információ: [Encryption in Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). A titkosítással kapcsolatos konfigurációk biztosításáról az [Azure Data Lake Storage Gen1 használatának első lépései az Azure Portal használatával című témakörben](../data-lake-store/data-lake-store-get-started-portal.md)talál. |

Ha többet szeretne tudni az adatok védelméről a Data Lake Storage Gen1 szolgáltatásban, olvassa el [az Azure Data Lake Storage Gen1 szolgáltatásban tárolt adatok védelme.](../data-lake-store/data-lake-store-secure-data.md)

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>A Data Lake Storage Gen1-tel kompatibilis alkalmazások
A Data Lake Storage Gen1 kompatibilis a Hadoop ökoszisztéma legtöbb nyílt forráskódú összetevőjével. Emellett egyéb Azure-szolgáltatásokkal is jól integrálható.  Az alábbi hivatkozásokra kattintva többet is megtudhat arról, hogyan használható a Data Lake Storage Gen1 a nyílt forráskódú összetevőkkel és más Azure-szolgáltatásokkal.

* Az [Azure Data Lake Storage Gen1 szolgáltatással működő nyílt forráskódú big data-alkalmazások 1.](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)
* Az [Azure Data Lake Storage Gen1 integrálása más Azure-szolgáltatásokkal](../data-lake-store/data-lake-store-integrate-with-other-services.md) a Data Lake Storage Gen1 más Azure-szolgáltatásokkal való használatának megértéséhez a forgatókönyvek szélesebb körének engedélyezéséhez.
* A Data [Lake Storage Gen1 használatával a Big Data Storage](../data-lake-store/data-lake-store-data-scenarios.md) Gen1 használatával megtudhatja, hogyan használhatja a Data Lake Storage Gen1-et olyan forgatókönyvekben, mint az adatok betöltése, az adatok feldolgozása, az adatok letöltése és az adatok megjelenítése.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 fájlrendszer (adl://)
Hadoop-környezetekben (HDInsight-fürttel érhető el) az új fájlrendszeren, az AzureDataLakeFilesystemen (adl://) keresztül érheti el a Data Lake Storage Gen1 szolgáltatást. A adl:// használó alkalmazások és szolgáltatások teljesítménye olyan módon optimalizálható, amely jelenleg nem érhető el a WebHDFS fájlrendszerben. Ennek eredményeképpen a Data Lake Storage Gen1 használataesetén rugalmasságot kap, hogy az ajánlott adl:// használatával a legjobb teljesítményt nyújtsa, vagy a meglévő kódot a WebHDFS API közvetlen használatával továbbra is használja. Az Azure HDInsight teljes mértékben kihasználja az AzureDataLakeFilesystem előnyeit, hogy a legjobb teljesítményt nyújtsa a Data Lake Storage Gen1 szolgáltatáson.

A Data Lake Storage Gen1 szolgáltatásban tárolt adatok az alábbi használatával érhetők el:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

A Data Lake Storage Gen1 szolgáltatásban található adatok eléréséről [a Tárolt adatokon elérhető műveletek című](../data-lake-store/data-lake-store-get-started-portal.md#properties)témakörben talál további információt.



## <a name="next-steps"></a>További lépések

* [Bevezetés az Azure Data Lake Storage Gen2 szolgáltatásba](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)

---
title: A tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használathoz
description: Áttekintést nyújt a tárolási típusokról, valamint arról, hogyan működnek együtt az Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 98d71434ac9e3f712be0cbd8c505b7d5a537e7cc
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79095546"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>A tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használathoz

HDInsight-fürtök létrehozásakor több különböző Azure Storage-szolgáltatás közül választhat:

* Azure Storage
* 2\. generációs Azure Data Lake Storage
* 1\. generációs Azure Data Lake Storage

Ez a cikk áttekintést nyújt ezekről a tárolási típusokról és azok egyedi funkcióiról.

A következő táblázat összefoglalja a HDInsight különböző verziói által támogatott Azure Storage-szolgáltatásokat:

| Tárolási szolgáltatás | Fiók típusa | Névtér típusa | Támogatott szolgáltatások | Támogatott teljesítményszint | Támogatott hozzáférési szintek | HDInsight verziója | Fürt típusa |
|---|---|---|---|---|---|---|---|
|2\. generációs Azure Data Lake Storage| Általános célú v2 | Hierarchikus (fájlrendszer) | Blob | Standard | Gyors elérésű, ritka elérésű Archívum | 3.6. + | Mind a Spark 2,1, mind a 2,2 kivételével|
|Azure Storage| Általános célú v2 | Objektum | Blob | Standard | Gyors elérésű, ritka elérésű Archívum | 3.6. + | Összes |
|Azure Storage| Általános célú v1 | Objektum | Blob | Standard | N/A | Összes | Összes |
|Azure Storage| Blob Storage** | Objektum | BLOB letiltása | Standard | Gyors elérésű, ritka elérésű Archívum | Összes | Összes |
|1\. generációs Azure Data Lake Storage| N/A | Hierarchikus (fájlrendszer) | N/A | N/A | N/A | 3,6 csak | A HBase kivételével |

\* * A HDInsight-fürtök esetében csak a másodlagos Storage-fiókok lehetnek BlobStorage, és az oldal Blobja nem támogatott tárolási lehetőség.

Az Azure Storage-fiókok típusairól további információt az [Azure Storage-fiók áttekintése](../storage/common/storage-account-overview.md) című témakörben talál.

További információ az Azure Storage hozzáférési szintjeiről [: Azure Blob Storage: Premium (előzetes verzió), gyakori, ritka elérésű és archív tárolási szintek](../storage/blobs/storage-blob-storage-tiers.md)

Létrehozhat egy fürtöt az elsődleges és választható másodlagos tárolók különböző szolgáltatásainak kombinációi használatával. A következő táblázat összefoglalja a HDInsight által jelenleg támogatott fürtök tárolási konfigurációit:

| HDInsight verziója | Elsődleges tárterület | Másodlagos tároló | Támogatott |
|---|---|---|---|
| 3,6 & 4,0 | Általános célú v1, általános célú v2 | Általános célú v1, általános célú v2, BlobStorage (blokk Blobok) | Igen |
| 3,6 & 4,0 | Általános célú v1, általános célú v2 | 2\. generációs Data Lake Storage | Nem |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | 2\. generációs Data Lake Storage | Igen |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Általános célú v1, általános célú v2, BlobStorage (blokk Blobok) | Igen |
| 3,6 & 4,0 | 2\. generációs Data Lake Storage | 1\. generációs Data Lake Storage | Nem |
| 3.6 | 1\. generációs Data Lake Storage | 1\. generációs Data Lake Storage | Igen |
| 3.6 | 1\. generációs Data Lake Storage | Általános célú v1, általános célú v2, BlobStorage (blokk Blobok) | Igen |
| 3.6 | 1\. generációs Data Lake Storage | 2\. generációs Data Lake Storage | Nem |
| 4.0 | 1\. generációs Data Lake Storage | Bármelyik | Nem |
| 4.0 | Általános célú v1, általános célú v2 | 1\. generációs Data Lake Storage | Nem |

\* = Ez lehet egy vagy több Data Lake Storage Gen2 fiók is, feltéve, hogy mindegyik telepítő ugyanazt a felügyelt identitást használja a fürt eléréséhez.

> [!Note] 
> Data Lake Storage Gen2 elsődleges tároló nem támogatott a Spark 2,1 vagy 2,2 fürtök esetében. 

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Azure Data Lake Storage Gen2 használata az Azure HDInsight Apache Hadoop

A Azure Data Lake Storage Gen2 a Azure Data Lake Storage Gen1 alapvető funkcióit veszi át, és integrálja őket az Azure Blob Storage-ba. Ezek a funkciók olyan fájlrendszert tartalmaznak, amely kompatibilis a Hadoop, a Azure Active Directory (Azure AD) és a POSIX-alapú hozzáférés-vezérlési listákkal (ACL-ekkel). Ez a kombináció lehetővé teszi, hogy kihasználhassa a Azure Data Lake Storage Gen1 teljesítményét, miközben a blob Storage rétegbeli és adatkezelési életciklus-kezelését is használja.

További információ a Azure Data Lake Storage Gen2ről: a [Azure Data Lake Storage Gen2 bemutatása](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>A Azure Data Lake Storage Gen2 alapvető funkciói

* A **Hadoop kompatibilis hozzáférés:** A Azure Data Lake Storage Gen2ban ugyanúgy kezelheti és érheti el az adatelérést, mint egy Hadoop-elosztott fájlrendszer (HDFS). Az Azure Blob fájlrendszer (ABFS) illesztőprogramja minden Apache Hadoop környezetben elérhető, beleértve az Azure HDInsight és a Azure Databricks. A ABFS használatával férhet hozzá a Data Lake Storage Gen2 tárolt adatszolgáltatásokhoz.

* **POSIX-engedélyek felülbírálása:** A Data Lake Gen2 biztonsági modellje támogatja az ACL és a POSIX engedélyeket, valamint a Data Lake Storage Gen2ra vonatkozó további részletességet. A beállítások a felügyeleti eszközök vagy keretrendszerek, például a Apache Hive és a Apache Spark használatával konfigurálhatók.

* **Költséghatékonyság:** A Data Lake Storage Gen2 alacsony költséghatékonyságú tárolási kapacitást és tranzakciókat biztosít. Az Azure Blob Storage életciklusának funkciói lehetővé teszik az alacsonyabb költségek csökkentését azáltal, hogy a számlázási sebességet az életciklusán keresztül mozgatják.

* **A blob Storage-eszközökkel,-keretrendszerekkel és-alkalmazásokkal való kompatibilitás:** Data Lake Storage Gen2 továbbra is együttműködik számos eszközzel, keretrendszert és alkalmazást a blob Storage-hoz.

* **Optimalizált illesztőprogram:** A ABFS-illesztőprogram kifejezetten big data elemzésekhez van optimalizálva. A megfelelő REST API-k az elosztott fájlrendszer (DFS) végpontján (dfs.core.windows.net) vannak felszínben.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>A 2. generációs Azure Data Lake Storage újdonságai

#### <a name="managed-identities-for-secure-file-access"></a>Felügyelt identitások biztonságos fájlokhoz való hozzáféréshez

Az Azure HDInsight felügyelt identitásokat használ a Azure Data Lake Storage Gen2 lévő fájlokhoz való hozzáférés biztonságossá tételéhez. A felügyelt identitások olyan Azure Active Directory funkciói, amelyek automatikusan felügyelt hitelesítő adatokkal rendelkező Azure-szolgáltatásokat biztosítanak. Ezek a hitelesítő adatok a Active Directory hitelesítést támogató bármely szolgáltatás hitelesítésére használhatók. A felügyelt identitások használatához nem szükséges a hitelesítő adatok tárolása a kódban vagy a konfigurációs fájlokban.

További információ: [felügyelt identitások az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Azure Blob fájlrendszer-illesztőprogram

Apache Hadoop alkalmazások natívan várnak a helyi lemezes tárolóból származó adatok olvasására és írására. A Hadoop fájlrendszer-illesztőprogram, például a ABFS lehetővé teszi a Hadoop-alkalmazások számára a felhőalapú tárolást a normál Hadoop-fájlrendszer műveleteinek emulálása révén. Az illesztőprogram átalakítja az alkalmazásból kapott parancsokat olyan műveletekre, amelyeket az aktuális felhőalapú tárolási platform megértett.

Korábban a Hadoop fájlrendszer-illesztőprogram az összes fájlrendszerbeli műveletet átalakította az Azure Storage-REST API a hívások az ügyfél oldalán, majd meghívja a REST API. Ez az ügyféloldali átalakítás azonban több REST API hívását eredményezte egyetlen fájlrendszer-művelethez, például egy fájl átnevezéséhez. A ABFS áthelyezte a Hadoop fájlrendszer logikáját az ügyfél oldaláról a kiszolgáló oldalára. A Azure Data Lake Storage Gen2 API mostantól párhuzamosan fut a blob API-val. Ez a Migrálás javítja a teljesítményt, mivel a Hadoop-fájlrendszerrel kapcsolatos gyakori műveletek egy REST API hívással hajthatók végre.

További információkért lásd [Az Azure Blob fájlrendszer-illesztőprogramot (ABFS): a Hadoop dedikált Azure Storage-illesztőprogramját](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-séma a Azure Data Lake Storage Gen 2 

Azure Data Lake Storage Gen2 egy új URI-sémát használ az Azure Storage-ban lévő fájlok eléréséhez a HDInsight-ből:

`abfs://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Az URI-séma SSL-titkosítású hozzáférést biztosít.

`<FILE_SYSTEM_NAME>` a fájlrendszer Data Lake Storage Gen2 elérési útját azonosítja.

`<ACCOUNT_NAME>` azonosítja az Azure Storage-fiók nevét. Szükség van a teljes tartománynévre (FQDN-re).

`<PATH>` a fájl vagy a könyvtár HDFS elérési útja.

Ha `<FILE_SYSTEM_NAME>` és `<ACCOUNT_NAME>` érték nincs megadva, a rendszer az alapértelmezett fájlrendszert használja. Az alapértelmezett fájlrendszer fájljaihoz használjon relatív elérési útvonalat vagy abszolút elérési utat. Például a HDInsight-fürtökhöz tartozó `hadoop-mapreduce-examples.jar`-fájl a következő elérési utak egyikének használatával hívható meg:

```
abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfs:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> A fájlnév a 2,1-es és 1,6-es HDInsight-verziókban `hadoop-examples.jar`. Ha a HDInsight-en kívül található fájlokkal dolgozik, a legtöbb segédprogram nem ismeri fel a ABFS formátumát, hanem egy alapszintű elérésiút-formátumot vár, például `example/jars/hadoop-mapreduce-examples.jar`.

További információ: [a Azure Data Lake Storage GEN2 URI használata](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage egy robusztus általános célú tárolási megoldás, amely zökkenőmentesen integrálható a HDInsight. A HDInsight egy blobtárolót használhat az Azure Storage-ben a fürt alapértelmezett fájlrendszereként. A HDFS felületen keresztül a HDInsight-összetevők teljes készlete működhet közvetlenül a strukturált vagy strukturálatlan, blobként tárolt adaton.

Javasoljuk, hogy külön tároló tárolókat használjon az alapértelmezett fürtöt tárolóhoz és az üzleti adataihoz, hogy elkülönítse a HDInsight-naplókat és az ideiglenes fájlokat a saját üzleti adataitól. Javasoljuk továbbá, hogy törölje az alapértelmezett BLOB-tárolót, amely az alkalmazás-és rendszernaplókat tartalmazza, miután mindegyik használattal csökkenti a tárolási költségeket. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

Ha úgy dönt, hogy védi a Storage-fiókot a **tűzfalakkal és a virtuális hálózatokkal** kapcsolatos korlátozásokkal a **kiválasztott hálózatokon**, ügyeljen arra, hogy a kivételt engedélyezze a **megbízható Microsoft-szolgáltatások számára** ... így a HDInsight hozzáférhet a Storage-fiókhoz.

### <a name="hdinsight-storage-architecture"></a>HDInsight tároló-architektúra

Az alábbi ábra az Azure Storage HDInsight architektúrájának absztrakt nézetét tartalmazza:

![HDInsight-tár architektúra](./media/hdinsight-hadoop-compare-storage-options/storage-architecture.png "HDInsight-tár architektúra")

A HDInsight hozzáférést nyújt a helyileg a számítási csomópontokhoz csatlakozó elosztott fájlrendszerhez. Ez a fájlrendszer a teljes URI használatával érhető el, például:

    hdfs://<namenodehost>/<path>

A HDInsight-on keresztül az Azure Storage-ban is elérhetők az adatkészletek. A szintaxis a következő:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

HDInsight-fürtökkel rendelkező Azure Storage-fiók használata esetén vegye figyelembe a következő alapelveket:

* **Fürthöz csatlakozó tárolók a tárfiókokban:** Mivel a fiók neve és kulcsa társítva van a fürttel a létrehozás során, teljes hozzáféréssel rendelkezik a tárolókban lévő összes blobhoz.

* **Nyilvános tárolók vagy nyilvános Blobok olyan Storage-fiókokban, amelyek *nem* kapcsolódnak fürthöz:** Csak olvasási jogosultsággal rendelkezik a tárolóban lévő blobokhoz.
  
  > [!NOTE]  
  > A nyilvános tárolók lehetővé teszik az adott tárolóban elérhető Blobok listájának lekérését és a tároló metaadatainak beolvasását. A nyilvános blobok esetén csak akkor érheti el a blobokat, ha ismeri a pontos URL-t. További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](../storage/blobs/storage-manage-access-to-resources.md) foglalkozó témakört.

* Olyan **tárolók, amelyek *nem* csatlakoznak fürthöz:** A tárolóban lévő Blobok csak akkor érhetők el, ha a Webhcaten-feladatok elküldésekor a Storage-fiókot definiálja. 

A létrehozási folyamat során meghatározott tárfiókok és a kulcsaik a %HADOOP_HOME%/conf/core-site.xml fájlban találhatók a fürtcsomópontokon. Alapértelmezés szerint a HDInsight a Core-site. xml fájlban meghatározott tárolási fiókokat használja. Ezt a beállítást az [Apache Ambari](./hdinsight-hadoop-manage-ambari.md)használatával lehet módosítani.

Több Webhcaten-feladat, többek között a Apache Hive, a MapReduce, a Apache Hadoop streaming és az Apache Pig, a Storage-fiókok és-metaadatok leírását is elvégezheti. (Ez jelenleg csak a Storage-fiókkal rendelkező Pig esetében érvényes, metaadatok esetében nem.) További információ: [HDInsight-fürt használata másodlagos Storage-fiókokkal és-metaadattárak](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

A blobok a strukturált és strukturálatlan adatokhoz használhatók. A blob-tárolók az értékeket kulcs/érték párokként tárolják, és nem rendelkeznek címtár-hierarchiával. A kulcs neve azonban tartalmazhat perjel karaktert (/) úgy, hogy úgy jelenjen meg, mintha egy fájl egy könyvtár struktúráján belül tárolódik. A blob kulcsa például lehet `input/log1.txt`. Nem létezik tényleges `input` könyvtár, de a kulcsnévben lévő perjel karakter miatt a kulcs egy fájl elérési útjára hasonlít.

### <a id="benefits"></a>Az Azure Storage előnyei
A nem közösen elhelyezett számítási fürtök és tárolási erőforrások implicit teljesítménnyel kapcsolatos költségekkel rendelkeznek. Ezeket a költségeket a számítási fürtök az Azure-régióban található Storage-fiók erőforrásaihoz közeledő módon történő létrehozásával csökkentik. Ebben a régióban a számítási csomópontok hatékonyan férhetnek hozzá az adatokhoz az Azure Storage-on belüli nagy sebességű hálózaton keresztül.

Ha a HDFS helyett az Azure Storage-ban tárolja az adattárolást, számos előnnyel jár:

* **Adatok újbóli használata és megosztása:** A HDFS-ben az adatok a számítási fürtön belül találhatóak. Csak a számítási fürtöt elérő alkalmazások használhatják az adatokat HDFS API-k használatával. Az Azure Storage-ban tárolt adatok ezzel szemben a HDFS API-kon vagy a blob Storage REST API-kon keresztül érhetők el. Ennek a megoldásnak a miatt a nagyobb alkalmazások (beleértve az egyéb HDInsight-fürtöket) és az eszközök is használhatók az adatfeldolgozásra és-felhasználásra.

* **Adatarchiválás:** Ha az Azure Storage-ban tárolják az adattárolást, a számításhoz használt HDInsight-fürtöket a felhasználói adatvesztés nélkül lehet biztonságosan törölni.

* **Adattárolási díj:** Az adatok elosztott fájlrendszerbeli tárolásának hosszú távú tárolása költségesebb, mint az adatok Azure Storage-tárolóban való tárolása, mert a számítási fürt díja meghaladja az Azure Storage költségeit. Továbbá, mivel az adatoknak nem kell újratölteniük minden számítási fürt létrehozásakor, az adattöltés költségeit is menteni kell.

* **Rugalmas kibővítés:** Bár a HDFS kibővített fájlrendszert biztosít, a léptéket a fürthöz létrehozott csomópontok száma határozza meg. A skála módosítása bonyolultabb lehet, mint az Azure Storage-ban automatikusan elérhető rugalmas skálázási funkciókra támaszkodva.

* **Geo-replikáció:** Az Azure Storage-t földrajzilag lehet replikálni. Bár a Geo-replikáció földrajzi helyreállítást és adatredundanciát biztosít, a földrajzilag replikált helyre történő feladatátvétel jelentősen befolyásolja a teljesítményt, és további költségeket eredményezhet. Ezért válassza a Geo-replikációt óvatosan, és csak akkor, ha az adatmennyiség indokolja a további költségeket.

Bizonyos MapReduce-feladatok és csomagok olyan köztes eredményeket hozhatnak létre, amelyeket nem szeretne az Azure Storage-ban tárolni. Ebben az esetben dönthet úgy, hogy az adattárolást a helyi HDFS tárolja. A HDInsight az elosztott fájlrendszert használja több ilyen köztes eredményhez a kaptár-feladatokban és más folyamatokban.

> [!NOTE]  
> A legtöbb HDFS-parancs (például `ls`, `copyFromLocal`és `mkdir`) a várt módon működik az Azure Storage-ban. Az Azure Storage-ban csak a natív HDFS-implementációra (azaz a DFS-re) jellemző parancsokra, például a `fschk`ra és a `dfsadmin`re van mód.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>A Azure Data Lake Storage Gen1 áttekintése

A Azure Data Lake Storage Gen1 egy nagyvállalati szintű nagy kapacitású adattár big data analitikus számítási feladatokhoz. A Azure Data Lake használatával bármilyen méretű, típusú és feldolgozási sebességű adatot rögzíthet egy helyen az operatív és a felderítő elemzéshez.

A WebHDFS-kompatibilis REST API-k használatával férhet hozzá Data Lake Storage Gen1 a Hadoop (HDInsight-fürthöz elérhető). Data Lake Storage Gen1 úgy lett kialakítva, hogy lehetővé tegye az elemzést a tárolt adatokon, és az adatelemzési forgatókönyvekben teljesítményre hangolva. A jelölőnégyzetből megtalálhatja azokat a képességeket, amelyek elengedhetetlenek a valós nagyvállalati használati esetekhez. A képességek közé tartozik a biztonság, a kezelhetőség, a méretezhetőség, a megbízhatóság és a rendelkezésre állás.

További információ a Azure Data Lake Storage Gen1ről: [Azure Data Lake Storage Gen1 részletes áttekintése](../data-lake-store/data-lake-store-overview.md).

A Data Lake Storage Gen1 főbb képességei a következők:

### <a name="compatibility-with-hadoop"></a>Kompatibilitás a Hadoop

Data Lake Storage Gen1 egy Apache Hadoop fájlrendszer, amely kompatibilis a HDFS, és együttműködik a Hadoop-ökoszisztémával.  A WebHDFS API-t használó meglévő HDInsight-alkalmazások és-szolgáltatások könnyen integrálhatók Data Lake Storage Gen1val. A Data Lake Storage Gen1 a WebHDFS-kompatibilis REST-felületet is elérhetővé teszi az alkalmazásokhoz.

A Data Lake Storage Gen1ban tárolt adatai könnyen elemezhetők a Hadoop analitikai keretrendszerek, például a MapReduce vagy a kaptár használatával. Az Azure HDInsight-fürtök üzembe helyezhetők és konfigurálhatók úgy, hogy közvetlenül hozzáférhessenek Data Lake Storage Gen1ban tárolt adatszolgáltatásokhoz.

### <a name="unlimited-storage-petabyte-files"></a>Korlátlan tárterület, petabájtnyi fájlok

Data Lake Storage Gen1 korlátlan tárhelyet biztosít, és alkalmas a különböző adatelemzések tárolására. Nem korlátozza a fiók méreteit, a fájlméretet, illetve a adattóban tárolható adatmennyiséget. Az egyes fájlok mérete kilobájttól a petabájt-ig terjedhet, így a Data Lake Storage Gen1 kiváló választás bármilyen típusú adatok tárolására. A rendszer több másolat készítésével tárolja az adattárolási tartósan, és nem korlátozza, hogy mennyi ideig tárolhatók az adatközpontok.

### <a name="performance-tuning-for-big-data-analytics"></a>Teljesítmény-Finomhangolás a big data elemzésekhez

Data Lake Storage Gen1 a nagy mennyiségű adatok lekérdezéséhez és elemzéséhez szükséges nagy teljesítményű analitikai rendszerek futtatására készült. A adat-tó több különálló Storage-kiszolgálón is elterjed egy fájl részein. Az adatok elemzésekor ez a telepítő javítja az olvasási sebességet, ha a fájl párhuzamosan van beolvasva.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Nagyvállalati készültség: kiválóan elérhető és biztonságos

A Data Lake Storage Gen1 iparági szintű rendelkezésre állást és megbízhatóságot biztosít. Az adategységek tárolása tartósan történik: a redundáns másolatok megvédik a váratlan hibákat. A vállalatok a meglévő adatplatformjuk fontos részeként használhatják a Data Lake Storage Gen1 a megoldásokban.

A Data Lake Storage Gen1 nagyvállalati szintű biztonságot is biztosít a tárolt adattároláshoz. További információ: az [adatok biztonságossá tétele a Azure Data Lake Storage Gen1ban](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Rugalmas adatstruktúrák

A Data Lake Storage Gen1 a natív formátumban tárolhatja az összes olyan adatforrást, amely korábbi átalakítások nélkül is használható. Data Lake Storage Gen1 nem szükséges sémát meghatározni az betöltés előtt. Az egyéni elemzési keretrendszer értelmezi az adatelemzést, és definiál egy sémát az elemzés időpontjában. Mivel az képes tetszőleges méretű és formátumú fájlokat tárolni, a Data Lake Storage Gen1 képes kezelni a strukturált, a félig strukturált és a strukturálatlan adatmennyiséget.

Data Lake Storage Gen1 tárolók alapvetően mappák és fájlok. A tárolt adatai SDK-k, a Azure Portal és a Azure PowerShell használatával működnek. Ha ezeket a csatolókat és a megfelelő tárolókat használva helyezi el az adatait a tárolóba, bármilyen típusú adatait tárolhatja. Data Lake Storage Gen1 nem végez semmilyen speciális adatkezelést az általa tárolt adatok típusától függően.

## <a name="DataLakeStoreSecurity"></a>Adatbiztonság a Data Lake Storage Gen1ban
A Data Lake Storage Gen1 a hitelesítéshez Azure Active Directory használ, és hozzáférés-vezérlési listákat (ACL-eket) használ az adataihoz való hozzáférés kezelésére.

| **Funkció** | **Leírás** |
| --- | --- |
| Authentication |A Data Lake Storage Gen1 az Azure Active Directory (Azure AD) szolgáltatással integrálható az identitás-és hozzáférés-kezeléshez a Data Lake Storage Gen1 tárolt összes adattal. Az integráció miatt Data Lake Storage Gen1 az Azure AD összes funkciójának előnyeit. Ezen funkciók közé tartozik a többtényezős hitelesítés, a feltételes hozzáférés, a szerepköralapú hozzáférés-vezérlés, az alkalmazások használatának figyelése, a biztonsági monitorozás és a riasztások. A Data Lake Storage Gen1 támogatja a OAuth 2,0 protokollt a REST-felületen belüli hitelesítéshez. Lásd: [Azure Data Lake Storage Gen1on belüli hitelesítés Azure Active Directory használatával](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)|
| Hozzáférés-vezérlés |Data Lake Storage Gen1 hozzáférés-vezérlést biztosít a WebHDFS protokoll által közzétett POSIX stílusú engedélyek támogatásával. A hozzáférés-vezérlési listák a gyökérkönyvtáron, az alkönyvtárakon és az egyes fájlokon is engedélyezhetők. Az ACL-ek Data Lake Storage Gen1 környezetében való működésével kapcsolatos további információkért lásd: [Data Lake Storage Gen1 hozzáférés-vezérlése](../data-lake-store/data-lake-store-access-control.md). |
| Titkosítás |A Data Lake Storage Gen1 a fiókban tárolt adathalmazok titkosítását is biztosítja. Data Lake Storage Gen1 fiók létrehozásakor megadhatja a titkosítási beállításokat. Megadhatja, hogy az adatai titkosítva legyenek, vagy a titkosítás nélkül is legyenek kiválasztva. További információ: [titkosítás Data Lake Storage Gen1ban](../data-lake-store/data-lake-store-encryption.md). A titkosítással kapcsolatos konfiguráció megadására vonatkozó utasításokért lásd: [a Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](../data-lake-store/data-lake-store-get-started-portal.md). |

Ha többet szeretne megtudni az Data Lake Storage Gen1-beli adatvédelmet illetően, tekintse meg [a Azure Data Lake Storage Gen1 tárolt adatainak biztonságossá](../data-lake-store/data-lake-store-secure-data.md)tételét ismertető témakört.

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1-kompatibilis alkalmazások
A Data Lake Storage Gen1 kompatibilis a Hadoop-ökoszisztémában található legtöbb nyílt forráskódú összetevővel. Emellett egyéb Azure-szolgáltatásokkal is jól integrálható.  Az alábbi hivatkozásokat követve további információkat tudhat meg arról, hogyan használhatók a Data Lake Storage Gen1 a nyílt forráskódú összetevőkkel és más Azure-szolgáltatásokkal is.

* A Data Lake Storage Gen1val együttműködő nyílt forráskódú alkalmazások listájának megtekintéséhez tekintse [meg a nyílt forráskódú Big Data a Azure Data Lake Storage Gen1t használó alkalmazásokat](../data-lake-store/data-lake-store-compatible-oss-other-applications.md) .
* Tekintse meg a [Azure Data Lake Storage Gen1 integrációja más Azure-szolgáltatásokkal](../data-lake-store/data-lake-store-integrate-with-other-services.md) című témakört, amelyből megtudhatja, hogyan Data Lake Storage Gen1 használhatja más Azure-szolgáltatásokkal a különböző forgatókönyvek széles skáláját.
* Lásd: a [Azure Data Lake Storage Gen1 használata Big Data követelményekhez](../data-lake-store/data-lake-store-data-scenarios.md) , amelyből megtudhatja, hogyan használhatja az Data Lake Storage Gen1 olyan helyzetekben, mint például az adatok betöltése, az adatok feldolgozása, az adatok letöltése és az adatok megjelenítése.

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 fájlrendszer (adl://)
A Hadoop-környezetekben (HDInsight-fürttel elérhető) az új fájlrendszer, a Azuredatalakefilesystemet (adl://) segítségével érheti el Data Lake Storage Gen1. A adl://-t használó alkalmazások és szolgáltatások teljesítménye olyan módon optimalizálható, amely jelenleg nem érhető el a WebHDFS-ben. Ennek eredményeképpen a Data Lake Storage Gen1 használatakor a rugalmasságot úgy érheti el, hogy a javasolt adl://használja a legjobb teljesítményt, vagy megtartja a meglévő kódokat, ha továbbra is közvetlenül használja a WebHDFS API-t. Az Azure HDInsight teljes mértékben kihasználja a Azuredatalakefilesystemet, hogy a lehető legjobb teljesítményt nyújtsa Data Lake Storage Gen1on.

A következő lépésekkel érheti el Data Lake Storage Gen1 adatait:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

A Data Lake Storage Gen1 lévő adatok elérésével kapcsolatos további információkért lásd: [a tárolt adatokon elérhető műveletek](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Data Lake Storage Gen2ba](../storage/blobs/data-lake-storage-introduction.md)
* [A Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md)

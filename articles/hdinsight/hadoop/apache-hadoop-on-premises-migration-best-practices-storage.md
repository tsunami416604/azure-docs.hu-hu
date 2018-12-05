---
title: A helyszíni Apache Hadoop-fürtök áttelepítése Azure HDInsight - storage ajánlott eljárásai
description: Ismerje meg a tároló áttelepítése a helyszíni Hadoop-fürtöket az Azure HDInsight ajánlott eljárásai.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 5edaf056dd28d132c8abeb148464f5b07bb9265e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874787"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---storage-best-practices"></a>A helyszíni Apache Hadoop-fürtök áttelepítése Azure HDInsight - storage ajánlott eljárásai

Ez a cikk az Azure HDInsight rendszerben ajánlásait adattárolásra biztosít. Ez azt egy olyan sorozat részét, amely ajánlott eljárásokat, amelyek segítik az Azure HDInsight áttelepítése a helyszíni Apache Hadoop-rendszerekhez biztosít.

## <a name="choose-the-right-storage-system-for-hdinsight-clusters"></a>Válassza ki a megfelelő tárolási rendszert, a HDInsight-fürtök

A helyszíni Apache Hadoop fájlrendszer (HDFS) könyvtárstruktúrát újra létrehozhatók az Azure Storage vagy az Azure Data Lake Storage. Felhasználói adatok elvesztése nélkül törölje a számításhoz használt HDInsight-fürtök nyugodtan majd törölheti. Mindkét szolgáltatás egy HDInsight-fürtön az alapértelmezett fájlrendszer és a egy további fájlt rendszer is használható. A HDInsight-fürt és a tárfióknak ugyanabban a régióban lehet üzemeltetni.

### <a name="azure-storage"></a>Azure Storage tárterület

HDInsight-fürtök használhatja a blob-tároló Azure Storage-ban vagy az alapértelmezett fájlrendszer, vagy egy további fájlrendszer. A Standard szintű tárfiók és a HDInsight-fürtök együttes használata esetén támogatott. A Premier szintű támogatás tarifacsomag nem támogatott. Az alapértelmezett Blob-tároló a fürtre jellemző információkat, például a feladatelőzményeket és a naplókat tárolja. Egy blobtároló alapértelmezett fájlrendszerként való, több fürt közötti megosztása nem támogatott.

A tárfiókok kulcsait, és a létrehozási folyamat meghatározott tárolt `%HADOOP_HOME%/conf/core-site.xml` a fürtcsomópontokon. Ezek a "Egyéni core webhely" szakaszban az Ambari felhasználói felületén a HDFS-konfigurációban is elérhetők. A tárfiók kulcsát alapértelmezés szerint titkosított, és a egy egyéni visszafejtési parancsfájl segítségével előtt Hadoop démonok átadásra a kulcsok visszafejtéséhez. A feladatokat, beleértve a Hive, MapReduce, Hadoop-Stream és Pig, a storage-fiókok és a velük metaadatok leírását biztosítunk.

Az Azure storage georeplikált is lehet. Bár a georeplikáció földrajzi helyreállítást és adatredundanciát biztosít, a georeplikált helyre feladatátvétel súlyosan hatással van a teljesítményre, és előfordulhat, hogy további költségekkel. Az javasoljuk, hogy válassza a georeplikációt, kategóriába tartozik, és csak az adatok érték esetén a további költségeket.

A következő formátumok egyikét használható az Azure Storage szolgáltatásban tárolt adatok elérésére:

- `wasb:///`: Hozzáférés az alapértelmezett tároló titkosítatlan kommunikáció használata.
- `wasbs:///`: Hozzáférés az alapértelmezett tároló használatával titkosított kommunikáció.
- `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Egy nem alapértelmezett tárfiókhoz való kommunikáció során használt. 

[Az Azure Storage méretezhetőségi és Teljesítménycéljai](../../storage/common/storage-scalability-targets.md) sorolja fel az Azure storage-fiókok aktuális korlátait. Ha az alkalmazás igényeinek meghaladja az egy tárfiókban, a skálázhatósági célokat, az alkalmazás felépíthető több tárfiók használata, és ezután particionálása adatobjektumok ezen a tárfiókon keresztül.

[Az Azure Storage Analytics](../../storage/storage-analytics.md) metrikákat biztosít a tárolási szolgáltatások és az Azure Portalon konfigurált gyűjtése mérőszámok keresztül diagramok az alábbi ábra is lehet. Riasztások arra az esetre, ha küszöbértékeket a rendszer elérte a tárolási erőforrás-mérőszámok hozható létre.

Az Azure Storage kínál [blob-objektumok a helyreállítható Törlés](../../storage/blobs/storage-blob-soft-delete.md) Ha véletlenül módosított vagy törölt egy alkalmazás vagy más storage-fiók felhasználó által az adatok helyreállítását segíti.

Létrehozhat [blobpillanatképet](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob). Egy pillanatképet egy blob egy időben végrehajtott egy csak olvasható verziója, és lehetővé teszi a blob mentésére. Pillanatkép létrehozása után azt is olvassa el, másolja, vagy törölni, de nem módosulnak.

> [!Note]
> A helyi helyszíni Hadoop Disztribúciók a "wasbs" tanúsítvány nem rendelkező régebbi verzióját a Java megbízhatósági tároló importálni kell.

A következő módszerek használhatók tanúsítványok importálása a Java megbízhatósági tárolójába:

Egy fájlba az Azure Blob ssl-tanúsítvány letöltése

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

A fenti fájlt importálja az összes csomóponton, a Java megbízhatósági tároló

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Ellenőrizze, hogy az új tanúsítvány szerepel-e a megbízhatósági tároló

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

További információkért tekintse át a következő cikkeket:

- [Az Azure storage használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-blob-storage.md)
- [Az Azure Storage skálázhatósági és teljesítménycéljai](../../storage/common/storage-scalability-targets.md)
- [A Microsoft Azure Storage teljesítmény- és skálázhatósági ellenőrzőlistája](../../storage/common/storage-performance-checklist.md)
- [Microsoft Azure Storage felügyelete, diagnosztizálása és hibaelhárítása](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Az Azure Portal tárfiók figyelése](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>1. generációs Azure Data Lake Storage

Az Azure Data Lake Storage valósítja meg a HDFS- és POSIX stílusú hozzáférés-vezérlési modellből. Az aad-vel az első osztályú integrációs finom részletes hozzáférés-vezérlést biztosít. Adatok tárolására alkalmas, vagy hogy nagymértékben párhuzamos elemzések futtatására mérete nincs korlátozva van.

További információkért tekintse át a következő cikkeket:

- [Data Lake Storage HDInsight-fürtök létrehozása az Azure portal használatával](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Data Lake Storage használata az Azure HDInsight-fürtök](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2-preview"></a>Az Azure Data Lake Storage Gen2 (előzetes verzió)

Az Azure Data Lake Storage Gen2 a a legújabb tárolási ajánlat és az előzetes verzióban érhető el, ez a cikk írásának időpontjában. Egy Hadoop-kompatibilis fájl rendszer végponttal közvetlenül az Azure Blob Storage-bA integrált Azure Data Lake Storage első generációja a legfontosabb funkcióit, egyesíti. Ez a fejlesztés megbízhatósága és teljesítménye általában csak a helyszíni fájlrendszerek kapcsolódó objektumtár a méretezési csoport és a költség előnyeit ötvözi.

A beépített ADLS általános 2 [Azure Blob storage](../../storage/blobs/storage-blobs-introduction.md) , és lehetővé teszi, hogy mindkét fájl rendszer és a objektum tárolási paradigmákat használatával illesztő. A szolgáltatások [Azure Data Lake Storage Gen1](../../data-lake-store/index.md), például a fájlrendszer szemantikáját, fájlszintű biztonsági és méretezési csoport alacsony költségű, többrétegű tárolást, a magas rendelkezésre állás és vész-helyreállítási funkciókat és a egy nagy SDK és eszközök együtt az ökoszisztéma [Azure Blob storage](../../storage/blobs/storage-blobs-introduction.md). A Data Lake Storage Gen2 objektumtárolás az összes minőség hozzáadása egy fájlrendszer felületen előnyei optimalizált elemzési számítási feladatok közben továbbra is.

A Data Lake Storage Gen2 alapvető jellemzője, igény szerinti hozzáadásával egy [hierarchikus névtér](../../storage/data-lake-storage/namespace.md) a Blob storage szolgáltatásban, amely rendszerezi objektumok vagy fájlokat be egy nagy teljesítményű adatelérés könyvtár-hierarchia. A hierarchikus struktúra lehetővé teszi, hogy a műveletek, például a átnevezése vagy egy kell egyetlen elemi metaadat-művelet a címtárban a címtár törlése helyett enumerálása és annak a könyvtárnak a nevét előtagot használó összes objektum feldolgozása.

Múltbeli időpont felhőalapú elemzési kellett veszélyeztetheti a teljesítmény, a felügyelet és biztonság területéhez. Az Azure Data Lake Storage (ADLS) Gen2 kulcs funkciói a következők:

- **Hadoop-kompatibilis hozzáférés**: Azure Data Lake Storage Gen2 lehetővé teszi, hogy a kezelése és az adatok eléréséhez, ugyanúgy, mint az egy [Hadoop elosztott fájlrendszer (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Az új [ABFS illesztőprogram](../../storage/data-lake-storage/abfs-driver.md) érhető el, amelyek szerepelnek az összes Apache Hadoop-környezetekben [Azure HDInsight](../index.yml). Az illesztőprogram lehetővé teszi a Data Lake Storage Gen2 tárolt adatok elérését.

- **Felülbírálja a POSIX-engedélyek**: A biztonsági modell a Data Lake Gen2 teljes körűen támogatja az ACL-JEI és POSIX engedélyekkel együtt néhány további pontossággal megadott Data Lake Storage Gen2-re. Beállítások felügyeleti eszközökkel vagy keretrendszereket, mint például a Hive és a Spark segítségével konfigurálhatók.

- **Költséghatékony**: Data Lake Storage Gen2 funkciói költséghatékony tárolási kapacitás és a tranzakciók. A teljes életciklusán keresztül adatok transitions lehetőségnél szerint díjszabása nem módosul, mint például a beépített funkciók használatával költségek minimalizálása érdekében módosítsa [Azure Blob storage életciklus](../../storage/common/storage-lifecycle-management-concepts.md).

- **A Blob storage-eszközökkel, keretrendszerek és alkalmazások működik**: Data Lake Storage Gen2 továbbra is az eszközöket, keretrendszerek és alkalmazások a Blob Storage jelenleg létező széles választékának működik.

- **Optimalizált illesztőprogram**: az Azure Blob fájlrendszer illesztőprogramja (ABFS) [kifejezetten optimalizált](../../storage/data-lake-storage/abfs-driver.md) big data-elemzőeszközöket. A megfelelő REST API-k az elosztott fájlrendszerbeli végpont végzetesnek dfs.core.windows.net.

A következő formátumok egyikét az ADLS Gen2 tárolt adatok elérésére használható:
- `abfs:///`: Az alapértelmezett Data Lake Storage a hozzáférés a fürthöz.
- `abfs[s]://file_system@account_name.dfs.core.windows.net`: Egy nem alapértelmezett Data Lake Storage való kommunikáció során használt.

További információkért tekintse át a következő cikkeket:

- [Bevezetés az Azure Data Lake Storage Gen2 előzetes verzió](../../storage/data-lake-storage/introduction.md)
- [Az Azure Blob fájlrendszer illesztőprogram (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)

## <a name="protect-azure-storage-key-visibility-within-the-on-premises-hadoop-cluster-configuration"></a>Azure Storage key látható-e a helyszíni Hadoop-fürt konfigurációját belül védelme

A Hadoop-konfigurációs fájlok kerülnek az Azure storage kulcsokat a helyi HDFS és az Azure Blob storage közötti kapcsolatot létesíteni. Ezek a kulcsok védelme biztosítható az a Hadoop Hitelesítőadat-szolgáltatói keretrendszeren titkosításával. Ha titkosított, azok tárolt és biztonságosan érhetők el.

**A hitelesítő adatok létrehozásához:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**Vegye fel a fenti szolgáltató elérési utat, a core-site.xml, vagy az egyéni hely Ambari konfigurációja:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> A szolgáltató elérési útja tulajdonságban is lehet hozzáadni a distcp parancssor fürt szintjén, a core-site.xml kulcs a következő helyett:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-access-to-azure-storage-data-using-sas-signatures"></a>SAS-aláírások használatával az Azure storage-adatokhoz való hozzáférés korlátozása

Alapértelmezés szerint a HDInsight a fürthöz társított Azure Storage-fiókokat az adatok teljes hozzáféréssel rendelkezik. Közös hozzáférésű Jogosultságkódok (SAS) a blob-tároló segítségével korlátozza a hozzáférést az adatokat, mint például az adatok csak olvasási hozzáféréssel biztosíthatja a felhasználók számára.

### <a name="using-the-sas-token-created-with-python"></a>A Python használatával létrehozott SAS-jogkivonat használatával

1. Nyissa meg a [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) fájlt, és módosítsa a következő értékeket:
    - házirendnév: A nevét, a tárolt házirend létrehozásához.
    - storage_account_name mezőbe: a tárfiók nevére.
    - storage_account_key mezőbe: az a tárfiók kulcsát.
    - storage_container_name: A tároló, amely korlátozza a hozzáférést a kívánt storage-fiókban.
    - example_file_path: A tárolóba feltöltött fájl elérési útja

2. A SASToken.py fájl együtt származik a `ContainerPermissions.READ + ContainerPermissions.LIST` engedélyeket, és a használati eset alapján.

3. Hajtsa végre a parancsfájl a következőképpen: `python SASToken.py`

4. A parancsfájl lefutásakor megjeleníti a SAS-jogkivonatát az alábbi szöveghez hasonló: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Egy tároló közös hozzáférésű Jogosultságkód való hozzáférés korlátozásához adjon egy egyéni bejegyzést a hely konfigurációjának a fürthöz, a hely hozzáadása az Ambari HDFS Configs speciális egyéni tulajdonság.

6. A következő értékeket használja a **kulcs** és **érték** mezők:

    **Kulcs**: `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **érték**: az SAS-kulcsot a Python alkalmazást FROM fenti 4. lépéssel által visszaadott.

7. Kattintson a **Hozzáadás** gombra kattintva mentse a kulcs-érték, majd kattintson a **mentése** gombot a konfigurációs módosítások mentéséhez. Amikor a rendszer kéri, adjon meg egy leírást a változás ("Hozzáadás SAS-tároló hozzáférés" például), és kattintson a **mentése**.

8. Az az Ambari webes felhasználói Felületét, HDFS válassza a bal oldali listából, és válassza **indítsa újra az összes érintett** a a szolgáltatási műveletek legördülő menü a jobb oldali listából. Amikor a rendszer kéri, válassza ki a **megerősítése indítsa újra az összes**.

9. Ismételje meg ezt a folyamatot MapReduce2 és YARN.

Van három fontos Megjegyzendő tudnivalók az Azure-beli SAS-jogkivonatok használatával kapcsolatban:

1. Ha SAS-tokeneket "+ LIST OLVASÁSA" engedélyekkel jönnek létre, a Blob-tároló a SAS-jogkivonat használatával hozzáféréssel rendelkező felhasználókat nem lehet "írási és törlési" adat. Felhasználók, akik a SAS-jogkivonat használatával a blobtároló eléréséhez és próbálja meg az írási vagy törlési művelet, hasonló üzenet jelenik meg `"This request is not authorized to perform this operation"`.

2. Az SAS-jogkivonatok létrehozásának `READ + LIST + WRITE` engedélyek (korlátozása `DELETE` csak), parancsok, például `hadoop fs -put` először írni egy `\_COPYING\_` fájlt, és ismételje meg a fájl átnevezéséhez. A HDFS-művelet képez le egy `copy+delete` a WASB. Mivel a `DELETE` engedély nem lett megadva, a "put" sikertelen lesz. A `\_COPYING\_` műveletet egy olyan Hadoop-szolgáltatás, szolgál, hogy néhány egyidejűség-vezérlés. Jelenleg nincs lehetőség a korlátozása csak a "Törlés" művelet "WRITE" műveletek, valamint befolyásolása nélkül.

3. Sajnos a hadoop hitelesítőadat-szolgáltató és a visszafejtési kulcs szolgáltató (ShellDecryptionKeyProvider) jelenleg nem működik a SAS-tokeneket, és így már jelenleg nem biztosítható a látható-e.

További információkért lásd: [használata az Azure Storage közös hozzáférésű Jogosultságkódokat HDInsight adatok való hozzáférés korlátozása](../hdinsight-storage-sharedaccesssignature-permissions.md)

## <a name="use-data-encryption-and-replication"></a>Használati adatok titkosítása és replikáció

Azure Storage tárterületre írt összes adat automatikusan titkosítva lesznek használatával [Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md). Azure storage-fiókban lévő adatok mindig replikálódik a magas rendelkezésre állás érdekében. Amikor létrehoz egy tárfiókot, a következő replikációs lehetőségek közül választhat:

- [Helyileg redundáns tárolás (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Zónaredundáns tárolás (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Georedundáns tárolás (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Írásvédett georedundáns tárolás (RA-GRS)](../../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)

Az Azure Data Lake Storage biztosítja a helyileg redundáns tárolás (LRS), de kell is kritikus fontosságú adatokat másol egy másik Data Lake Storage-fiók egy másik régióban található a Vészhelyreállítási terv igényeihez igazodó gyakorisággal. Különböző módszerekkel másolhatja az adatokat, többek között [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md), a DistCp, [Azure PowerShell-lel](../../data-lake-store/data-lake-store-get-started-powershell.md), vagy [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md). Emellett ajánlott hozzáférési szabályzatok érvényesítését a Data Lake tárfiókot véletlen törlés megelőzése érdekében.

További információkért tekintse át a következő cikkeket:

- [Az Azure storage replikáció](../../storage/common/storage-redundancy.md)
- [Vészhelyreállítási útmutató az Azure Data Lake Storage (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-the-cluster"></a>A fürt további Azure storage-fiókok csatolása

A HDInsight létrehozása során egy Azure Storage-fiók vagy az Azure Data Lake-tárfiókot az alapértelmezett fájlrendszerként van kiválasztva. Az alapértelmezett tárfiók mellett további tárfiókokat adhat hozzá az Azure-előfizetéshez vagy az Azure-előfizetések a fürt létrehozása során, vagy a fürt létrehozása után.

További storage-fiók egy, az az alábbi módokon adhatók hozzá:
- Hely Ambari HDFS Config speciális egyéni adja hozzá a fiók nevét és kulcsát, a szolgáltatások újraindítása
- Használatával [műveleti parancsfájl](../hdinsight-hadoop-add-storage.md) tartalmában való böngészéshez illessze a tárfiók neve és kulcsa

> [!Note]
> Az érvényes használati esetek, az Azure storage korlátait szóló kérelem keresztül növelhető [Azure-támogatási](https://azure.microsoft.com/support/faq/).

További információkért tekintse át a következő cikkeket:
- [A HDInsight további tárfiókok hozzáadása](../hdinsight-hadoop-add-storage.md)
- [A fürt további Azure storage-fiókok csatolása](https://blogs.msdn.microsoft.com/ashish/2016/08/25/hdinsight-attach-additional-azure-storage-accounts/)

## <a name="next-steps"></a>További lépések

Olvassa el az oktatóanyag-sorozatban a következő cikkben:

- [Adatok áttelepítése ajánlott eljárások a helyszíni az Azure HDInsight Hadoop-áttelepítés](apache-hadoop-on-premises-migration-best-practices-data-migration.md)
---
title: 'Tárolás: Telepítse a helyszíni Apache Hadoop-ot az Azure HDInsightba'
description: Ismerje meg a helyszíni Hadoop-fürtök Azure HDInsightba való áttelepítésével kapcsolatos gyakorlati tanácsokat.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: f19d4adad675cdf95f59aca0f752f46211b75e8f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436920"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Helyszíni Apache Hadoop-fürtök áttelepítése az Azure HDInsightba

Ez a cikk az Azure HDInsight-rendszerekben történő adattárolásra vonatkozó javaslatokat tartalmaz. Ez egy olyan sorozat része, amely gyakorlati tanácsokkal segíti a helyszíni Apache Hadoop-rendszerek Azure HDInsightba való áttelepítését.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Válassza ki a megfelelő tárolórendszert a HDInsight-fürtökhöz

A helyszíni Apache Hadoop fájlrendszer (HDFS) könyvtárstruktúra újra létrehozható az Azure Storage vagy az Azure Data Lake Storage rendszerben. Ezután biztonságosan törölheti a számítási célokra használt HDInsight-fürtöket a felhasználói adatok elvesztése nélkül. Mindkét szolgáltatás használható alapértelmezett fájlrendszerként és egy HDInsight-fürt további fájlrendszereként. A HDInsight-fürt és a tárfiók üzemeltetni kell ugyanabban a régióban.

### <a name="azure-storage"></a>Azure Storage

A HDInsight-fürtök az Azure Storage blobtárolóját alapértelmezett fájlrendszerként vagy egy további fájlrendszerként használhatják.A standard szintű tárfiók hdinsight-fürtökkel való használatra támogatott. A Premier szint nem támogatott. Az alapértelmezett Blob-tároló a fürtre jellemző információkat, például a feladatelőzményeket és a naplókat tárolja.Nem támogatott egy blobtároló megosztása több fürt alapértelmezett fájlrendszereként.

A létrehozási folyamatban definiált tárfiókok és a megfelelő `%HADOOP_HOME%/conf/core-site.xml` kulcsok a fürtcsomópontokon tárolódnak. Az Ambari felhasználói felületen a HDFS-konfiguráció "Egyéni alapwebhely" szakaszában is elérhetők. A tárfiók kulcsa alapértelmezés szerint titkosítva van, és egy egyéni visszafejtési parancsfájlt használ a kulcsok visszafejtésére, mielőtt átadnák a Hadoop démonainak. A feladatok, beleértve a Hive, MapReduce, Hadoop streamelési és pig, a tárfiókok és a metaadatok leírását hordozza velük.

Az Azure Storage georeplikált lehet. Bár a georeplikáció földrajzi helyreállítást és adatredundanciát biztosít, a georeplikált helyre történő feladatátvétel súlyosan befolyásolja a teljesítményt, és további költségekkel járhat. A javaslat az, hogy válassza ki a georeplikáció bölcsen, és csak akkor, ha az adatok értéke megéri a többletköltséget.

Az alábbi formátumok egyike használható az Azure Storage-ban tárolt adatok eléréséhez:

|Adatelérési formátum |Leírás |
|---|---|
|`wasb:///`|Az alapértelmezett tároló elérése titkosítatlan kommunikációval.|
|`wasbs:///`|Hozzáférés az alapértelmezett tárolóhoz titkosított kommunikáció használatával.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Nem alapértelmezett tárfiókkal való kommunikációhoz használatos. |

[Méretezhetőségi célok at standard tárfiókok](../../storage/common/scalability-targets-standard-account.md) felsorolja az Azure Storage-fiókok aktuális korlátait. Ha az alkalmazás igényei meghaladják az egyetlen tárfiók méretezhetőségi céljait, az alkalmazás több tárfiók használatára is létrehozható, majd az adatobjektumok particionálása a tárfiókok között.

[Az Azure Storage Analytics](../../storage/storage-analytics.md) az összes tárolási szolgáltatáshoz biztosít metrikákat, és az Azure Portal konfigurálható a diagramokon keresztül láthatóvá tenni. Riasztások hozhatók létre, hogy értesítse, ha elérte a küszöbértékeket a tárolási erőforrás metrikák.

Az Azure Storage [a blobobjektumok helyreállítható törlését](../../storage/blobs/storage-blob-soft-delete.md) kínálja az adatok helyreállításához, ha azokat véletlenül módosítja vagy törli egy alkalmazás vagy más tárfiók-felhasználó.

[Létrehozhat blob-pillanatképeket.](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob) A pillanatkép egy blob egy adott időpontban készített, és egy blob biztonsági másolatot. A pillanatkép létrehozása után olvasható, másolható vagy törölhető, de nem módosítható.

> [!Note]
> A helyszíni Hadoop disztribúciók régebbi verziói, amelyek nem rendelkeznek a "wasbs" tanúsítvány, azokat importálni kell a Java megbízhatósági tárolóba.

A tanúsítványok Java megbízhatósági tárolóba történő importálásához a következő módszerek használhatók:

Az Azure Blob TLS/SSL tanúsítvány letöltése fájlba

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

A fenti fájl importálása a Java megbízhatósági tárolóba az összes csomóponton

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Annak ellenőrzése, hogy a hozzáadott tanúsítvány a megbízhatósági tárolóban van-e

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

További információkért tekintse át a következő cikkeket:

- [Az Azure Storage használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-blob-storage.md)
- [Méretezhetőségi célok a szabványos tárfiókokhoz](../../storage/common/scalability-targets-standard-account.md)
- [Méretezhetőségi és teljesítménycélok a Blob storage-hoz](../../storage/blobs/scalability-targets.md)
- [A Microsoft Azure Storage teljesítmény- és skálázhatósági ellenőrzőlistája](../../storage/common/storage-performance-checklist.md)
- [Microsoft Azure Storage felügyelete, diagnosztizálása és hibaelhárítása](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Tárfiók monitorozása az Azure Portalon](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>1. generációs Azure Data Lake Storage

Az Azure Data Lake Storage a HDFS és a POSIX stílusú hozzáférés-vezérlési modellt valósítja meg. Első osztályú integrációt biztosít az AAD-vel a finom szemcsés hozzáférés-vezérléshez. Nincsenek korlátok az adatok méretét, hogy tárolhatja, vagy képes futtatni masszívan párhuzamos elemzés.

További információkért tekintse át a következő cikkeket:

- [HDInsight-fürtök létrehozása a Data Lake Storage szolgáltatással az Azure Portal használatával](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [A Data Lake Storage használata az Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-store.md)

### <a name="azure-data-lake-storage-gen2"></a>2. generációs Azure Data Lake Storage

Az Azure Data Lake Storage Gen2 a legújabb tárolási ajánlat. Egyesíti az Azure Data Lake Storage első generációjának alapvető képességeit egy Hadoop-kompatibilis fájlrendszer-végponttal, amely közvetlenül integrálódik az Azure Blob Storage-ba. Ez a fejlesztés egyesíti az objektumtárolás méretezési és költségelőnyeit a csak a helyszíni fájlrendszerekkel jellemző megbízhatósággal és teljesítménnyel.

Az ADLS Gen 2 az [Azure Blob storage-ra](../../storage/blobs/storage-blobs-introduction.md) épül, és lehetővé teszi, hogy az adatokkal mind a fájlrendszer, mind az objektumtárolási paradigmák használatával érintkezhet. Az [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml)szolgáltatásai, például a fájlrendszer szemantikája, a fájlszintű biztonság és a méretezés alacsony költségű, többszintű tárolással, magas rendelkezésre állású/vész-helyreállítási képességekkel, valamint az [Azure Blob storage](../../storage/blobs/storage-blobs-introduction.md)nagy SDK/tooling ökoszisztémájával párosulnak. A Data Lake Storage Gen2-ben az objektumtárolás minden tulajdonsága megmarad, miközben hozzáadja az elemzési számítási feladatokra optimalizált fájlrendszer-felület előnyeit.

A Data Lake Storage Gen2 alapvető jellemzője egy [hierarchikus névtér](../../storage/data-lake-storage/namespace.md) hozzáadása a Blob storage szolgáltatáshoz, amely az objektumokat/fájlokat könyvtárak hierarchiájába rendezi a teljesítményalapú adateléréshez.A hierarchikus struktúra lehetővé teszi, hogy az olyan műveletek, mint például a könyvtár átnevezése vagy törlése, egyetlen atomi metaadat-művelet legyenek a könyvtárban, ahelyett, hogy enumerálnák és feldolgoznák a könyvtár névelőtagját használó összes objektumot.

A múltban a felhőalapú elemzésnek kompromisszumot kellett kötnie a teljesítmény, a felügyelet és a biztonság területén. Az Azure Data Lake Storage (ADLS) Gen2 legfontosabb funkciói a következők:

- **Hadoop-kompatibilis hozzáférés:** Az Azure Data Lake Storage Gen2 lehetővé teszi az adatok kezelését és elérését, ugyanúgy, mint a [Hadoop Distributed File System (HDFS) segítségével.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) Az új [ABFS-illesztőprogram](../../storage/data-lake-storage/abfs-driver.md) az [Azure HDInsight](../index.yml)ban található összes Apache Hadoop-környezetben elérhető. Ez az illesztőprogram lehetővé teszi a Data Lake Storage Gen2-ben tárolt adatok elérését.

- **PoSIX-engedélyek egy szuperhalmaza:** A Data Lake Gen2 biztonsági modellje teljes mértékben támogatja az ACL és POSIX engedélyeket, valamint néhány, a Data Lake Storage Gen2-re jellemző extra részletességet. A beállítások konfigurálhatók felügyeleti eszközökön vagy keretrendszereken, például a Hive-n és a Sparkon keresztül.

- **Költséghatékony**: A Data Lake Storage Gen2 alacsony költségű tárolókapacitással és tranzakciókkal rendelkezik. Ahogy az adatok átváltoznak a teljes életcikluson keresztül, a számlázási díjak változnak, hogy minimalizálják a költségeket olyan beépített funkciókkal, mint például [az Azure Blob tárolási életciklusa.](../../storage/common/storage-lifecycle-management-concepts.md)

- **Blob-tárolóeszközökkel, keretrendszerekkel és alkalmazásokkal működik:** a Data Lake Storage Gen2 továbbra is számos olyan eszközzel, keretrendszerrel és alkalmazással működik, amelyek ma a Blob storage számára léteznek.

- **Optimalizált illesztőprogram:** Az Azure Blob fájlrendszer-illesztőprogram (ABFS) kifejezetten a big data-elemzésre [van optimalizálva.](../../storage/data-lake-storage/abfs-driver.md) A megfelelő REST API-k a dfs-végponton keresztül, dfs.core.windows.net kerülnek felszínre.

Az Alábbi formátumok egyike használható az ADLS Gen2-ben tárolt adatok eléréséhez:
- `abfs:///`: A fürt alapértelmezett Data Lake Storage tárolójának elérése.
- `abfs://file_system@account_name.dfs.core.windows.net`: Nem alapértelmezett Data Lake Storage szolgáltatással való kommunikációsorán használatos.

További információkért tekintse át a következő cikkeket:

- [Bevezetés az Azure Data Lake Storage Gen2 szolgáltatásba](../../storage/data-lake-storage/introduction.md)
- [Az Azure Blob fájlrendszer-illesztőprogramja (ABFS.md)](../../storage/data-lake-storage/abfs-driver.md)
- [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Biztonságos Azure Storage-kulcsok a helyszíni Hadoop-fürt konfigurációján belül

A Hadoop konfigurációs fájljaihoz hozzáadott Azure Storage-kulcsok kapcsolatot létesítenek a helyszíni HDFS és az Azure Blob storage között. Ezek a kulcsok a Hadoop hitelesítő adatok szolgáltatói keretrendszerrel való titkosításával védhetők. A titkosítást követően biztonságosan tárolhatók és elérhetők.

**A hitelesítő adatok kiépítése:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**A fenti szolgáltatóelérési út hozzáadása a core-site.xml fájlhoz vagy az Ambari-konfigurációhoz az egyéni maghely alatt:**

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
> A szolgáltató elérési útja tulajdonság a distcp parancssorhoz is hozzáadható ahelyett, hogy a kulcsot fürtszinten tárolnák a core-site.xml fájlban az alábbiak szerint:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Az Azure Storage-adathozzáférés korlátozása a SAS használatával

HDInsight alapértelmezés szerint teljes hozzáféréssel rendelkezik a fürthöz társított Azure Storage-fiókok adataihoz. Megosztott hozzáférési aláírások (SAS) a blob tárolóban lehet használni az adatokhoz való hozzáférés korlátozására, például a felhasználók írásvédett hozzáférést biztosít az adatokhoz.

### <a name="using-the-sas-token-created-with-python"></a>A pythonnal létrehozott SAS-token használata

1. Nyissa meg a [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) fájlt, és módosítsa a következő értékeket:

    |Token tulajdonság|Leírás|
    |---|---|
    |policy_name|A tárolt házirend létrehozásához használandó név.|
    |storage_account_name|A tárfiók neve.|
    |storage_account_key|A tárfiók kulcsa.|
    |storage_container_name|A tároló a tárfiókban, amely a hozzáférést korlátozni szeretné.|
    |example_file_path|A tárolóba feltöltött fájl elérési útja.|

2. A SASToken.py fájl `ContainerPermissions.READ + ContainerPermissions.LIST` jön az engedélyeket, és lehet igazítani alapján a használati eset.

3. Hajtsa végre a parancsfájlt a következőképpen:`python SASToken.py`

4. A parancsfájl befejezésekor a következő höz hasonló SAS-jogkivonatot jeleníti meg:`sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Ha korlátozni szeretné a hozzáférést egy megosztott hozzáférésű aláírással rendelkező tárolóhoz, adjon hozzá egy egyéni bejegyzést a fürt maghely-konfigurációjához az Ambari HDFS Configs Advanced Custom core-site Add tulajdonság alatt.

6. A **Kulcs** és az **Érték** mezőkhöz használja a következő értékeket:

    **Kulcs** `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` : **Érték**: A Python alkalmazás által visszaadott SAS KULCS A fenti 4.

7. A kulcs és az érték mentéséhez kattintson a **Hozzáadás** gombra, majd a **Mentés** gombra a konfigurációs módosítások mentéséhez. Amikor a rendszer kéri, adja meg a módosítás leírását (például SAS-tároló-hozzáférés hozzáadása), majd kattintson a **Mentés gombra.**

8. Az Ambari webes felhasználói felületen válassza a HDFS elemet a bal oldali listából, majd válassza az **Összes érintett újraindítása lehetőséget** a jobb oldali Szolgáltatási műveletek legördülő listában. Amikor a rendszer kéri, válassza az **Újraindítás megerősítése mindet**lehetőséget.

9. Ismételje meg ezt a folyamatot a MapReduce2 és a YARN esetében.

Három fontos dolog, amit meg kell emlékezni a SAS-jogkivonatok Azure-ban való használatával kapcsolatban:

1. Ha a SAS-jogkivonatok "READ + LIST" engedélyekkel jönnek létre, a Blob-tárolót az adott SAS-jogkivonattal elérő felhasználók nem tudnak "írni és törölni" az adatokat. Azok a felhasználók, akik ezzel a SAS-jogkivonattal érik el `"This request is not authorized to perform this operation"`a Blob-tárolót, és megpróbálnak írni vagy törölni egy műveletet, a következőhöz hasonló üzenetet kapnak: .

2. Ha a SAS-jogkivonatok `READ + LIST + WRITE` engedélyeket `DELETE` hoznak létre `hadoop fs -put` (csak `\_COPYING\_` korlátozzák), parancsok, mint például először írni egy fájlt, majd próbálja meg átnevezni a fájlt. Ez a HDFS-művelet a `copy+delete` WASB-hez készült. Mivel `DELETE` az engedély nem volt megadva, a "put" sikertelen lenne. A `\_COPYING\_` művelet egy Hadoop-szolgáltatás, amelynek célja, hogy némi egyidejűség-vezérlést biztosítson. Jelenleg nincs mód csak a "DELETE" művelet korlátozására anélkül, hogy az "WRITE" műveleteket is befolyásolná.

3. Sajnos a hadoop hitelesítő adatok szolgáltató és visszafejtési kulcs szolgáltató (ShellDecryptionKeyProvider) jelenleg nem működik a SAS-jogkivonatok, és így jelenleg nem védhető a láthatóságtól.

További információt az [Azure Storage megosztott hozzáférési aláírásai használata az adatokhoz való hozzáférés hdinsightban való korlátozásához című témakörben talál.](../hdinsight-storage-sharedaccesssignature-permissions.md)

## <a name="use-data-encryption-and-replication"></a>Adattitkosítás és -replikáció használata

Az Azure Storage [szolgáltatástitkosítás (SSE)](../../storage/common/storage-service-encryption.md)használatával automatikusan titkosítva van az Azure Storage szolgáltatásba írt összes adat. Az Azure Storage-fiókban lévő adatok mindig replikálódik a magas rendelkezésre állás érdekében.Amikor létrehoz egy tárfiókot, az alábbi replikációs lehetőségek közül választhat:

- [Helyileg redundáns tárolás (LRS)](../../storage/common/storage-redundancy-lrs.md)
- [Zónaredundáns tárolás (ZRS)](../../storage/common/storage-redundancy-zrs.md)
- [Georedundáns tárolás (GRS)](../../storage/common/storage-redundancy-grs.md)
- [Írásvédett georedundáns tárolás (RA-GRS)](../../storage/common/storage-redundancy.md)

Az Azure Data Lake Storage helyileg redundáns tárolást (LRS) biztosít, de a kritikus fontosságú adatokat egy másik régióban lévő másik Data Lake Storage-fiókba is át kell másolnia, a vész-helyreállítási terv igényeinek megfelelő gyakorisággal.Az adatok másolására különböző módszerek léteznek, például [az ADLCopy,](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) [a DistCp,](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html)az [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)vagy az [Azure Data Factory.](../../data-factory/connector-azure-data-lake-store.md)Azt is javasoljuk, hogy a data lake storage-fiók hozzáférési szabályzatok kényszerítése a véletlen törlés megelőzése érdekében.

További információkért tekintse át a következő cikkeket:

- [Az Azure Storage replikációja](../../storage/common/storage-redundancy.md)
- [Katasztrófa-elhárítás az Azure Data Lake Storage (ADLS) számára](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>További Azure Storage-fiókok csatolása a fürthöz

A HDInsight-létrehozási folyamat során egy Azure Storage-fiók vagy az Azure Data Lake Storage-fiók lesz az alapértelmezett fájlrendszer. Ezen az alapértelmezett tárfiókon kívül további tárfiókok is hozzáadhatók ugyanabból az Azure-előfizetésből vagy különböző Azure-előfizetésekből a fürt létrehozási folyamata során vagy a fürt létrehozása után.

További tárfiók az alábbi módokon adható hozzá:
- Ambari HDFS Config Advanced Custom core-site Adja hozzá a tárfiók nevét és a legfontosabb újraindítása a szolgáltatások
- [Parancsfájl-művelet](../hdinsight-hadoop-add-storage.md) használata a tárfiók nevének és kulcsának átadásával

> [!Note]
> Érvényes használati esetekben az Azure-tárhely korlátai növelhetők az [Azure-támogatáshoz](https://azure.microsoft.com/support/faq/)intézett kéréssel.

További információkért lásd: [Add additional storage accounts to HDInsight](../hdinsight-hadoop-add-storage.md) (További tárfiókok hozzáadása a HDInsighthoz).

## <a name="next-steps"></a>További lépések

Olvassa el a sorozat következő cikkét: A helyszíni adatok áttelepítésével kapcsolatos gyakorlati tanácsok az [Azure HDInsight Hadoop-áttelepítéshez.](apache-hadoop-on-premises-migration-best-practices-data-migration.md)

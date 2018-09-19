---
title: Az Azure portal segítségével Azure HDInsight-fürtök létrehozása az Azure Data Lake Storage Gen1 |} A Microsoft Docs
description: Az Azure portal használatával létrehozása és használata HDInsight-fürtök az Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 28bed3578c653c8081868b3d950ab6332879784a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46124325"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Az Azure Data Lake Storage Gen1 HDInsight-fürtök létrehozása az Azure portal használatával
> [!div class="op_single_selector"]
> * [Az Azure Portal használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell használata (az alapértelmezett tároló)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Használja a Powershellt (további tároló)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Erőforrás-kezelő használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Ismerje meg, hogyan hozzon létre egy HDInsight-fürt egy további tárterületet, az alapértelmezett tároló egy olyan Azure Data Lake Storage Gen1 fiókkal az Azure portal használatával. Annak ellenére, hogy a további tárhely nem kötelező megadni egy HDInsight-fürt, javasoljuk, hogy a további tárfiókok az üzleti adatok tárolására.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez győződjön meg arról, hogy teljesítette az alábbi követelményeknek:

* **Azure-előfizetés**. Lépjen a [lekérése az Azure ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* **A Data Lake Storage Gen1 fiók**. Kövesse az utasításokat a [használatának első lépései az Azure Data Lake Storage Gen1 az Azure Portalon](data-lake-store-get-started-portal.md). A fiók is létre kell hoznia egy legfelső szintű mappát.  Ebben az oktatóanyagban a gyökérmappa nevű __/fürtök__ szolgál.
* **Egy Azure Active Directory-szolgáltatásnevet**. Ebben az oktatóanyagban útmutatás egy szolgáltatásnév létrehozásához az Azure Active Directoryban (Azure AD). Azonban egy szolgáltatásnév létrehozásához, hogy Azure AD-rendszergazda kell lennie. Ha Ön rendszergazda, kihagyhatja ezt az előfeltételt, és folytassa az oktatóanyagot.

    >[!NOTE]
    >Létrehozhat egy szolgáltatás egyszerű csak akkor, ha az Azure AD-rendszergazdaként. Az Azure AD-rendszergazda kell egy egyszerű szolgáltatás létrehozása a Data Lake Storage Gen1 egy HDInsight-fürt létrehozása előtt. Emellett az egyszerű szolgáltatás léteznie kell egy tanúsítványt, az ismertetett módon [egyszerű szolgáltatás létrehozása tanúsítvánnyal](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>HDInsight-fürt létrehozása

Ebben a szakaszban egy HDInsight-fürt a Data Lake Storage Gen1 fiókokkal, az alapértelmezett vagy a további tárhely létrehozása. Ez a cikk csak a Data Lake Storage Gen1 fiókok konfigurálására része foglalkozik.  Az általános fürt létrehozásának információkat és eljárásokat, lásd: [Hadoop-fürtök létrehozása a HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Fürt létrehozása a Data Lake Storage Gen1 alapértelmezett tárolóként

**A HDInsight-fürt létrehozása a Data Lake Storage Gen1 fiókkal az alapértelmezett tárfiók**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Hajtsa végre a [fürtöket](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) az általános információk a HDInsight-fürtök létrehozása során.
3. Az a **tárolási** panel alatt **elsődleges tárolási típusok**, jelölje be **Azure Data Lake Storage Gen1**, majd adja meg a következő információkat:

    ![Szolgáltatásnév hozzáadása a HDInsight-fürt](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "hozzáadása szolgáltatásnévhez a HDInsight-fürthöz")

    - **Válassza ki, Data Lake Store-fiók**: Válasszon egy meglévő Data Lake Storage Gen1 fiókot. Egy meglévő Data Lake Storage Gen1 fiókot kell megadni.  Lásd: [Előfeltételek](#prerequisites).
    - **Gyökér elérési útját**: Adja meg egy elérési utat, ahol a fürtre jellemző fájlok vannak-e tárolni. A képernyőképen az __/fürtök/myhdiadlcluster/__, amelyben a __/fürtök__ mappának léteznie kell, és a portál létrehozza a *myhdicluster* mappát.  A *myhdicluster* a fürt neve.
    - **Data Lake Store-hozzáférés**: konfigurálja a HDInsight-fürt és a Data Lake Storage Gen1 fiók közötti hozzáférést. Útmutatásért lásd: [konfigurálása Data Lake Storage Gen1 hozzáférés](#configure-data-lake-store-access).
    - **További tárfiókok**: Adja hozzá az Azure storage-fiókok, a fürt további tárfiókok. Data Lake Storage Gen1 további fiókok hozzáadása a fürt engedélyek a Data Lake Storage Gen1 további fiókok adatokon, így az elsődleges tárolási típusok, egy Data Lake Storage Gen1 fiók konfigurálása során történik. Lásd: [konfigurálása Data Lake Storage Gen1 hozzáférés](#configure-data-lake-store-access).

4. Az a **Data Lake Store-hozzáférés**, kattintson a **kiválasztása**, majd folytassa a fürt létrehozása leírtak szerint [Hadoop-fürtök létrehozása a HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Fürt létrehozása a Data Lake Storage Gen1 kiegészítő tárolóként

Az alábbi utasításokat egy HDInsight-fürtöt hozzon létre egy Azure storage-fiókot az alapértelmezett tárolóként, és a egy Data Lake Storage Gen1 fiókot egy kiegészítő tárolóként.

**Hozzon létre egy HDInsight-fürtöt egy Data Lake Storage Gen1 fiókot egy tárfiókot**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Hajtsa végre a [fürtöket](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) az általános információk a HDInsight-fürtök létrehozása során.
3. Az a **tárolási** panel alatt **elsődleges tárolási típusok**, jelölje be **Azure Storage**, majd adja meg a következő információkat:

    ![Szolgáltatásnév hozzáadása a HDInsight-fürt](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "hozzáadása szolgáltatásnévhez a HDInsight-fürthöz")

    - **Kiválasztási módszer**: használja a következő lehetőségek egyikét:

        * Megadhat egy storage-fiókot az Azure-előfizetés részét képező **saját előfizetések**, majd válassza ki a tárfiókot.
        * Egy storage-fiókot, amely kívül esik az Azure-előfizetésében, jelölje be **hozzáférési kulcs**, és adja meg a külső tárfiók adatait.

    - **Alapértelmezett tároló**: használja az alapértelmezett értéket, vagy adja meg a saját nevét.

    - További tárfiókok: Adjon hozzá további Azure storage-fiókok további tárolóként.
    - Data Lake Store-hozzáférés: a Data Lake Storage Gen1 fiók és a HDInsight-fürt közötti hozzáférés konfigurálásához. Útmutatásért lásd: [konfigurálása Data Lake Storage Gen1 hozzáférés](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1 hozzáférés konfigurálása 

Ebben a szakaszban HDInsight-fürtök az Azure Active Directory egyszerű szolgáltatás használatával a Data Lake Storage Gen1 hozzáférést konfigurálja. 

### <a name="specify-a-service-principal"></a>Adja meg az egyszerű szolgáltatás

Az Azure Portalról használjon egy meglévő egyszerű szolgáltatást, vagy hozzon létre egy újat.

**Egyszerű szolgáltatás létrehozása az Azure Portalról**

1. Kattintson a **Data Lake Store-hozzáférés** a tároló panelen.
2. Az a **Data Lake Storage Gen1 hozzáférés** panelen kattintson a **új létrehozása**.
3. Kattintson a **szolgáltatásnév**, és kövesse az utasításokat egy szolgáltatásnév létrehozásához.
4. Ha úgy dönt, hogy a későbbiekben használni, töltse le a tanúsítványt. A tanúsítvány letöltése akkor hasznos, ha azonos egyszerű szolgáltatás használandó további HDInsight-fürtök létrehozásakor.

    ![Szolgáltatásnév hozzáadása a HDInsight-fürt](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "hozzáadása szolgáltatásnévhez a HDInsight-fürthöz")

4. Kattintson a **hozzáférés** a mappa hozzáférés konfigurálásához.  Lásd: [fájlengedélyek beállítása](#configure-file-permissions).


**Egy meglévő egyszerű szolgáltatást az Azure Portal használata**

1. Kattintson a **Data Lake Store-hozzáférés**.
1. Az a **Data Lake Storage Gen1 hozzáférés** panelen kattintson a **meglévő**.
2. Kattintson a **szolgáltatásnév**, majd válassza ki egy egyszerű szolgáltatást. 
3. Töltse fel a kiválasztott szolgáltatásnévhez társított tanúsítvány (.pfx-fájl), és adja meg a tanúsítvány jelszavát.

    ![Szolgáltatásnév hozzáadása a HDInsight-fürt](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "hozzáadása szolgáltatásnévhez a HDInsight-fürthöz")

4. Kattintson a **hozzáférés** a mappa hozzáférés konfigurálásához.  Lásd: [fájlengedélyek beállítása](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Fájlengedélyek beállítása

A konfigurálható eltérőek attól függően, hogy a fiókot használja, az alapértelmezett tároló egy tárfiókot:

- Használja az alapértelmezett tároló

    - a Data Lake Storage Gen1 fiók gyökérszinten engedély
    - a HDInsight tárolójaként gyökérszinten engedély. Ha például a __/fürtök__ az oktatóanyag korábbi részében használt mappa.
- Egy kiegészítő tárolóként használja

    - Engedély a a mappákat, ahol fájlt kell a hozzáférést.

**A Data Lake Storage Gen1 fiók gyökérszinten engedély hozzárendelése**

1. Az a **Data Lake Storage Gen1 hozzáférés** panelen kattintson a **hozzáférés**. A **fájlengedélyek kiválasztása** panel megnyílik. Az előfizetés a Data Lake Storage Gen1 fiókok listáját.
2. Vigye fölé (ne kattintson) az egérrel az egérmutatót a Data Lake Storage Gen1 fiók láthatóvá tétele a jelölőnégyzetet, majd jelölje be a jelölőnégyzetet.

    ![Szolgáltatásnév hozzáadása a HDInsight-fürt](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "hozzáadása szolgáltatásnévhez a HDInsight-fürthöz")

  Alapértelmezés szerint __OLVASÁSI__, __írási__, és __EXECUTE__ vannak jelölve.

3. Kattintson a **kiválasztása** az oldal alján.
4. Kattintson a **futtatása** rendelhet hozzá.
5. Kattintson a **Done** (Kész) gombra.

**A HDInsight fürt legfelső szintjén engedély hozzárendelése**

1. Az a **Data Lake Storage Gen1 hozzáférés** panelen kattintson a **hozzáférés**. A **fájlengedélyek kiválasztása** panel megnyílik. Az előfizetés a Data Lake Storage Gen1 fiókok listáját.
1. Az a **fájlengedélyek kiválasztása** panelen kattintson a Data Lake Storage Gen1 fiók nevére, a tartalma megjelenítéséhez.
2. Válassza ki a HDInsight-fürt tárolási legfelső szintű mappa a bal oldali jelölőnégyzet bejelölésével. A képernyőképen megfelelően korábban, a fürt tárolási legfelső szintű van __/fürtök__ megadott Data Lake Storage Gen1 kiválasztásakor az alapértelmezett tároló.
3. Beállítja az engedélyeket a mappához.  Alapértelmezés szerint olvasási, írási és végrehajtási vannak jelölve.
4. Kattintson a **kiválasztása** az oldal alján.
5. Kattintson a **Run** (Futtatás) parancsra.
6. Kattintson a **Done** (Kész) gombra.

Ha a Data Lake Storage Gen1 kiegészítő tárolóként használja, hozzá kell rendelnie engedély csak a mappákat, amelyeket el szeretne érni a HDInsight-fürtből. Ha például az alábbi képernyőképen, hozzáférést csak a **mynewfolder** mappa a Data Lake Storage Gen1-fiókban.

![Szolgáltatás egyszerű engedélyek hozzárendelése a HDInsight-fürt](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "szolgáltatás egyszerű engedélyek hozzárendelése a HDInsight-fürthöz")


## <a name="verify-cluster-set-up"></a>Állítsa be a fürt ellenőrzése

A fürt telepítés befejezése után a fürt paneljén ellenőrizze az eredményeket valamelyike vagy mindegyike a következő lépések végrehajtásával:

* Győződjön meg arról, hogy a társított tárolót a fürt a Data Lake Storage Gen1 fiók a megadott, kattintson a **tárfiókok** a bal oldali panelen.

    ![Szolgáltatásnév hozzáadása a HDInsight-fürt](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "hozzáadása szolgáltatásnévhez a HDInsight-fürthöz")

* Ellenőrizheti, hogy a HDInsight-fürt megfelelően társítva-e az egyszerű szolgáltatás, a **Data Lake Storage Gen1 hozzáférés** a bal oldali panelen.

    ![Szolgáltatásnév hozzáadása a HDInsight-fürt](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "hozzáadása szolgáltatásnévhez a HDInsight-fürthöz")


## <a name="examples"></a>Példák

Miután beállította a fürtöt a Data Lake Storage Gen1 a tárolóként, tekintse meg ezekben a példákban a HDInsight-fürt használata a Data Lake Storage Gen1 tárolt adatok elemzéséhez.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Hive-lekérdezést futtathat egy Data Lake Storage Gen1 fiókban található adatok alapján (az elsődleges tároló)

A Hive-lekérdezések futtatásához használja a Hive-nézetek felület az Ambari portálon. Az Ambari Hive-nézetek használata az utasításokért lásd: [a Hive-nézet használata a HDInsight Hadoop-keretrendszerrel](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

A Data Lake Storage Gen1 fiókban adatokkal dolgozik, ha nincsenek néhány karakterláncok módosítása.

Ha használ, például a Data Lake Storage Gen1 elsődleges tárolóként, a létrehozott fürtöt az adatok elérési útja: *adl: / / < data_lake_storage_gen1_account_name > /azuredatalakestore.net/path/to/file*. A mintaadatokat a Data Lake Storage Gen1 fiókban tárolt tábla létrehozása Hive-lekérdezést a következő utasítás hasonlóan néz ki:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Leírása:
* `adl://hdiadlsg1storage.azuredatalakestore.net/` a Data Lake Storage Gen1 fiók gyökérkönyvtárában van.
* `/clusters/myhdiadlcluster` a fürt adatai, a fürt létrehozásakor megadott gyökérkönyvtárában van.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` az a hely a lekérdezésben használt minta-fájl.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>A Hive-lekérdezések futtatásához egy Data Lake Storage Gen1 fiókban található adatok (kiegészítő tárolóként)

Ha a fürthöz létrehozott Blob storage alapértelmezett tárolóként használ, a mintaadatok nem szerepel a kiegészítő tárolóként használt Data Lake Storage Gen1 fiókot. Ebben az esetben először át az adatok Blob storage-ból a Data Lake Storage Gen1 fiók, és futtassa a lekérdezéseket, a fenti példában látható módon.

Az adatok másolása Blob storage-ból egy Data Lake Storage Gen1 fiókhoz további információkért lásd: a következő cikkeket:

* [Adatok másolása az Azure Storage-blobok és a Data Lake Storage Gen1 között a Distcp használatával](data-lake-store-copy-data-wasb-distcp.md)
* [Adatok másolása az Azure Storage-blobokból a Data Lake Storage Gen1 AdlCopy használatával](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Data Lake Storage Gen1 használata Spark-fürttel
Spark-fürt használatával Spark-feladatokat futtathat a Data Lake Storage Gen1-fiókban tárolt adatokon. További információkért lásd: [használata a HDInsight Spark-fürt a Data Lake Storage Gen1 adatok elemzéséhez](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Data Lake Storage Gen1 használható Storm-topológia
A Data Lake Storage Gen1 fiók használható Storm-topológiából származó adatok írására. Hogyan érhető el ebben a forgatókönyvben az utasításokért lásd: [használata Azure Data Lake Storage Gen1 a HDInsight-alapú Apache Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Lásd még
* [Az Azure HDInsight-fürtök használata a Data Lake Storage Gen1](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: Hozzon létre egy HDInsight-fürt használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

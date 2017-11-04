---
title: "Azure HDInsight-fürtök létrehozása a Data Lake Store az Azure-portál használatával |} Microsoft Docs"
description: "Az Azure-portál használatával hozzon létre, és a HDInsight-fürtök használata az Azure Data Lake Store"
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/14/2017
ms.author: nitinme
ms.openlocfilehash: 6be2b9e21d134461dc00d93c3f188c96c6e7af6e
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>A HDInsight-fürtök létrehozása a Data Lake Store az Azure-portál használatával
> [!div class="op_single_selector"]
> * [Az Azure Portal használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [A PowerShell (az alapértelmezett tároló)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Használja a Powershellt (tárhely)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Erőforrás-kezelő használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Útmutató egy HDInsight-fürt létrehozása az Azure Data Lake Store-fiók az alapértelmezett tároló vagy egy további tárhely az Azure-portál használatával. Annak ellenére, hogy a további tárhely nem kötelező megadni egy HDInsight-fürtöt, javasoljuk, hogy az üzleti adatok tárolása a további tárfiókok.

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag megkezdése előtt győződjön meg arról, hogy teljesítette az alábbi követelményeknek:

* **Azure-előfizetés**. Ugrás a [beolvasása az Azure ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Store-fiók**. Kövesse az utasításokat a [Ismerkedés az Azure Data Lake Store az Azure portál használatával](data-lake-store-get-started-portal.md). A fiók is létre kell hoznia egy legfelső szintű mappát.  Ebben az oktatóanyagban egy legfelső szintű mappa neve a __/fürtök__ szolgál.
* **Egy Azure Active Directory szolgáltatás egyszerű**. Ez az oktatóanyag útmutatás az Azure Active Directory (Azure AD) egyszerű szolgáltatás létrehozása. Azonban szeretne létrehozni egy egyszerű szolgáltatást, akkor az Azure AD rendszergazdai jogokkal kell rendelkeznie. Ha Ön rendszergazda, hagyja ki ezt az előfeltételt, és az oktatóanyag folytatásához.

    >[!NOTE]
    >Létrehozhat egy szolgáltatás egyszerű csak akkor, ha az Azure AD-rendszergazdaként. Az Azure AD rendszergazdának létre kell hoznia egy szolgáltatás egyszerű HDInsight-fürtök létrehozása a Data Lake Store előtt. Emellett a szolgáltatás egyszerű kell létrehozni a tanúsítvánnyal részben ismertetett módon [hozzon létre egy egyszerű tanúsítvány](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>HDInsight-fürtök létrehozása

Ebben a szakaszban egy HDInsight-fürtöt az alapértelmezett vagy a további tárhely Data Lake Store-fiókok létrehozása. Ez a cikk csak a Data Lake Store-fiókok konfigurálása része foglalkozik.  Az általános fürt létrehozása információkat és eljárásokat, [Hadoop létrehozása a HDInsight-fürtök](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>Fürt létrehozása a Data Lake Store alapértelmezett tárolóként

**A HDInsight-fürt létrehozása az egy Data Lake Store az alapértelmezett tárfiók**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Hajtsa végre a [fürtöket létrehozni](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) az általános létrehozásával kapcsolatos információkat a HDInsight-fürtök.
3. A a **tárolási** panel alatt **elsődleges tárolási típus**, jelölje be **Data Lake Store**, és írja be a következő információkat:

    ![HDInsight-fürthöz Hozzáadás egyszerű](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Hozzáadás szolgáltatás egyszerű HDInsight-fürthöz")

    - **Válassza ki Data Lake Store-fiók**: Válasszon egy meglévő Data Lake Store-fiókot. Egy meglévő Data Lake Store-fiókot kell megadni.  Lásd: [Előfeltételek](#prereuisites).
    - **Elérési útjának gyökeréhez**: Adjon meg útvonalat, ahol a fürt fájlokat vannak-e tárolni. Képernyőképen látható, a rendszer __/fürtök/myhdiadlcluster/__, amelyben a __/fürtök__ mappának léteznie kell, és a portál létrehoz egyet *myhdicluster* mappa.  A *myhdicluster* a fürt neve.
    - **Data Lake Store hozzáférés**: konfigurálja a hozzáférést a Data Lake Store-fiók és a HDInsight-fürt között. Útmutatásért lásd: [konfigurálása Data Lake Store hozzáférés](#configure-data-lake-store-access).
    - **További tárfiókok**: a fürthöz tartozó fiókok hozzáadása Azure Storage-fiókokról további tárolóként. Hozzáadandó további Data Lake tárolók végezhető el a fürt engedélyeket ad a további Data Lake Store-fiók adatait az elsődleges tárolási típus, egy Data Lake Store-fiók konfigurálása közben. Lásd: [A Data Lake Store-hoz történő hozzáférés konfigurálása](#configure-data-lake-store-access).

4. A a **Data Lake Store hozzáférés**, kattintson a **kiválasztása**, majd folytassa a fürt létrehozása a [Hadoop létrehozása a HDInsight-fürtök](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>A fürt létrehozása a Data Lake Store további tárhely

Az alábbi utasításokat egy HDInsight-fürtöt hozzon létre egy Azure Storage-fiókot az alapértelmezett tárolóként, és további tárterületként egy Data Lake Store-fiókot.
**A HDInsight-fürt létrehozása az egy Data Lake Store az alapértelmezett tárfiók**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Hajtsa végre a [fürtöket létrehozni](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) az általános létrehozásával kapcsolatos információkat a HDInsight-fürtök.
3. A a **tárolási** panel alatt **elsődleges tárolási típus**, jelölje be **Azure Storage**, és írja be a következő információkat:

    ![HDInsight-fürthöz Hozzáadás egyszerű](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Hozzáadás szolgáltatás egyszerű HDInsight-fürthöz")

    - **Kijelöléséről**: használja a következő lehetőségek egyikét:

        * A storage-fiók, amely az Azure-előfizetéshez része, adja meg **a saját előfizetések**, majd válassza ki a tárfiók.
        * A storage-fiók, amely az Azure-előfizetéshez kívül esik, jelölje be a **hozzáférési kulcs**, és adja meg a külső tárfiók adatait.

    - **Alapértelmezett tároló**: használja az alapértelmezett értéket, vagy adja meg saját nevét.

    - További tárfiókok: a további tárhely további Azure Storage-fiókokat hozzáadni.
    - Data Lake Store hozzáférés: konfigurálja a hozzáférést a Data Lake Store-fiók és a HDInsight-fürt között. Útmutatásért lásd: [konfigurálása Data Lake Store hozzáférés](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Data Lake Store-hozzáférés konfigurálása 

Ebben a szakaszban egy Azure Active Directory szolgáltatás egyszerű HDInsight-fürtök a Data Lake Store-hozzáférés konfigurálásához. 

### <a name="specify-a-service-principal"></a>Adjon meg egy szolgáltatásnevet

Azure-portálról egy meglévő egyszerű szolgáltatást használja, vagy hozzon létre egy újat.

**Egy egyszerű szolgáltatás létrehozása az Azure-portálon**

1. Kattintson a **Data Lake Store hozzáférés** a Store panelen.
2. Az a **Data Lake Store hozzáférés** panelen kattintson a **hozzon létre új**.
3. Kattintson a **egyszerű**, majd kövesse az útmutatást követve hozzon létre egy egyszerű szolgáltatást.
4. Ha úgy dönt, hogy a későbbiekben használni, töltse le a tanúsítványt. A tanúsítvány letöltése akkor hasznos, ha további HDInsight-fürtök létrehozásakor az azonos egyszerű szolgáltatás használni szeretne.

    ![HDInsight-fürthöz Hozzáadás egyszerű](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Hozzáadás szolgáltatás egyszerű HDInsight-fürthöz")

4. Kattintson a **hozzáférés** a mappa hozzáférés konfigurálásához.  Lásd: [fájl engedélyeit](#configure-file-permissions).


**Egy meglévő egyszerű Azure-portálról használata**

1. Kattintson a **Data Lake Store hozzáférés**.
1. Az a **Data Lake Store hozzáférés** panelen kattintson a **meglévő**.
2. Kattintson a **egyszerű**, majd válassza ki a szolgáltatásnevet. 
3. Töltse fel a kijelölt szolgáltatás egyszerű társított tanúsítvány (.pfx-fájl), és írja be a tanúsítvány jelszavát.

    ![HDInsight-fürthöz Hozzáadás egyszerű](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Hozzáadás szolgáltatás egyszerű HDInsight-fürthöz")

4. Kattintson a **hozzáférés** a mappa hozzáférés konfigurálásához.  Lásd: [fájl engedélyeit](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Fájl-engedélyek konfigurálása

A konfigurálható eltér attól függően, hogy a rendszer fiókot használja az alapértelmezett tároló vagy egy további tárhely-fiókot:

- Alapértelmezett tárolójaként használni kívánt

    - a Data Lake Store-fiók gyökérszinten engedély
    - a HDInsight-fürt tároló gyökérszinten engedély. Például a __/fürtök__ az oktatóanyag korábbi részében használt mappát.
- További tárterületként használata

    - A a mappákat, ahol kell fájl hozzáférési engedély.

**A Data Lake Store fiók gyökérszinten engedély hozzárendelése**

1. A a **Data Lake Store hozzáférés** panelen kattintson a **hozzáférés**. A **válassza ki a fájl engedélyeit** panel már meg van nyitva. Felsorolja az előfizetés összes Data Lake Store-fiók.
2. Vigye (ne kattintson) az egér, a jelölőnégyzet látható, hogy a Data Lake Store-fiók neve alatt jelölje be a jelölőnégyzetet.

    ![HDInsight-fürthöz Hozzáadás egyszerű](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Hozzáadás szolgáltatás egyszerű HDInsight-fürthöz")

  Alapértelmezés szerint __OLVASÁSI__, __írási__, és __EXECUTE__ lehetőségek egyaránt be vannak jelölve.

3. Kattintson a **válasszon** az oldal alján.
4. Kattintson a **futtatása** rendelhet hozzá.
5. Kattintson a **Done** (Kész) gombra.

**A HDInsight fürt gyökérszinten engedély hozzárendelése**

1. A a **Data Lake Store hozzáférés** panelen kattintson a **hozzáférés**. A **válassza ki a fájl engedélyeit** panel már meg van nyitva. Felsorolja az előfizetés összes Data Lake Store-fiók.
1. Az a **válassza ki a fájl engedélyeit** panelen kattintson a Data Lake Store nevét, a benne lévő tartalom megjelenítése.
2. Válassza ki a HDInsight-fürt tárológyökérhez a mappa a bal oldali jelölőnégyzet bejelölésével. A képernyőfelvételen megfelelően korábban, a fürt tárológyökérhez van __/fürtök__ kiválasztása a Data Lake Store alapértelmezett tárolóként közben megadott mappában.
3. Beállítja az engedélyeket a mappához.  Alapértelmezés szerint olvasási, írási és végrehajtási lehetőségek egyaránt be vannak jelölve.
4. Kattintson a **válasszon** az oldal alján.
5. Kattintson a **Run** (Futtatás) parancsra.
6. Kattintson a **Done** (Kész) gombra.

Ha a Data Lake Store további tárolóként használ, hozzá kell rendelnie engedély csak a HDInsight-fürt eléréséhez használni kívánt mappákat. Például az alábbi képernyőképen kívánja megadni a hozzáférést csak a **hdiaddonstorage** mappát a Data Lake Store-fiók.

![Szolgáltatás egyszerű engedélyek hozzárendelése a HDInsight-fürt](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "szolgáltatás egyszerű engedélyek hozzárendelése a HDInsight-fürt")


## <a name="verify-cluster-set-up"></a>Ellenőrizze a fürt beállítása

A fürt telepítés befejezése után, a fürt paneljén, ellenőrizze az eredményeket valamelyike vagy mindegyike a következő lépések végrehajtásával:

* Győződjön meg arról, hogy a társított tárolót a fürt a Data Lake Store-fiók, amely a megadott, kattintson a **tárfiókok** a bal oldali ablaktáblán.

    ![HDInsight-fürthöz Hozzáadás egyszerű](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Hozzáadás szolgáltatás egyszerű HDInsight-fürthöz")

* Győződjön meg arról, hogy a szolgáltatás egyszerű megfelelően a HDInsight-fürthöz társított, kattintson a **Data Lake Store hozzáférés** a bal oldali ablaktáblán.

    ![HDInsight-fürthöz Hozzáadás egyszerű](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Hozzáadás szolgáltatás egyszerű HDInsight-fürthöz")


## <a name="examples"></a>Példák

Után a tárolására állította be a fürt a Data Lake Store, tekintse meg ezekben a példákban a HDInsight-fürt használata a Data Lake Store-ban tárolt adatok elemzésére.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Hive-lekérdezések futtatása egy Data Lake Store-ban adatok alapján (elsődleges tárolóként)

Hive-lekérdezések futtatásához használja a Hive-nézetek felület az Ambari portálon. Ambari Hive-nézetek használata, lásd: [Hive nézet használata a hadooppal a Hdinsightban](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Ha az egy Data Lake Store-adatokkal dolgozik, van néhány karakterláncok módosítása.

Ha használ, például a fürt, amelyet a Data Lake Store elsődleges tárolóként hozott létre az adatok elérési útja: *adl: / / < data_lake_store_account_name > /azuredatalakestore.net/path/to/file*. Táblázat létrehozása a Data Lake Store-fiókban tárolt adatok minta Hive-lekérdezések néz ki a következő utasítást:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Leírása:
* `adl://hdiadlstorage.azuredatalakestore.net/`a Data Lake Store-fiók gyökérkönyvtárában van.
* `/clusters/myhdiadlcluster`a fürt adatait, amely a fürt létrehozásakor megadott gyökérkönyvtárában van.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`az a hely a lekérdezésben használt minta-fájl.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>Hive-lekérdezések futtatása egy Data Lake Store-ban adatok alapján (a további tárhely)

Ha a fürt létrehozott Blob-tároló alapértelmezett tárolóként használ, a mintaadatok nem szerepel az Azure Data Lake Store további tárolására használt fiók. Ebben az esetben először átviteléhez a Blob storage a Data Lake Store az adatokat, és futtassa újra a lekérdezések, az előző példában látható módon.

Az adatok másolása az Blob storage egy Data Lake Store további információkért lásd: a következő cikkeket:

* [Ból a Distcp követve másolja át a adatok Azure Storage-BLOB és a Data Lake Store között](data-lake-store-copy-data-wasb-distcp.md)
* [Adatok másolása az Azure Storage blobs Data Lake Store AdlCopy segítségével](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Használjon Data Lake Store egy Spark-fürt
Spark-fürt használatával a Data Lake Store-ban tárolt adatok a feladatok futtatása Spark. További információkért lásd: [adatelemzéshez a Data Lake Store használata a HDInsight Spark-fürt](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Használjon Data Lake Store a Storm-topológia
A Data Lake Store az adatok írása a Storm-topológia a használhatja. Ez a forgatókönyv megvalósítható utasításokért lásd: [használata Azure Data Lake Store a HDInsight alatt futó Apache Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Lásd még:
* [Használjon Data Lake Store az Azure HDInsight-fürtök](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: Data Lake Store használatára HDInsight-fürtök létrehozása](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

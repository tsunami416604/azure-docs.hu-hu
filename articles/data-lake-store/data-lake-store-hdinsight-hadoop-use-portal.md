---
title: Azure HDInsight-fürtök létrehozása a Data Lake Storage Gen1 portállal
description: HdInsight-fürtök létrehozása és használata az Azure Data Lake Storage Gen1 használatával az Azure Data Portalon
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1d1368ef8ffb474c6bec1240f567f043961597fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265570"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>HDInsight-fürtök létrehozása az Azure Data Lake Storage Gen1 használatával az Azure Portal használatával

> [!div class="op_single_selector"]
> * [Az Azure Portal használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [PowerShell használata (alapértelmezett tárhelyhez)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [PowerShell használata (további tárhelyhez)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Erőforrás-kezelő használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Megtudhatja, hogyan hozhat létre az Azure Portalon egy HDInsight-fürtöt egy Azure Data Lake Storage Gen1 fiókkal alapértelmezett tárként vagy egy további tárként. Annak ellenére, hogy a HDInsight-fürt höz nem kötelező további tárhely, ajánlott az üzleti adatokat a további tárfiókokban tárolni.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy megfelelt az alábbi követelményeknek:

* **Azure-előfizetés**. Nyissa meg az [Ingyenes Azure-próbaverzió beszerezhető.](https://azure.microsoft.com/pricing/free-trial/)
* **A Data Lake Storage Gen1 fiók**. Kövesse az [Azure Data Lake Storage Gen1 használatának első](data-lake-store-get-started-portal.md)lépéseit az Azure Portal használatával című útmutatóban. A fiókban létre kell hoznia egy gyökérmappát is.  Ebben a cikkben a __/clusters__ nevű gyökérmappát használja a rendszer.
* **Egy Azure Active Directory szolgáltatásnév.** Ez az útmutató útmutatást nyújt az Azure Active Directoryban (Azure AD) való egyszerű szolgáltatás létrehozásához. Azonban egy egyszerű szolgáltatás létrehozásához, az Azure AD-rendszergazda kell lennie. Ha Ön rendszergazda, kihagyhatja ezt az előfeltételt, és folytathatja.

>[!NOTE]
>Az egyszerű szolgáltatás csak akkor hozhat létre, ha az Azure AD-rendszergazda. Az Azure AD-rendszergazdának létre kell hoznia egy egyszerű szolgáltatás, mielőtt hdinsight-fürtöt hozhat létre a Data Lake Storage Gen1 használatával. Emellett a szolgáltatásnév kell létrehozni egy tanúsítványt, ahogy azt a [Szolgáltatásnév létrehozása tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)című részen leírtak szerint.
>

## <a name="create-an-hdinsight-cluster"></a>HDInsight-fürt létrehozása

Ebben a szakaszban hozzon létre egy HDInsight-fürtdata lake storage gen1 fiókok alapértelmezett vagy a további tároló. Ez a cikk csak a Data Lake Storage Gen1 fiókok konfigurálásának része. Az általános fürtlétrehozási információkat és eljárásokat a [Hadoop-fürtök létrehozása a HDInsightban című témakörben talál.](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Fürt létrehozása a Data Lake Storage Gen1 alapértelmezett tárolójával

HDInsight-fürt létrehozása Data Lake Storage Gen1 fiókkal alapértelmezett tárfiókként:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Kövesse [a Fürtök létrehozása](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) a HDInsight-fürtök létrehozásával kapcsolatos általános információkat.
3. A **Storage** **panelen**az Elsődleges tároló típus csoportban válassza az **Azure Data Lake Storage Gen1 lehetőséget,** majd adja meg a következő adatokat:

    ![Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz")

    * **Válassza ki a Data Lake Store-fiókot:** Válasszon ki egy meglévő Data Lake Storage Gen1 fiókot. Egy meglévő Data Lake Storage Gen1 fiók szükséges.  Lásd: [Előfeltételek](#prerequisites).
    * **Gyökér elérési útja**: Adja meg azt az elérési utat, amelyen a fürtspecifikus fájlokat tárolni kell. A képernyőképen a __/clusters/myhdiadlcluster/__, amelyben a __/clusters mappának__ léteznie kell, és a portál létrehozza a *myhdicluster* mappát.  A *myhdicluster* a fürt neve.
    * **Data Lake Store-hozzáférés:** Konfigurálja a hozzáférést a Data Lake Storage Gen1 fiók és a HDInsight-fürt között. További információt a [Data Lake Storage Gen1 hozzáférés konfigurálása című témakörben talál.](#configure-data-lake-storage-gen1-access)
    * **További tárfiókok:** Add azure storage-fiókok további tárfiókok a fürthöz. További Data Lake Storage Gen1 fiókok hozzáadása úgy történik, hogy a fürt engedélyeket a ddata lake storage gen1 fiókok, míg a Data Lake Storage Gen1 fiók elsődleges tárolási típusként konfigurálása. Lásd: [A Data Lake Storage Gen1 hozzáférés konfigurálása.](#configure-data-lake-storage-gen1-access)

4. A **Data Lake Store-hozzáférésen**kattintson a **Kijelölés gombra,** majd folytassa a fürt létrehozását a [HDInsight Hadoop-fürtök létrehozása című részen leírtak szerint.](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Fürt létrehozása a Data Lake Storage Gen1 szolgáltatással további tárolóként

A következő utasítások hozzon létre egy HDInsight-fürt öt Azure-tárfiók alapértelmezett tárolóként, és a Data Lake Storage Gen1 fiók további tárolóként.

HDInsight-fürt létrehozása Data Lake Storage Gen1 fiókkal további tárfiókként:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Kövesse [a Fürtök létrehozása](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) a HDInsight-fürtök létrehozásával kapcsolatos általános információkat.
3. A **Storage** **panelen**az Elsődleges tároló típus csoportban válassza az **Azure Storage**lehetőséget, majd adja meg a következő adatokat:

    ![Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz")

    * **Kijelölési mód** – Az Azure-előfizetés részét tartalmazó tárfiók megadásához válassza a **Saját előfizetések**lehetőséget, majd válassza ki a tárfiókot. Az Azure-előfizetésen kívüli tárfiók megadásához válassza az **Access kulcs**lehetőséget, majd adja meg a külső tárfiók adatait.

    * **Alapértelmezett tároló** – Használja az alapértelmezett értéket, vagy adja meg a saját nevét.
    * **További tárfiókok** – További Azure-tárfiókok hozzáadása további tárként.
    * **Data Lake Store-hozzáférés** – A Data Lake Storage Gen1 fiók és a HDInsight-fürt közötti hozzáférés konfigurálása. További információt a [Data Lake Storage Gen1 hozzáférés konfigurálása című témakörben talál.](#configure-data-lake-storage-gen1-access)

## <a name="configure-data-lake-storage-gen1-access"></a>A Data Lake Storage Gen1 hozzáférés konfigurálása

Ebben a szakaszban konfigurálja a Data Lake Storage Gen1 hozzáférést a HDInsight-fürtökből egy Azure Active Directory egyszerű szolgáltatás használatával.

### <a name="specify-a-service-principal"></a>Egyszerű szolgáltatás megadása

Az Azure Portalon használhatja egy meglévő egyszerű szolgáltatás, vagy hozzon létre egy újat.

Egyszerű szolgáltatás létrehozása az Azure Portalról:

1. Válassza ki a **Data Lake Store-hozzáférést** a Storage panelről.
1. A **Data Lake Storage Gen1 hozzáférési** panelen válassza **az Új létrehozása lehetőséget.**
1. Válassza **az egyszerű szolgáltatás**lehetőséget, majd kövesse az utasításokat egy egyszerű szolgáltatás létrehozásához.
1. Töltse le a tanúsítványt, ha úgy dönt, hogy a jövőben újra használni szeretné. A tanúsítvány letöltése akkor hasznos, ha ugyanazt a szolgáltatásnévhasználatát szeretné használni, amikor további HDInsight-fürtöket hoz létre.

    ![Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz")

1. A mappahozzáférés konfigurálásához válassza az **Access** lehetőséget.  Lásd: [Fájlengedélyek konfigurálása](#configure-file-permissions).

Meglévő egyszerű szolgáltatás használata az Azure Portalról:

1. Válassza a **Data Lake Store-hozzáférés lehetőséget.**
1. A **Data Lake Storage Gen1 hozzáférési** panelen válassza a **Meglévő használata**lehetőséget.
1. Válassza **az Egyszerű szolgáltatás**lehetőséget, majd válasszon egy egyszerű szolgáltatást.
1. Töltse fel a kiválasztott egyszerű szolgáltatáshoz társított tanúsítványt (.pfx fájlt), majd adja meg a tanúsítvány jelszavát.

    ![Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz")

1. A mappahozzáférés konfigurálásához válassza az **Access** lehetőséget.  Lásd: [Fájlengedélyek konfigurálása](#configure-file-permissions).

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Fájlengedélyek konfigurálása

A konfiguráció attól függően eltérő, hogy a fiókot alapértelmezett tárként vagy további tárfiókként használja-e:

* Alapértelmezett tárolóként használva

  * engedély a Data Lake Storage Gen1 fiók gyökérszintjén
  * a HDInsight-fürttároló gyökérszintjén. Például a __/clusters__ mappát használt korábban az oktatóanyagban.

* További tárolóként való felhasználás

  * Engedély azokon a mappákon, ahol fájlhozzáférésre van szükség.

Engedély hozzárendelése a Data Lake Storage Gen1 fiók gyökérszintjén:

1. A **Data Lake Storage Gen1 hozzáférési** panelen válassza az **Access**lehetőséget. Meg nyílik a **Fájlengedélyek kiválasztása** panel. Felsorolja az összes Data Lake Storage Gen1 fiók az előfizetésben.
1. Vigye az egérmutatót (ne kattintson) az egérmutatót a Data Lake Storage Gen1 fiók nevére, hogy láthatóvá tegye a jelölőnégyzetet, majd jelölje be a jelölőnégyzetet.

    ![Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz")

   Alapértelmezés szerint az __OLVASÁS__, __AZ ÍRÁS__és __A VÉGREHAJTÁS__ beállítás van kiválasztva.

1. Kattintson a lap alján a **Kijelölés** gombra.
1. Engedély hozzárendeléséhez válassza a **Futtatás** lehetőséget.
1. Válassza a **Done** (Kész) lehetőséget.

Engedély hozzárendelése a HDInsight-fürt gyökérszintjén:

1. A **Data Lake Storage Gen1 hozzáférési** panelen válassza az **Access**lehetőséget. Meg nyílik a **Fájlengedélyek kiválasztása** panel. Felsorolja az összes Data Lake Storage Gen1 fiók az előfizetésben.
1. A **Fájlengedélyek kiválasztása** panelen válassza ki a Data Lake Storage Gen1 fiók nevét a tartalom megjelenítéséhez.
1. Jelölje ki a HDInsight fürttároló gyökérét a mappa bal oldalán található jelölőnégyzet bejelölésével. A korábbi képernyőkép szerint a fürttároló gyökérkönyvtára __/fürtök__ mappája, amelyet a Data Lake Storage Gen1 alapértelmezett tárolóként való kiválasztásakor adott meg.
1. Állítsa be a mappa engedélyeit.  Alapértelmezés szerint az olvasás, az írás és a végrehajtás mind ki van jelölve.
1. Kattintson a lap alján a **Kijelölés** gombra.
1. Válassza a **Futtatás** lehetőséget.
1. Válassza a **Done** (Kész) lehetőséget.

Ha a Data Lake Storage Gen1-et használja további tárolóként, csak a HDInsight-fürtből elérni kívánt mappákhoz kell hozzárendelnie az engedélyt. Az alábbi képernyőképen például csak a Data Lake Storage Gen1 fiók **mynewfolder** mappájához biztosít hozzáférést.

![Egyszerű szolgáltatásengedélyek hozzárendelése a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Egyszerű szolgáltatásengedélyek hozzárendelése a HDInsight-fürthöz")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Fürt beállításának ellenőrzése

A fürt telepítése után a fürtpanelen ellenőrizze az eredményeket az alábbi lépések egyikével vagy mindkettővel:

* Annak ellenőrzéséhez, hogy a fürt társított tárolója a megadott Data Lake Storage Gen1 fiók-e, válassza a **Storage-fiókok** lehetőséget a bal oldali ablaktáblában.

    ![Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz")

* Annak ellenőrzéséhez, hogy az egyszerű szolgáltatás megfelelően van-e társítva a HDInsight-fürthöz, válassza a **Data Lake Storage Gen1 access lehetőséget** a bal oldali ablaktáblában.

    ![Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Egyszerű szolgáltatás hozzáadása a HDInsight-fürthöz")

## <a name="examples"></a>Példák

Miután beállította a fürtet a Data Lake Storage Gen1 tárolóként, tekintse meg az alábbi példákat, hogyan használhatja a HDInsight-fürtsegítségével a Data Lake Storage Gen1-ben tárolt adatok elemzését.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Hiv-lekérdezés futtatása adatokon egy Data Lake Storage Gen1 fiókban (elsődleges tárolóként)

Hive-lekérdezés futtatásához használja a Hive-nézetek felületét az Ambari portálon. Az Ambari Hive-nézetek használatáról [a Hive-nézet használata a Hadoop használatával a HDInsight ban című témakörben talál.](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md)

Ha egy Data Lake Storage Gen1-fiókban dolgozik adatokkal, néhány karakterláncot módosítani kell.

Ha például a Data Lake Storage Gen1 használatával létrehozott fürtöt használja elsődleges tárolóként, az adatok elérési útja a következő: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/file*. A Data Lake Storage Gen1 fiókban tárolt mintaadatokból tábla létrehozásához szolgáló Hive-lekérdezés a következő utasításnak tűnik:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Leírások:

* `adl://hdiadlsg1storage.azuredatalakestore.net/`a Data Lake Storage Gen1 fiók gyökere.
* `/clusters/myhdiadlcluster`a fürt létrehozásakor megadott fürtadatok gyökere.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`a lekérdezésben használt mintafájl helye.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Hiv-lekérdezés futtatása adatokon egy Data Lake Storage Gen1 fiókban (további tárolóként)

Ha a létrehozott fürt blob storage alapértelmezett tárolóként használja, a mintaadatok nem szerepelnek a Data Lake Storage Gen1 fiókban, amely további tárolóként használatos. Ebben az esetben először vigye át az adatokat a Blob storage-ból a Data Lake Storage Gen1 fiókba, majd futtassa a lekérdezéseket az előző példában látható módon.

A Blob storage-ból a Data Lake Storage Gen1 fiókba történő másolásáról az alábbi cikkekben talál tájékoztatást:

* [Adatok másolása az Azure Storage-blobok és a Data Lake Storage Gen1 között a Distcp használatával](data-lake-store-copy-data-wasb-distcp.md)
* [Adatok másolása az Azure Storage-blobokból az Azure Storage-blobokból az AdlCopy használatával1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>A Data Lake Storage Gen1 használata Spark-fürttel

A Spark-fürt használatával spark-feladatok futtatásához a Data Lake Storage Gen1 fiókban tárolt adatokon. További információ: [A HDInsight Spark-fürt használata a Data Lake Storage Gen1 adatainak elemzéséhez.](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>A Data Lake Storage Gen1 használata storm topológiában

A Data Lake Storage Gen1 fiók segítségével adatokat írhat egy Storm-topológiából. A forgatókönyv elérésével kapcsolatos tudnivalókért olvassa el [az Azure Data Lake Storage Gen1 használata az Apache Storm segítségével a HDInsight szolgáltatással.](../hdinsight/storm/apache-storm-write-data-lake-store.md)

## <a name="see-also"></a>Lásd még

* [A Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: Hozzon létre egy HDInsight-fürtet a Data Lake Storage Gen1 használatához](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

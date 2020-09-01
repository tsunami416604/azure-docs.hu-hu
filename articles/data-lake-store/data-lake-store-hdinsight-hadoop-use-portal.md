---
title: Azure HDInsight-fürtök létrehozása Data Lake Storage Gen1-Portalon
description: A Azure Portal használatával HDInsight-fürtöket hozhat létre és használhat Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fd49ddcb59e0d0f3a706f566cf0c011116b1501a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229225"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>HDInsight-fürtök létrehozása Azure Data Lake Storage Gen1ekkel a Azure Portal használatával

> [!div class="op_single_selector"]
> * [Az Azure Portal használata](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [A PowerShell használata (az alapértelmezett tárolóhoz)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [A PowerShell használata (további tárhelyhez)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [A Resource Manager használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Ebből a témakörből megtudhatja, hogyan hozhat létre egy HDInsight-fürtöt az alapértelmezett tárolóként vagy egy további tárolóként egy Azure Data Lake Storage Gen1-fiókkal a Azure Portal használatával. Bár a HDInsight-fürtök további tárterülete nem kötelező, ajánlott az üzleti adatait a további Storage-fiókokban tárolni.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy teljesítette a következő követelményeket:

* **Egy Azure-előfizetés**. Nyissa meg az [Azure ingyenes próbaverzióját](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Data Lake Storage Gen1-fiók**. Kövesse a [Azure Data Lake Storage Gen1 használatának első lépései a Azure Portal használatával](data-lake-store-get-started-portal.md)című témakör utasításait. Létre kell hoznia egy legfelső szintű mappát is a fiókban.  Ebben a cikkben egy __/Clusters__ nevű gyökérmappa van használatban.
* **Egy Azure Active Directory egyszerű szolgáltatás**. Ez a útmutató útmutatást nyújt egy egyszerű szolgáltatásnév létrehozásához Azure Active Directory (Azure AD). Egyszerű szolgáltatásnév létrehozásához azonban Azure AD-rendszergazdának kell lennie. Ha Ön rendszergazda, akkor kihagyhatja ezt az előfeltételt, és folytathatja.

>[!NOTE]
>Csak akkor hozhat létre egyszerű szolgáltatásnevet, ha Ön Azure AD-rendszergazda. Az Azure AD-rendszergazdának létre kell hoznia egy egyszerű szolgáltatásnevet ahhoz, hogy HDInsight-fürtöt hozzon létre Data Lake Storage Gen1 használatával. Emellett a szolgáltatásnevet tanúsítványokkal kell létrehozni az [egyszerű szolgáltatásnév létrehozása tanúsítvánnyal](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)című témakörben leírtak szerint.
>

## <a name="create-an-hdinsight-cluster"></a>HDInsight-fürt létrehozása

Ebben a szakaszban egy HDInsight-fürtöt hoz létre Data Lake Storage Gen1 fiókokkal alapértelmezettként vagy a további tárterületként. Ez a cikk csak a Data Lake Storage Gen1-fiókok konfigurálásának részére koncentrál. A fürtök általános létrehozásával kapcsolatos információk és eljárásokért lásd: [Hadoop-fürtök létrehozása a HDInsight-ben](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Fürt létrehozása Data Lake Storage Gen1 alapértelmezett tárolóként

Data Lake Storage Gen1-fiókkal rendelkező HDInsight-fürt létrehozása alapértelmezett Storage-fiókként:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A HDInsight-fürtök létrehozásával kapcsolatos általános információkért kövesse a [fürtök létrehozása](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) című témakört.
3. A **tároló** panel **elsődleges tároló típusa**területén válassza a **Azure Data Lake Storage Gen1**lehetőséget, majd adja meg a következő adatokat:

    ![Egyszerű szolgáltatásnév hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Egyszerű szolgáltatásnév hozzáadása a HDInsight-fürthöz")

    * **Data Lake Store fiók kiválasztása**: válasszon ki egy meglévő Data Lake Storage Gen1 fiókot. Meglévő Data Lake Storage Gen1 fiók szükséges.  Lásd: [Előfeltételek](#prerequisites).
    * **Gyökér elérési útja**: adjon meg egy elérési utat, ahol a fürtre jellemző fájlokat tárolni szeretné. A képernyőképen ez a __/Clusters/myhdiadlcluster/__, amelyben a __/Clusters__ mappa léteznie kell, és a portál létrehozza a *myhdicluster* mappát.  A *myhdicluster* a fürt neve.
    * **Data Lake Store hozzáférés**: konfigurálja a hozzáférést a Data Lake Storage Gen1 fiók és a HDInsight-fürt között. Útmutatásért lásd: [Data Lake Storage Gen1 hozzáférés konfigurálása](#configure-data-lake-storage-gen1-access).
    * **További Storage-fiókok**: az Azure Storage-fiókokat további Storage-fiókként adja hozzá a fürthöz. További Data Lake Storage Gen1-fiókok hozzáadásához adja meg a fürt számára a további Data Lake Storage Gen1 fiókokban tárolt adatokra vonatkozó engedélyeket, miközben az elsődleges tárolási típusként konfigurálja a Data Lake Storage Gen1 fiókot. Lásd: [Data Lake Storage Gen1 hozzáférés konfigurálása](#configure-data-lake-storage-gen1-access).

4. A **Data Lake Store hozzáférés**lapon kattintson a **kiválasztás**elemre, majd folytassa a fürt létrehozásával a [Hadoop-fürtök létrehozása a HDInsight-ben](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md)című témakörben leírtak szerint.

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Fürt létrehozása Data Lake Storage Gen1 kiegészítő tárolóként

Az alábbi utasítások egy Azure Storage-fiókkal rendelkező HDInsight-fürtöt hoznak létre alapértelmezett tárolóként, valamint egy Data Lake Storage Gen1 fiókot további tárolóként.

Data Lake Storage Gen1-fiókkal rendelkező HDInsight-fürt létrehozása további Storage-fiókként:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A HDInsight-fürtök létrehozásával kapcsolatos általános információkért kövesse a [fürtök létrehozása](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) című témakört.
3. A **tároló** panel **elsődleges tároló típusa**területén válassza az **Azure Storage**lehetőséget, majd adja meg a következő adatokat:

    ![Egyszerű szolgáltatásnév hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Egyszerű szolgáltatásnév hozzáadása a HDInsight-fürthöz")

    * **Kiválasztási módszer** – az Azure-előfizetés részét képező Storage-fiók megadásához válassza a **saját előfizetések**lehetőséget, majd válassza ki a Storage-fiókot. Az Azure-előfizetésen kívüli Storage-fiók megadásához válassza a **hozzáférési kulcs**lehetőséget, majd adja meg a külső Storage-fiók adatait.

    * **Alapértelmezett tároló** – használja az alapértelmezett értéket, vagy adja meg a saját nevét.
    * **További Storage-fiókok** – további tárterületként további Azure Storage-fiókokat adhat hozzá.
    * **Data Lake Store hozzáférés** – konfigurálja a Data Lake Storage Gen1 fiók és a HDInsight-fürt közötti hozzáférést. Útmutatásért lásd: [Data Lake Storage Gen1 hozzáférés konfigurálása](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Data Lake Storage Gen1 hozzáférés konfigurálása

Ebben a szakaszban egy Azure Active Directory egyszerű szolgáltatásnév használatával konfigurálja Data Lake Storage Gen1 hozzáférést a HDInsight-fürtökhöz.

### <a name="specify-a-service-principal"></a>Egyszerű szolgáltatásnév meghatározása

A Azure Portal használhat meglévő szolgáltatásnevet, vagy létrehozhat egy újat.

Egyszerű szolgáltatásnév létrehozása a Azure Portalből:
1. Lásd: [egyszerű szolgáltatásnév és tanúsítványok létrehozása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) Azure Active Directory használatával.

Meglévő egyszerű szolgáltatásnév használata a Azure Portalből:

1. Az egyszerű szolgáltatásnév tulajdonosi engedélyekkel kell rendelkeznie a Storage-fiókhoz. Lásd: [az egyszerű szolgáltatásnév engedélyeinek beállítása tulajdonosként a Storage-fiókban](#configure-serviceprincipal-permissions)
1. Válassza **Data Lake Store a hozzáférés**lehetőséget.
1. A **Data Lake Storage Gen1 hozzáférés** panelen válassza a **meglévő használata**lehetőséget.
1. Válassza ki az **egyszerű szolgáltatásnév**elemet, majd válasszon ki egy szolgáltatásnevet.
1. Töltse fel a kiválasztott egyszerű szolgáltatáshoz társított tanúsítványt (. pfx fájlt), majd adja meg a tanúsítvány jelszavát.

    ![Egyszerű szolgáltatásnév hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Egyszerű szolgáltatásnév hozzáadása a HDInsight-fürthöz")

1. Válassza a **hozzáférés** lehetőséget a mappa elérésének konfigurálásához.  Lásd: [fájlengedélyek konfigurálása](#configure-file-permissions).

### <a name="set-up-permissions-for-the-service-principal-to-be-owner-on-the-storage-account"></a><a name="configure-serviceprincipal-permissions"></a>Engedélyek beállítása az egyszerű szolgáltatásnév számára a Storage-fiók tulajdonosaként
1. A Storage-fiók Access Control (IAM) paneljén kattintson a szerepkör-hozzárendelés hozzáadása lehetőségre. 
2. A szerepkör-hozzárendelés hozzáadása panelen válassza a szerepkör tulajdonosként lehetőséget, majd válassza ki az egyszerű szolgáltatásnevet, és kattintson a Mentés gombra.

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Fájlengedélyek konfigurálása

A konfiguráció eltérő attól függően, hogy a fiókot alapértelmezett tárolóként vagy további Storage-fiókként használja-e a rendszer:

* Alapértelmezett tárolóként használatos

  * engedély a Data Lake Storage Gen1 fiók legfelső szintjén
  * engedély a HDInsight-fürt tárterületének legfelső szintjén. Például az oktatóanyag korábbi részében használt __/Clusters__ mappa.

* Használat további tárolóként

  * Engedély azon mappák esetében, amelyekhez fájl-hozzáférésre van szükség.

Engedély kiosztása a Data Lake Storage Gen1 fiók gyökérszintű szintjén:

1. A **Data Lake Storage Gen1 hozzáférés** panelen válassza a **hozzáférés**lehetőséget. Megnyílik a **fájl engedélyeinek kiválasztása** panel. Felsorolja az előfizetés összes Data Lake Storage Gen1 fiókját.
1. Vigye a kurzort (ne kattintson a gombra) a Data Lake Storage Gen1 fiók neve fölé, hogy láthatóvá tegye a jelölőnégyzetet, majd jelölje be a jelölőnégyzetet.

    ![Egyszerű szolgáltatásnév hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Egyszerű szolgáltatásnév hozzáadása a HDInsight-fürthöz")

   Alapértelmezés szerint az __olvasás__, __írás__és __végrehajtás__ elem van kiválasztva.

1. Kattintson a lap alján található **kiválasztás** gombra.
1. Válassza a **Futtatás** elemet az engedély kiosztásához.
1. Válassza a **Kész** lehetőséget.

Engedély kiosztása a HDInsight-fürt gyökérszintű szintjén:

1. A **Data Lake Storage Gen1 hozzáférés** panelen válassza a **hozzáférés**lehetőséget. Megnyílik a **fájl engedélyeinek kiválasztása** panel. Felsorolja az előfizetés összes Data Lake Storage Gen1 fiókját.
1. A **fájl engedélyeinek kiválasztása** panelen válassza ki a Data Lake Storage Gen1 fiók nevét a tartalom megjelenítéséhez.
1. Jelölje ki a HDInsight-fürt gyökerét a mappa bal oldalán található jelölőnégyzet bejelölésével. A képernyőképen korábban a fürt __/Clusters__ a Data Lake Storage Gen1 alapértelmezett tárolóként való kiválasztásakor megadott mappa.
1. Állítsa be a mappára vonatkozó engedélyeket.  Alapértelmezés szerint az olvasás, írás és végrehajtás elem van kiválasztva.
1. Kattintson a lap alján található **kiválasztás** gombra.
1. Válassza a **Futtatás** lehetőséget.
1. Válassza a **Kész** lehetőséget.

Ha a Data Lake Storage Gen1 kiegészítő tárolóként használja, csak a HDInsight-fürtből elérni kívánt mappákhoz kell engedélyeket rendelnie. Az alábbi képernyőképen például csak a **mynewfolder** mappához biztosít hozzáférést Data Lake Storage Gen1 fiókban.

![Egyszerű szolgáltatás engedélyeinek kiosztása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Egyszerű szolgáltatás engedélyeinek kiosztása a HDInsight-fürthöz")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Fürt beállításának ellenőrzése

A fürt telepítésének befejezése után a fürt paneljén ellenőrizze az eredményeket a következő lépések egyikével vagy mindkettővel:

* Annak ellenőrzéséhez, hogy a fürthöz társított tár a megadott Data Lake Storage Gen1 fiók, a bal oldali ablaktáblán válassza a **Storage-fiókok** lehetőséget.

    ![Egyszerű szolgáltatásnév hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Egyszerű szolgáltatásnév hozzáadása a HDInsight-fürthöz")

* Annak ellenőrzéséhez, hogy a szolgáltatásnév megfelelően van-e társítva a HDInsight-fürthöz, válassza a bal oldali ablaktábla **Data Lake Storage Gen1 hozzáférés** elemét.

    ![Egyszerű szolgáltatásnév hozzáadása a HDInsight-fürthöz](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Egyszerű szolgáltatásnév hozzáadása a HDInsight-fürthöz")

## <a name="examples"></a>Példák

Miután beállította a fürtöt Data Lake Storage Gen1 tárolóként, tekintse át a következő példákat, hogy miként lehet a HDInsight-fürt használatával elemezni a Data Lake Storage Gen1 tárolt adatait.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Struktúra-lekérdezés futtatása egy Data Lake Storage Gen1-fiókban (elsődleges tárolóként)

A kaptár-lekérdezések futtatásához használja a Ambari-portál kaptár-nézetek felületét. A Ambari-struktúra nézeteinek használatáról további információt [a kaptár nézet használata a Hadoop a HDInsight-ben](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md)című témakörben talál.

Ha Data Lake Storage Gen1-fiókban található adattal dolgozik, néhány karakterláncot meg kell változtatni.

Ha például az Data Lake Storage Gen1 elsődleges tárolóként létrehozott fürtöt használja, az adatelérési út a következő: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/Path/to/file*. A Data Lake Storage Gen1-fiókban tárolt mintaadatok táblázatának létrehozására szolgáló kaptár-lekérdezés a következő utasításhoz hasonlít:

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

Leírások

* `adl://hdiadlsg1storage.azuredatalakestore.net/` a Data Lake Storage Gen1 fiók gyökere.
* `/clusters/myhdiadlcluster` a fürt létrehozásakor megadott fürtkonfiguráció gyökerét adja meg.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` a lekérdezésben használt minta fájljának helye.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Struktúra-lekérdezés futtatása egy Data Lake Storage Gen1-fiókban (további tárolóként)

Ha a létrehozott fürt blob Storage-t használ alapértelmezett tárolóként, a mintaadatok nem szerepelnek a további tárolóként használt Data Lake Storage Gen1 fiókban. Ilyen esetben először vigye át az adatait a blob Storage-ból a Data Lake Storage Gen1-fiókba, majd futtassa a lekérdezéseket az előző példában látható módon.

Az adatok blob Storage-ból Data Lake Storage Gen1-fiókba való másolásával kapcsolatos információkért tekintse meg a következő cikkeket:

* [Az Distcp használata az Azure Storage-blobok és a Data Lake Storage Gen1 közötti adatmásoláshoz](data-lake-store-copy-data-wasb-distcp.md)
* [Adatok másolása az Azure Storage-blobokból a Data Lake Storage Gen1ba a AdlCopy használatával](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Data Lake Storage Gen1 használata Spark-fürttel

A Spark-fürtöket a Spark-feladatok futtatására használhatja Data Lake Storage Gen1 fiókban tárolt adatokon. További információ: a [HDInsight Spark-fürt használata az adatok elemzéséhez Data Lake Storage Gen1ban](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Data Lake Storage Gen1 használata Storm-topológiában

A Data Lake Storage Gen1 fiók használatával írhat adatokat egy Storm-topológiából. Ennek a forgatókönyvnek a megvalósításával kapcsolatos útmutatásért lásd: [Azure Data Lake Storage Gen1 használata a Apache Storm és a HDInsight használatával](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Lásd még

* [Data Lake Storage Gen1 használata az Azure HDInsight-fürtökkel](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: hozzon létre egy HDInsight-fürtöt Data Lake Storage Gen1 használatához](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

---
title: "Apache Spark-fürt létrehozása az Azure HDInsightban | Microsoft Docs"
description: "HDInsight Spark rövid útmutató az Apache Spark-fürtök HDInsightban történő létrehozásáról."
keywords: "spark gyorsútmutató,interaktív spark,interaktív lekérdezés,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/18/2018
ms.author: jgao
ms.openlocfilehash: 1dbad36b7420791e70066263a566f1820823ad27
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Apache Spark-fürt létrehozása az Azure HDInsightban

Megtudhatja, hogyan hozhat létre Apache Spark-fürtöt az Azure HDInsightban, és hogyan futtathat Spark SQL-lekérdezéseket a Hive-táblákon. A Spark on HDInsight további információiért lásd: [Áttekintés: Apache Spark on Azure HDInsight](apache-spark-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>HDInsight Spark-fürt létrehozása

Hozzon létre egy HDInsight Spark-fürtöt az [Azure Resource Manager-sablon](../hdinsight-hadoop-create-linux-clusters-arm-templates.md) használatával. A sablon megtalálható a [GitHubon](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Egyéb fürtlétrehozási módszerek: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md).

1. Az alábbi képre kattintva megnyithatja a sablont az Azure Portalon.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Írja be a következő értékeket:

    ![HDInsight Spark-fürt létrehozása egy Azure Resource Manager-sablonnal](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Spark-fürt létrehozása a HDInsightban egy Azure Resource Manager-sablonnal")

    * **Előfizetés**: Válassza ki a fürt létrehozásához használt Azure-előfizetést.
    * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévőt. Az erőforráscsoport kezeli a projektek Azure-erőforrásait.
    * **Hely**: Válasszon egy helyet az erőforráscsoportnak. A sablon ezt a helyet használja a fürt létrehozásához, valamint az alapértelmezett fürttárolóhoz.
    * **ClusterName**: Adjon nevet a létrehozni kívánt HDInsight-fürtnek.
    * **A fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az admin.
    * **SSH-felhasználónév és -jelszó**.

3. Jelölje be az **Elfogadom a fenti feltételeket** és a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Vásárlás** elemre. Egy új csempe jelenik meg **Deploying Template deployment** (Üzembe helyezés – Sablon telepítése) címmel. A fürt létrehozása nagyjából 20 percet vesz igénybe.

Ha problémába ütközik a HDInsight-fürtök létrehozása során, előfordulhat, hogy nem rendelkezik a szükséges engedélyekkel. További információért tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> A cikkben leírt eljárás létrehoz egy Spark-fürtöt, amely [Azure Storage-blobokat használ fürttárolóként](../hdinsight-hadoop-use-blob-storage.md). Olyan Spark-fürtöt is létrehozhat, amely az [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md)-t használja alapértelmezett tárolóként. Útmutatás: [Create an HDInsight cluster with Data Lake Store](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) (HDInsight-fürt létrehozása a Data Lake Store-ral).
>
>

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

A [Jupyter notebook](http://jupyter.org) egy interaktív notebook-környezet, amely számos programozási nyelv támogatásával lehetővé teszi az adatai használatát, a kódok és markdown-szövegek egyesítését, valamint egyszerű megjelenítések elvégzését. A Spark on HDInsight része a [Zeppelin notebook](apache-spark-zeppelin-notebook.md) is. Az oktatóanyag a Jupyter notebookot alkalmazza.

**Jupyter notebook létrehozása**

1. Nyissa meg az [Azure portált](https://portal.azure.com/).

2. Nyissa meg a létrehozott Spark-fürtöt. Az utasításokért lásd: [Fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

3. A portálon kattintson a **Fürt irányítópultja** lehetőségre, majd a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

   ![A Jupyter notebook megnyitása interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "A Jupyter notebook megnyitása interaktív Spark SQL-lekérdezés futtatásához")

   > [!NOTE]
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Új notebook létrehozásához kattintson a **New** (Új), majd a **PySpark** elemre. A Jupyter notebookok a HDInsight-fürtökön három kernelt támogatnak – **PySpark**, **PySpark3** és **Spark**. Az oktatóanyag a **PySpark** kernelt alkalmazza. A kernelekkel, valamint a **PySpark** használatának előnyeivel kapcsolatos további információkért lásd: [Jupyter notebookkernelek használata Apache Spark-fürtökkel a HDInsightban](apache-spark-jupyter-notebook-kernels.md).

   ![Jupyter notebook létrehozása interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter notebook létrehozása interaktív Spark SQL-lekérdezés futtatásához")

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

4. Ha a felső részen a notebook nevére kattint, megadhat egy könnyen megjegyezhető nevet.

    ![Azon Jupyter notebook elnevezése, amelyből interaktív Spark lekérdezést futtat](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Azon Jupyter notebook elnevezése, amelyből interaktív Spark lekérdezést futtat")

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Spark SQL-utasítások futtatása Hive-táblán

Az SQL az adatok lekérdezéséhez és meghatározásához leggyakrabban és legszélesebb körben használt nyelv. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL-szintaxissal való feldolgozásához.

A Spark SQL az SQL és a HiveQL lekérdezési nyelvet is támogatja. A segítségével kötés végezhető Pythonban, Scalában és Javában. Ezzel számos különféle helyen, például külső adatbázisokban, strukturált adatfájlokban (például: JSON) és Hive-táblákban tárolt adatok kérdezhetők le.

Az adatok Hive-tábla helyett CSV-fájlból történő beolvasásáról az [Interaktív lekérdezések futtatása Spark-fürtökön a HDInsightban](apache-spark-load-data-run-query.md) című részben találhat példát.

**Spark SQL futtatása**

1. A notebook első indításakor a kernel a háttérben elvégez néhány feladatot. Várja meg, hogy a kernel elkészüljön. A kernel akkor áll készen, ha a neve mellett a notebookban egy üres kör látható. A teli kör azt jelenti, hogy a kernel foglalt.

    ![Hive-lekérdezés a HDInsight Sparkban](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Hive-lekérdezés a HDInsight Sparkban")

2. Ha a kernel készen áll, illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak futtatásához. A kimeneti felsorolásban szerepelnie kell egy `hivesampletable` táblának, amely alapértelmezés szerint elérhető a fürtön.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    ![Hive-lekérdezés a HDInsight Sparkban](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive-lekérdezés a HDInsight Sparkban")

    Ha a Jupyter notebookot HDInsight Spark-fürttel használja, egy előre beállított `sqlContext` elemet kap, amelyet Hive-lekérdezések Spark SQL-lel végzett futtatásához használhat. A `%%sql` megadja a Jupyter notebook számára, hogy az előre beállított `sqlContext` elemet használja a Hive-lekérdezés futtatásához. A lekérdezés lekérdezi az első 10 sort egy Hive-táblából (**hivesampletable**), amely alapértelmezés szerint minden HDInsight-fürtben megtalálható. Az `%%sql` magicről és az előre beállított környezetekről a [HDInsight-fürtökhöz elérhető Jupyter-kerneleket ismertető cikkben](apache-spark-jupyter-notebook-kernels.md) talál további információt.

    Minden alkalommal, amikor a Jupyterben lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik.
    
2. Futtasson egy másik lekérdezést a `hivesampletable` adatainak megtekintéséhez.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    A képernyő frissül, és megjeleníti a lekérdezés kimenetét.

    ![Hive-lekérdezés kimenete a HDInsight Sparkban](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Hive-lekérdezés kimenete a HDInsight Sparkban")

2. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. A notebook leállítása felszabadítja a fürt erőforrásait.

3. Ha később tervezi végrehajtani a következő lépéseket, törölje a jelen cikkben létrehozott HDInsight-fürtöt. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>További lépések 

Ebből a cikkből megtudhatta, hogyan hozható létre egy HDInsight Spark-fürt és hogyan futtatható egy alapszintű Spark SQL-lekérdezés. Folytassa a következő cikkel, amelyben megtudhatja, hogyan használhatja a HDInsight Spark-fürtöt interaktív lekérdezések mintaadatokon való futtatására.

> [!div class="nextstepaction"]
>[Interaktív lekérdezések futtatása HDInsight Spark-fürtön](apache-spark-load-data-run-query.md)




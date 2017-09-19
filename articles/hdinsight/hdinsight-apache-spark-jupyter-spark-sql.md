---
title: "Apache Spark-fürt létrehozása az Azure HDInsightban | Microsoft Docs"
description: "HDInsight Spark rövid útmutató az Apache Spark-fürtök HDInsightban történő létrehozásáról."
keywords: "spark gyorsútmutató,interaktív spark,interaktív lekérdezés,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 78051b9df15c62d4caf56d800c9a5f4421ea2254
ms.contentlocale: hu-hu
ms.lasthandoff: 09/13/2017

---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Apache Spark-fürt létrehozása az Azure HDInsightban

Ebből a cikkből megtudhatja, hogyan hozható létre egy Apache Spark-fürt az Azure HDInsightban, majd hogyan futtatható Spark SQL-lekérdezés egy Hive-táblán. A Spark on HDInsight további információiért lásd: [Áttekintés: Apache Spark on Azure HDInsight](hdinsight-apache-spark-overview.md).

   ![Gyorsútmutató-diagram, amely leírja az Apache Spark-fürtök Azure HDInsight rendszeren való létrehozásának lépéseit](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "Spark gyorsútmutató az Apache Spark használatához a HDInsight rendszeren. Szemléltetett lépések: fürt létrehozása; interaktív Spark-lekérdezés futtatása")

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása még ma](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>HDInsight Spark-fürt létrehozása

Ebben a szakaszban egy HDInsight Spark-fürtöt hozhat létre egy [Azure Resource Manager-sablonnal](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Egyéb fürtlétrehozási módszerek: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).

1. Az alábbi képre kattintva megnyithatja a sablont az Azure Portalon.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Írja be a következő értékeket:

    ![HDInsight Spark-fürt létrehozása egy Azure Resource Manager-sablonnal](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Spark-fürt létrehozása a HDInsightban egy Azure Resource Manager-sablonnal")

    * **Előfizetés**: Válassza ki a fürthöz használni kívánt Azure-előfizetést.
    * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévőt. Az erőforráscsoport kezeli a projektek Azure-erőforrásait.
    * **Hely**: Válasszon egy helyet az erőforráscsoportnak. A sablon ezt a helyet használja a fürt létrehozásához, valamint az alapértelmezett fürttárolóhoz.
    * **ClusterName**: Adjon nevet a létrehozni kívánt HDInsight-fürtnek.
    * **Spark-verzió**Válassza ki a **2.0**-s verziót a fürtön telepíteni kívánt verzióként.
    * **A fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az admin.
    * **SSH-felhasználónév és -jelszó**.

   Jegyezze fel ezeket az értékeket.  Az oktatóanyag későbbi részében szüksége lesz rájuk.

3. Jelölje be az **Elfogadom a fenti feltételeket** és a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Vásárlás** elemre. Ekkor egy új csempe jelenik meg Submitting deployment for Template deployment (Üzemelő példány elküldése sablon üzemelő példányhoz) címmel. A fürt létrehozása nagyjából 20 percet vesz igénybe.

Ha problémába ütközik a HDInsight-fürtök létrehozása során, előfordulhat, hogy nem rendelkezik a szükséges engedélyekkel. További információért tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> A cikkben leírt eljárás létrehoz egy Spark-fürtöt, amely [Azure Storage-blobokat használ fürttárolóként](hdinsight-hadoop-use-blob-storage.md). Olyan Spark-fürtöt is létrehozhat, amely az [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md)-t használja alapértelmezett tárolóként. Útmutatás: [Create an HDInsight cluster with Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) (HDInsight-fürt létrehozása a Data Lake Store-ral).
>
>

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Spark SQL-utasítások futtatása Hive-táblán

Az SQL az adatok lekérdezéséhez és meghatározásához leggyakrabban és legszélesebb körben használt nyelv. A Spark megalkotói ezt a tudást kihasználva megnyitották ezt a jól ismert adatlekérdezési nyelvet az elemzők szélesebb rétege előtt, akik Hadoop elosztott fájlrendszeren (HDFS-en) tárolt adatokkal szeretnének dolgozni. Ez az ajánlat a Spark SQL. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL szintaxissal való feldolgozásához.

A Spark SQL az SQL és a HiveQL lekérdezési nyelvet is támogatja. A segítségével kötés végezhető Pythonban, Scalában és Javában. Ezzel számos különféle helyen, például külső adatbázisokban, strukturált adatfájlokban (például: JSON) és Hive-táblákban tárolt adatok kérdezhetők le.

### <a name="running-spark-sql-on-an-hdinsight-cluster"></a>A Spark SQL futtatása HDInsight-fürtön

Ha a HDInsight Spark-fürthöz konfigurált Jupyter notebookot használja, egy előre beállított `sqlContext` elemet kap, amelyet Hive-lekérdezések Spark SQL-lel végzett futtatásához használhat. Ebben a szakaszban megtudhatja, hogyan indíthat egy Jupyter notebookot, és ezután hogyan futtathat egy alapszintű Spark SQL-lekérdezést az összes HDInsight-fürtön elérhető meglévő Hive-táblán (**hivesampletable**).

1. Nyissa meg az [Azure portált](https://portal.azure.com/).

2. Ha rögzítette a fürtöt az irányítópulton, a fürt paneljének megnyitásához kattintson a fürt csempéjére az irányítópulton.

    Ha nem rögzítette a fürtöt az irányítópulton, a bal oldali panelen kattintson a **HDInsight-fürtök** elemre, majd a létrehozott fürtre.

3. A **Gyorshivatkozások** menüben kattintson a **Fürt irányítópultjai** lehetőségre, majd a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

   ![A Jupyter notebook megnyitása interaktív Spark SQL-lekérdezés futtatásához](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "A Jupyter notebook megnyitása interaktív Spark SQL-lekérdezés futtatásához")

   > [!NOTE]
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Hozzon létre egy notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.

   ![Jupyter notebook létrehozása interaktív Spark SQL-lekérdezés futtatásához](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter notebook létrehozása interaktív Spark SQL-lekérdezés futtatásához")

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

4. Ha a felső részen a notebook nevére kattint, megadhat egy könnyen megjegyezhető nevet.

    ![A Jupyter notebook elnevezése, amelyből interaktív Spark lekérdezést futtat](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "A Jupyter notebook elnevezése, amelyből interaktív Spark lekérdezést futtat")

5.  Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak futtatásához. A lenti kódban az (sql magic nevű) `%%sql` megadja a Jupyter notebook számára, hogy az előre beállított `sqlContext` elemet használja a Hive-lekérdezés futtatásához. A lekérdezés lekérdezi az első 10 sort egy Hive-táblából (**hivesampletable**), amely alapértelmezés szerint minden HDInsight-fürtben elérhető.

        %%sql
        SELECT * FROM hivesampletable LIMIT 10

    ![Hive-lekérdezés a HDInsight Sparkban](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive-lekérdezés a HDInsight Sparkban")

    Az `%%sql` magicről és az előre beállított környezetekről a [HDInsight-fürtökhöz elérhető Jupyter-kerneleket ismertető cikkben](hdinsight-apache-spark-jupyter-notebook-kernels.md) talál további információt.

    > [!NOTE]
    > Minden alkalommal, amikor a Jupyterben lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezése után ez a jel üres körre változik.
    >
    >
    
6. A képernyő frissül, és megjeleníti a lekérdezés kimenetelét.

    ![Hive-lekérdezés kimenete a HDInsight Sparkban](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Hive-lekérdezés kimenete a HDInsight Sparkban")

7. Az alkalmazás futtatása után állítsa le a notebookot a fürt erőforrásainak felszabadítása érdekében. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre.

8. Ha később tervezi végrehajtani a következő lépéseket, törölje a jelen cikkben létrehozott HDInsight-fürtöt. 

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-step"></a>Következő lépés 

Ebből a cikkből megtudhatta, hogyan hozható létre egy HDInsight Spark-fürt és hogyan futtatható egy alapszintű Spark SQL-lekérdezés. Folytassa a következő cikkel, amelyben megtudhatja, hogyan használhatja a HDInsight Spark-fürtöt interaktív lekérdezések mintaadatokon való futtatására.

> [!div class="nextstepaction"]
>[Interaktív lekérdezések futtatása HDInsight Spark-fürtön](hdinsight-apache-spark-load-data-run-query.md)





---
title: 'Rövid útmutató: Spark-fürt létrehozása a HDInsight-ben a Azure Portal használatával'
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure Portal egy Azure Spark-fürt létrehozásához az Azure HDInsightban, illetve egy Spark SQL futtatásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 09/27/2019
ms.custom: mvc
ms.openlocfilehash: 214a88ad8b0f5dd0352004edc125705ad2f04fc7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494142"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Rövid útmutató: Apache Spark-fürt létrehozása az Azure HDInsight Azure Portal használatával

Megtudhatja, hogyan hozhat létre Apache Spark-fürtöt az Azure HDInsightban, és hogyan futtathat Spark SQL-lekérdezéseket a Hive-táblákon. Az Apache Spark a memóriában végzett feldolgozás segítségével teszi lehetővé a gyors adatelemzést és fürtszámítást. A Spark on HDInsight további információiért lásd: [Áttekintés: Apache Spark on Azure HDInsight](apache-spark-overview.md).

Ebben a rövid útmutatóban egy HDInsight Spark-fürtöt hoz létre az Azure Portal használatával. A fürt Azure Storage-blobokat használ fürttárolóként. A 2. generációs Data Lake Storage használatáról [a fürtök HDInsightban történő beállításáról szóló rövid útmutatóban](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) talál további információt.

> [!IMPORTANT]  
> A HDInsight-fürtök számlázása percenként történik, akár használja őket, akár nem. Mindig törölje a fürtöt, ha már nem használja. További információkért lásd a cikk [Az erőforrások eltávolítása](#clean-up-resources) című szakaszát.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-an-hdinsight-spark-cluster"></a>HDInsight Spark-fürt létrehozása

1. A Azure Portal válassza az **erőforrás létrehozása** > **Analytics** > **HDInsight**lehetőséget.

    ![Erőforrás-HDInsight Azure Portal létrehozása](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster.png "HDInsight Azure Portal")

1. Az **Alapvető beállítások** területen adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Előfizetés  | Válassza ki a legördülő menüből a fürthöz használt Azure-előfizetést. Az ehhez a rövid útmutatóhoz használt előfizetés az **Azure**. |
    |Erőforráscsoport | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az ehhez a rövid útmutatóhoz használt erőforráscsoport-név **myResourceGroup**. |
    |Fürt neve | Adjon nevet a HDInsight Spark-fürtnek. Az ehhez a rövid útmutatóhoz használt fürt neve **myspark2019**.|
    |Földrajzi egység   | Válasszon egy helyet az erőforráscsoportnak. A sablon ezt a helyet használja a fürt létrehozásához, valamint az alapértelmezett fürttárolóhoz. A rövid útmutatóhoz használt hely az **USA keleti**régiója. |
    |Fürttípus| Válassza a **Spark** lehetőséget a fürt típusaként.|
    |Fürt verziója|Ez a mező automatikusan feltölti az alapértelmezett verziót a fürt típusának kiválasztása után.|
    |Fürt bejelentkezési felhasználóneve| Adja meg a fürt bejelentkezési felhasználónevét.  Az alapértelmezett név a *rendszergazda*. Ezzel a fiókkal a gyors útmutatóban később bejelentkezhet a Jupyter-jegyzetfüzetbe. |
    |Fürt bejelentkezési jelszava| Adja meg a fürt bejelentkezési jelszavát. |
    |Secure Shell- (SSH-) felhasználónév| Adja meg az SSH-felhasználónevet. A rövid útmutatóhoz használt SSH-felhasználónév: **sshuser**. Alapértelmezés szerint ennek a fióknak ugyanaz a jelszava, mint a *fürt bejelentkezési felhasználónevéhez* tartozó fióknak. |

    ![HDInsight Spark-fürt alapkonfigurációinak létrehozása](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Spark-fürt létrehozása a HDInsight-ben az alapvető konfigurációk")

    Válassza a **Next (tovább): storage > >** lehetőséget a **tárterület** oldalának folytatásához.

1. A **Storage** területen adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Elsődleges tároló típusa|Használja az alapértelmezett értéket az **Azure Storage**-ban.|
    |Kiválasztási módszer|Használja az alapértelmezett értéket a **listából**.|
    |Az elsődleges tárfiók|Használja az automatikusan feltöltött értéket.|
    |Tároló|Használja az automatikusan feltöltött értéket.|

    ![HDInsight Spark-fürt alapkonfigurációinak létrehozása](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage-spark.png "Spark-fürt létrehozása a HDInsight-ben az alapvető konfigurációk")

    A folytatáshoz válassza a **felülvizsgálat + létrehozás** lehetőséget.

1. A **felülvizsgálat + létrehozás**területen válassza a **Létrehozás**lehetőséget. A fürt létrehozása nagyjából 20 percet vesz igénybe. A következő munkamenetre csak a fürt létrehozását követően lehet továbblépni.

Ha problémába ütközik a HDInsight-fürtök létrehozása során, előfordulhat, hogy nem rendelkezik a szükséges engedélyekkel. További információért tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

A Jupyter Notebook egy interaktív notebook-környezet, amely számos programozási nyelvet támogat. A notebook lehetővé teszi az adatai használatát, a kódok és markdown-szövegek egyesítését, valamint egyszerű vizualizációk elvégzését.

1. Nyissa meg az [Azure portált](https://portal.azure.com).

1. Válassza a **HDInsight-fürtök** lehetőséget, majd a létrehozott fürtöt.

    ![HDInsight-fürt megnyitása az Azure Portalon](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

1. A portálon válassza a **Fürt irányítópultja** lehetőséget, majd a **Jupyter Notebook** elemet. Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

   ![Az interaktív Spark SQL-lekérdezés futtatásához Jupyter Notebook megnyitása](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Az interaktív Spark SQL-lekérdezés futtatásához Jupyter Notebook megnyitása")

1. Új notebook létrehozásához válassza a **New** > **PySpark** (Új > PySpark) lehetőséget.

   ![Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához")

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

## <a name="run-spark-sql-statements"></a>Spark SQL-utasítások futtatása

Az SQL az adatok lekérdezéséhez és meghatározásához leggyakrabban és legszélesebb körben használt nyelv. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL-szintaxissal való feldolgozásához.

1. Ellenőrizze, hogy a kernel készen áll-e. A kernel akkor áll készen, ha a neve mellett a notebookban egy üres kör látható. A teli kör azt jelenti, hogy a kernel foglalt.

    ![Apache Hive lekérdezés a HDInsight Spark1](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Struktúra-lekérdezés a HDInsight Spark1")

    A notebook első indításakor a kernel a háttérben elvégez néhány feladatot. Várja meg, hogy a kernel elkészüljön.

1. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak futtatásához. A parancs felsorolja a fürtön található Hive-táblákat:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Ha a Jupyter notebookot HDInsight Spark-fürttel használja, egy előre beállított `sqlContext` elemet kap, amelyet Hive-lekérdezések Spark SQL-lel végzett futtatásához használhat. A `%%sql` megadja a Jupyter notebook számára, hogy az előre beállított `sqlContext` elemet használja a Hive-lekérdezés futtatásához. A lekérdezés lekérdezi az első 10 sort egy Hive-táblából (**hivesampletable**), amely alapértelmezés szerint minden HDInsight-fürtben megtalálható. Az eredmények lekérdezése körülbelül 30 másodpercet vesz igénybe. A kimenet a következőképpen fog kinézni:

    ![Apache Hive lekérdezés a HDInsight Spark2](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Struktúra-lekérdezés a HDInsight Spark2")

    Minden alkalommal, amikor a Jupyterben lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik.

1. Futtasson egy másik lekérdezést a `hivesampletable` adatainak megtekintéséhez.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A képernyő frissül, és megjeleníti a lekérdezés kimenetét.

    ![Struktúra lekérdezési kimenete a HDInsight Sparkban](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Struktúra lekérdezési kimenete a HDInsight Sparkban")

1. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. A notebook leállítása felszabadítja a fürt erőforrásait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A HDInsight az Azure Storage vagy az Azure Data Lake Storage tárhelyein tárolja az adatokat, így a nem használt fürtök biztonságosan törölhetők. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. Ha azt tervezi, hogy rögtön elvégzi a [További lépések](#next-steps) szakaszban található oktatóanyagot is, akkor érdemes lehet megtartani a fürtöt.

Lépjen vissza az Azure Portalra és válassza a **Törlés** lehetőséget.

![HDInsight-fürt törlése Azure Portal](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésekor a rendszer a HDInsight Spark-fürtöt és az alapértelmezett tárfiókot is törli.

## <a name="next-steps"></a>További lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozható létre egy HDInsight Spark-fürt, illetve hogyan futtatható egy alapszintű Spark SQL-lekérdezés. Folytassa a következő oktatóanyaggal, amelyben megtudhatja, hogyan használhatja a HDInsight Spark-fürtöt interaktív lekérdezések mintaadatokon való futtatására.

> [!div class="nextstepaction"]
> [Interaktív lekérdezések futtatása Apache Sparkon](./apache-spark-load-data-run-query.md)

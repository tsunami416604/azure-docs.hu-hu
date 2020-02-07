---
title: 'Rövid útmutató: Spark-fürt létrehozása a HDInsight-ben a Azure Portal használatával'
description: Ez a rövid útmutató bemutatja, hogyan használható a Azure Portal egy Apache Spark-fürt létrehozásához az Azure HDInsight, és hogyan futtathat Spark SQL-lekérdezést.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 09/27/2019
ms.custom: mvc
ms.openlocfilehash: 070f1f158ef6fb1d4b8f6a41f5b36d7cb16b24da
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048480"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Rövid útmutató: Apache Spark-fürt létrehozása az Azure HDInsight Azure Portal használatával

Ebben a rövid útmutatóban a Azure Portal használatával hoz létre egy Apache Spark-fürtöt az Azure HDInsight-ben. Ezután létrehozhat egy Jupyter-jegyzetfüzetet, és használhatja a Spark SQL-lekérdezések futtatásához Apache Hive táblákon. Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. A HDInsight Apache Spark keretrendszere lehetővé teszi a gyors adatelemzést és a fürtözést a memóriabeli feldolgozás használatával. A Jupyter notebook lehetővé teszi az adatkezelést, a kód és a Markdown szöveg egyesítését, valamint egyszerű vizualizációk készítését.

[Áttekintés: Apache Spark az Azure HDInsight](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [Jupyter notebook](https://jupyter.org/)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Apache Spark-fürt létrehozása a HDInsight-ben

A Azure Portal használatával olyan HDInsight-fürtöt hozhat létre, amely Azure Storage-blobokat használ fürtként. A 2. generációs Data Lake Storage használatáról [a fürtök HDInsightban történő beállításáról szóló rövid útmutatóban](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) talál további információt.

> [!IMPORTANT]  
> A HDInsight-fürtök számlázása percenként történik, akár használja őket, akár nem. Mindig törölje a fürtöt, ha már nem használja. További információkért lásd a cikk [Az erőforrások eltávolítása](#clean-up-resources) című szakaszát.

1. Az Azure Portalon válassza az **Erőforrás létrehozása** lehetőséget.

    ![Erőforrás létrehozása Azure Portal](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create.png "Erőforrás létrehozása az Azure Portalon")

1. Az **új** lapon válassza az **elemzési** > **HDInsight**elemet.

    ![Azure Portal HDInsight létrehozása](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-hdinsight-spark-cluster.png "HDInsight Azure Portal")

1. Az **Alapvető beállítások** területen adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Előfizetést  | Válassza ki a legördülő menüből a fürthöz használt Azure-előfizetést. Az ehhez a rövid útmutatóhoz használt előfizetés az **Azure**. |
    |Erőforráscsoport | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az ehhez a rövid útmutatóhoz használt erőforráscsoport-név **myResourceGroup**. |
    |Fürt neve | Adjon egy nevet a HDInsight-fürtnek. Az ehhez a rövid útmutatóhoz használt fürt neve **myspark2019**.|
    |Hely   | Válasszon egy helyet az erőforráscsoportnak. A sablon ezt a helyet használja a fürt létrehozásához, valamint az alapértelmezett fürttárolóhoz. A rövid útmutatóhoz használt hely az **USA keleti**régiója. |
    |Fürt típusa| Válassza a **Spark** lehetőséget a fürt típusaként.|
    |Fürt verziója|Ez a mező automatikusan feltölti az alapértelmezett verziót a fürt típusának kiválasztása után.|
    |Fürt bejelentkezési felhasználóneve| Adja meg a fürt bejelentkezési felhasználónevét.  Az alapértelmezett név a *rendszergazda*. Ezzel a fiókkal a gyors útmutatóban később bejelentkezhet a Jupyter-jegyzetfüzetbe. |
    |Fürt bejelentkezési jelszava| Adja meg a fürt bejelentkezési jelszavát. |
    |Secure Shell- (SSH-) felhasználónév| Adja meg az SSH-felhasználónevet. A rövid útmutatóhoz használt SSH-felhasználónév: **sshuser**. Alapértelmezés szerint ennek a fióknak ugyanaz a jelszava, mint a *fürt bejelentkezési felhasználónevéhez* tartozó fióknak. |

    ![HDInsight-fürt alapkonfigurációinak létrehozása](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Spark-fürt létrehozása a HDInsight-ben az alapvető konfigurációk")

    Válassza a **Next (tovább): storage > >** lehetőséget a **tárterület** oldalának folytatásához.

1. A **Storage** területen adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Elsődleges tároló típusa|Használja az alapértelmezett értéket az **Azure Storage**-ban.|
    |Kiválasztási módszer|Használja az alapértelmezett értéket a **listából**.|
    |Az elsődleges tárfiók|Használja az automatikusan feltöltött értéket.|
    |Tároló|Használja az automatikusan feltöltött értéket.|

    ![HDInsight-fürt alapkonfigurációinak létrehozása](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png "Spark-fürt létrehozása a HDInsight-ben az alapvető konfigurációk")

    A folytatáshoz válassza a **felülvizsgálat + létrehozás** lehetőséget.

1. A **felülvizsgálat + létrehozás**területen válassza a **Létrehozás**lehetőséget. A fürt létrehozása nagyjából 20 percet vesz igénybe. A következő munkamenetre csak a fürt létrehozását követően lehet továbblépni.

Ha a HDInsight-fürtök létrehozásával kapcsolatos problémába ütközne, előfordulhat, hogy nem rendelkezik a megfelelő engedélyekkel. További információért tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

A Jupyter notebook egy interaktív notebook-környezet, amely számos programozási nyelvet támogat. A notebook lehetővé teszi az adatai használatát, a kódok és markdown-szövegek egyesítését, valamint egyszerű vizualizációk elvégzését.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

1. Válassza a **HDInsight-fürtök** lehetőséget, majd a létrehozott fürtöt.

    ![HDInsight-fürt megnyitása az Azure Portalon](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

1. A portálon válassza a **Fürt irányítópultja** lehetőséget, majd a **Jupyter Notebook** elemet. Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

   ![Az interaktív Spark SQL-lekérdezés futtatásához Jupyter Notebook megnyitása](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Az interaktív Spark SQL-lekérdezés futtatásához Jupyter Notebook megnyitása")

1. Új notebook létrehozásához válassza a **New** > **PySpark** (Új > PySpark) lehetőséget.

   ![Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához")

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL-utasítások futtatása

Az SQL az adatok lekérdezéséhez és meghatározásához leggyakrabban és legszélesebb körben használt nyelv. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL-szintaxissal való feldolgozásához.

1. Ellenőrizze, hogy a kernel készen áll-e. A kernel akkor áll készen, ha a neve mellett a notebookban egy üres kör látható. A teli kör azt jelenti, hogy a kernel foglalt.

    ![Apache Hive lekérdezés a HDInsight](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Struktúra-lekérdezés a HDInsight")

    A notebook első indításakor a kernel a háttérben elvégez néhány feladatot. Várja meg, hogy a kernel elkészüljön.

1. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak futtatásához. A parancs felsorolja a fürtön található Hive-táblákat:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Ha a HDInsight-fürttel Jupyter Notebook használ, egy előre definiált `sqlContext` kap, amellyel a Spark SQL használatával futtathatja a kaptár-lekérdezéseket. A `%%sql` megadja a Jupyter notebook számára, hogy az előre beállított `sqlContext` elemet használja a Hive-lekérdezés futtatásához. A lekérdezés lekérdezi az első 10 sort egy Hive-táblából (**hivesampletable**), amely alapértelmezés szerint minden HDInsight-fürtben megtalálható. Az eredmények lekérdezése körülbelül 30 másodpercet vesz igénybe. A kimenet a következőképpen fog kinézni:

    ![Apache Hive lekérdezés a HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Struktúra-lekérdezés a HDInsight")

    Minden alkalommal, amikor a Jupyterben lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik.

1. Futtasson egy másik lekérdezést a `hivesampletable` adatainak megtekintéséhez.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A képernyő frissül, és megjeleníti a lekérdezés kimenetét.

    ![Struktúra-lekérdezés kimenete a HDInsight-ben](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Struktúra-lekérdezés kimenete a HDInsight-ben")

1. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. A notebook leállítása felszabadítja a fürt erőforrásait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A HDInsight az Azure Storage vagy az Azure Data Lake Storage tárhelyein tárolja az adatokat, így a nem használt fürtök biztonságosan törölhetők. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. Ha azt tervezi, hogy rögtön elvégzi a [További lépések](#next-steps) szakaszban található oktatóanyagot is, akkor érdemes lehet megtartani a fürtöt.

Lépjen vissza az Azure Portalra és válassza a **Törlés** lehetőséget.

![HDInsight-fürt törlése Azure Portal](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésével törli a HDInsight-fürtöt és az alapértelmezett Storage-fiókot is.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Apache Spark-fürtöt a HDInsight-ben, és hogyan futtathat egy alapszintű Spark SQL-lekérdezést. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan futtathat HDInsight-fürtöket interaktív lekérdezések futtatásához a mintaadatok használatával.

> [!div class="nextstepaction"]
> [Interaktív lekérdezések futtatása Apache Sparkon](./apache-spark-load-data-run-query.md)

---
title: 'Rövid útmutató: Spark-fürt létrehozása a HDInsightban az Azure Portalon'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Apache Spark-fürtöt az Azure HDInsightban az Azure Spark-fürt, és hogyan futtathat egy Spark SQL-lekérdezést.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2020
ms.openlocfilehash: 5f4d1b8ef742a8dcafa2b8e34a6209f85ae050d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77650628"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-portal"></a>Rövid útmutató: Apache Spark-fürt létrehozása az Azure HDInsightban az Azure Portalon

Ebben a rövid útmutatóban az Azure Portalon hozzon létre egy Apache Spark-fürtöt az Azure HDInsightban. Ezután hozzon létre egy Jupyter-jegyzetfüzetet, és ezzel futtathatja a Spark SQL-lekérdezéseket az Apache Hive-táblákon. Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. Az Apache Spark HDInsight-keretrendszere gyors adatelemzést és fürtös számítástechnikát tesz lehetővé a memórián belüli feldolgozással. A Jupyter notebook lehetővé teszi az adatok kalkulálásával, a kód és a markdown szöveg kombinálásával és az egyszerű vizualizációk kombinálásával.

Az elérhető konfigurációk részletes magyarázata a [Fürtök beállítása a HDInsightban](../hdinsight-hadoop-provision-linux-clusters.md). A portál fürtök létrehozásához való használatáról a [Fürtök létrehozása a portálon](../hdinsight-hadoop-create-linux-clusters-portal.md)című témakörben talál további információt.

> [!IMPORTANT]  
> A HDInsight-fürtök számlázása percenként történik, akár használja őket, akár nem. Mindig törölje a fürtöt, ha már nem használja. További információkért lásd a cikk [Az erőforrások eltávolítása](#clean-up-resources) című szakaszát.

## <a name="prerequisites"></a>Előfeltételek

Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-apache-spark-cluster-in-hdinsight"></a>Apache Spark-fürt létrehozása a HDInsightban

Az Azure Portal segítségével hozzon létre egy HDInsight-fürtöt, amely az Azure Storage Blobs-ot használja fürttárolóként. A 2. generációs Data Lake Storage használatáról [a fürtök HDInsightban történő beállításáról szóló rövid útmutatóban](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) talál további információt.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. A felső menüben válassza a **+ Erőforrás létrehozása**lehetőséget.

    ![Az Azure Portal erőforrást hoz létre](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-create-resource.png "Erőforrás létrehozása az Azure Portalon")

1. Válassza az **Analytics** > **Azure HDInsight** lehetőséget a **HDInsight-fürt létrehozása** lap megugrásához.

1. Az **Alapok** lapon adja meg a következő információkat:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Előfizetés  | A legördülő listából válassza ki a fürthöz használt Azure-előfizetést. |
    |Erőforráscsoport | A legördülő listában jelölje ki a meglévő erőforráscsoportot, vagy válassza **az Új létrehozása lehetőséget.**|
    |Fürt neve | Adjon meg egy globálisan egyedi nevet.|
    |Régió   | A legördülő listából válassza ki azt a régiót, ahol a fürt létrejön. |
    |Fürt típusa| Lista megnyitásához válassza a Fürttípus kiválasztása lehetőséget. A listában válassza a **Spark**lehetőséget.|
    |Fürt verziója|A fürttípus kiválasztása után ez a mező automatikusan feltölti az alapértelmezett verziót.|
    |Fürt bejelentkezési felhasználóneve| Adja meg a fürt bejelentkezési felhasználónevét.  Az alapértelmezett név **admin**. Ezzel a fiókkal a rövid útmutató későbbi részében jelentkezhet be a Jupyter-jegyzetfüzetbe. |
    |Fürt bejelentkezési jelszava| Adja meg a fürt bejelentkezési jelszavát. |
    |Secure Shell- (SSH-) felhasználónév| Adja meg az SSH-felhasználónevet. A rövid útmutatóhoz használt SSH-felhasználónév: **sshuser**. Alapértelmezés szerint ennek a fióknak ugyanaz a jelszava, mint a *fürt bejelentkezési felhasználónevéhez* tartozó fióknak. |

    ![HdInsight-fürt alapvető konfigurációinak létrehozása](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-basics-spark.png "Spark-fürt létrehozása a HDInsightban az alapkonfigurációk")

    Válassza a **Tovább: Storage >>** lehetőséget a Storage lapra való **továbbkereséshez.**

1. A **Storage** területen adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Elsődleges tároló típusa|Használja az alapértelmezett **Azure Storage**értéket.|
    |Kiválasztási módszer|Használja az alapértelmezett : **Válassza ki a listából lehetőséget.**|
    |Az elsődleges tárfiók|Használja az automatikusan kitöltött értéket.|
    |Tároló|Használja az automatikusan kitöltött értéket.|

    ![HdInsight-fürt alapvető konfigurációinak létrehozása](./media/apache-spark-jupyter-spark-sql-use-portal/azure-portal-cluster-storage.png "Spark-fürt létrehozása a HDInsightban az alapkonfigurációk")

    A folytatáshoz válassza **a Véleményezés + létrehozás** lehetőséget.

1. A **Véleményezés + létrehozás**csoportban válassza a **Létrehozás lehetőséget.** A fürt létrehozása nagyjából 20 percet vesz igénybe. A következő munkamenetre csak a fürt létrehozását követően lehet továbblépni.

Ha a HDInsight-fürtök létrehozásával kapcsolatos probléma lép fel, előfordulhat, hogy ehhez nem rendelkezik a megfelelő engedélyekkel. További információért tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

A Jupyter Notebook egy interaktív notebook-környezet, amely számos programozási nyelvet támogat. A notebook lehetővé teszi az adatai használatát, a kódok és markdown-szövegek egyesítését, valamint egyszerű vizualizációk elvégzését.

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net/jupyter`meg `CLUSTERNAME` a , ahol a fürt neve. Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

1. Válassza **az Új** > **PySpark** lehetőséget a jegyzetfüzet létrehozásához.

   ![Jupyter-jegyzetfüzet létrehozása interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter-jegyzetfüzet létrehozása interaktív Spark SQL-lekérdezés futtatásához")

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL-utasítások futtatása

Az SQL az adatok lekérdezéséhez és meghatározásához leggyakrabban és legszélesebb körben használt nyelv. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL-szintaxissal való feldolgozásához.

1. Ellenőrizze, hogy a kernel készen áll-e. A kernel akkor áll készen, ha a neve mellett a notebookban egy üres kör látható. A teli kör azt jelenti, hogy a kernel foglalt.

    ![Apache Hive-lekérdezés a HDInsightban](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Hive-lekérdezés a HDInsightban")

    A notebook első indításakor a kernel a háttérben elvégez néhány feladatot. Várja meg, hogy a kernel elkészüljön.

1. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak futtatásához. A parancs felsorolja a fürtön található Hive-táblákat:

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Ha egy Jupyter-jegyzetfüzetet használ a HDInsight-fürthöz, egy készletet `sqlContext` kap, amely segítségével futtathatja a Hive-lekérdezéseket a Spark SQL használatával. A `%%sql` megadja a Jupyter notebook számára, hogy az előre beállított `sqlContext` elemet használja a Hive-lekérdezés futtatásához. A lekérdezés lekérdezi az első 10 sort egy Hive-táblából (**hivesampletable**), amely alapértelmezés szerint minden HDInsight-fürtben megtalálható. Az eredmények lekérdezése körülbelül 30 másodpercet vesz igénybe. A kimenet a következőképpen fog kinézni:

    ![Apache Hive-lekérdezés a HDInsightban](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query.png "Hive-lekérdezés a HDInsightban")

    Minden alkalommal, amikor a Jupyterben lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik.

1. Futtasson egy másik lekérdezést a `hivesampletable` adatainak megtekintéséhez.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A képernyő frissül, és megjeleníti a lekérdezés kimenetét.

    ![Hive-lekérdezés kimenete a HDInsightban](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-spark-get-started-hive-query-output.png "Hive-lekérdezés kimenete a HDInsightban")

1. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. A notebook leállítása felszabadítja a fürt erőforrásait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A HDInsight menti az adatokat az Azure Storage vagy az Azure Data Lake Storage szolgáltatásban, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat kell fizetnie, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban. Ha azt tervezi, hogy rögtön elvégzi a [További lépések](#next-steps) szakaszban található oktatóanyagot is, akkor érdemes lehet megtartani a fürtöt.

Lépjen vissza az Azure Portalra és válassza a **Törlés** lehetőséget.

![Az Azure Portal hdinsight-fürt törlése](./media/apache-spark-jupyter-spark-sql-use-portal/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésével törli a HDInsight-fürtöt és az alapértelmezett tárfiókot is.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Apache Spark-fürtöt a HDInsightban, és hogyan futtathat egy egyszerű Spark SQL-lekérdezést. A következő oktatóanyagra lépve megtudhatja, hogyan használhatja a HDInsight-fürtöt a mintaadatokon futó interaktív lekérdezések futtatásához.

> [!div class="nextstepaction"]
> [Interaktív lekérdezések futtatása az Apache Sparkon](./apache-spark-load-data-run-query.md)

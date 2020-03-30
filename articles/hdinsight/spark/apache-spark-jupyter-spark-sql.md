---
title: 'Rövid útmutató: Apache Spark-fürt létrehozása sablonnal – Azure HDInsight'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Apache Spark-fürtöt az Azure HDInsightban, és hogyan hozhat létre Egy Erőforrás-kezelő sablont az Azure HDInsightban, és hogyan futtathat egy Spark SQL-lekérdezést.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 6d590659a4ed73fa27193961721d949b555c3444
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064541"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Rövid útmutató: Apache Spark-fürt létrehozása az Azure HDInsightban az Erőforrás-kezelő sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager-sablon használatával hozzon létre egy [Apache Spark-fürtöt](./apache-spark-overview.md) az Azure HDInsightban. Ezután hozzon létre egy Jupyter-jegyzetfüzetet, és ezzel futtathatja a Spark SQL-lekérdezéseket az Apache Hive-táblákon. Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. Az Apache Spark HDInsight-keretrendszere gyors adatelemzést és fürtös számítástechnikát tesz lehetővé a memórián belüli feldolgozással. A Jupyter notebook lehetővé teszi az adatok kalkulálásával, a kód és a markdown szöveg kombinálásával és az egyszerű vizualizációk kombinálásával.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-an-apache-spark-cluster"></a>Apache Spark-fürt létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-spark-linux)

:::code language="json" source="~/quickstart-templates/101-hdinsight-spark-linux/azuredeploy.json" range="1-143":::

A sablonban két Azure-erőforrás van definiálva:

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): hozzon létre egy Azure Storage-fiókot.
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): HDInsight-fürt létrehozása.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Válassza ki az **Azure üzembe helyezése** gombot az Azure-ba való bejelentkezéshez és az Erőforrás-kezelő sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json)

1. Adja meg vagy válassza ki a következő értékeket:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|A legördülő listából válassza ki a fürthöz használt Azure-előfizetést.|
    |Erőforráscsoport|A legördülő listában jelölje ki a meglévő erőforráscsoportot, vagy válassza **az Új létrehozása lehetőséget.**|
    |Hely|Az érték automatikusan kinépesül az erőforráscsoporthoz használt helynel.|
    |Fürt neve|Adjon meg egy globálisan egyedi nevet. Ehhez a sablonhoz csak kisbetűket és számokat használjon.|
    |Fürt bejelentkezési felhasználóneve|Adja meg a felhasználónevet, az alapértelmezett **admin**.|
    |Fürt bejelentkezési jelszava|Adja meg a jelszót. A jelszónak legalább 10 karakter hosszúnak kell lennie, és legalább egy számjegyet, egy nagybetűt és egy kisbetűt, egy nem alfanumerikus karaktert (kivéve a " ' karaktereket ). |
    |Ssh felhasználónév|Adja meg a felhasználónevet, az alapértelmezett **sshuser**|
    |Ssh jelszó|Adja meg a jelszót.|

    ![Spark-fürt létrehozása a HDInsightban az Azure Resource Manager-sablonhasználatával](./media/apache-spark-jupyter-spark-sql/resource-manager-template-spark.png "Spark-fürt létrehozása a HDInsightban egy Azure Resource Manager-sablon használatával")

1. Tekintse át a **feltételeket**. Ezután válassza **az Elfogadom a fent meghatározott feltételeket**, majd **a Vásárlás**lehetőséget. Értesítést fog kapni arról, hogy a központi telepítés folyamatban van. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

Ha a HDInsight-fürtök létrehozásával kapcsolatos probléma lép fel, előfordulhat, hogy ehhez nem rendelkezik a megfelelő engedélyekkel. További információért tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A fürt létrehozása után kap egy **telepítési sikeres** értesítést egy Ugrás az **erőforrásra** hivatkozással. Az erőforráscsoport lapja felsorolja az új HDInsight-fürtöt és a fürthöz társított alapértelmezett tárolót. Minden fürt rendelkezik egy [Azure Storage-fiókkal](../hdinsight-hadoop-use-blob-storage.md) vagy egy [Azure Data Lake Storage-fiók függőség.](../hdinsight-hadoop-use-data-lake-store.md) Ez a továbbiakban az alapértelmezett tárfiók. A HDInsight-fürtés alapértelmezett tárfiókja együtt kell lennie ugyanabban az Azure-régióban. Fürtök törlése nem törli a tárfiókot.

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

[A Jupyter Notebook](https://jupyter.org/) egy interaktív jegyzetfüzet-környezet, amely támogatja a különböző programozási nyelveket. A jegyzetfüzet lehetővé teszi az adatok kalkulálható, a kód és a markdown szöveg kombinálását, valamint az egyszerű vizualizációk elvégzését.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

2. Válassza a **HDInsight-fürtök** lehetőséget, majd a létrehozott fürtöt.

    ![HDInsight-fürt megnyitása az Azure Portalon](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. A portálon a **Fürtirányítópultok** csoportban válassza a **Jupyter-jegyzetfüzet lehetőséget.** Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

   ![A Jupyter-jegyzetfüzet megnyitása interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "A Jupyter-jegyzetfüzet megnyitása interaktív Spark SQL-lekérdezés futtatásához")

4. Válassza **az Új** > **PySpark** lehetőséget a jegyzetfüzet létrehozásához.

   ![Jupyter-jegyzetfüzet létrehozása interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter-jegyzetfüzet létrehozása interaktív Spark SQL-lekérdezés futtatásához")

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

## <a name="run-apache-spark-sql-statements"></a>Apache Spark SQL-utasítások futtatása

Az SQL az adatok lekérdezésére és átalakítására leggyakrabban és legszélesebb körben használt nyelv. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL-szintaxissal való feldolgozásához.

1. Ellenőrizze, hogy a kernel készen áll-e. A kernel akkor áll készen, ha a neve mellett a notebookban egy üres kör látható. A teli kör azt jelenti, hogy a kernel foglalt.

    ![Kernel állapota](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Kernel állapota")

    A notebook első indításakor a kernel a háttérben elvégez néhány feladatot. Várja meg, hogy a kernel elkészüljön.

1. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak futtatásához. A parancs felsorolja a fürtön található Hive-táblákat:

    ```sql
    %%sql
    SHOW TABLES
    ```

    Ha egy Jupyter-jegyzetfüzetet használ a HDInsight-fürttel, egy előre beállított munkamenetet `spark` kap, amely segítségével futtathatja a Hive-lekérdezéseket a Spark SQL használatával. A `%%sql` megadja a Jupyter-notebook számára, hogy az előre beállított `spark`-munkamenetet használja a Hive-lekérdezés futtatásához. A lekérdezés lekérdezi az első 10 sort egy Hive-táblából (**hivesampletable**), amely alapértelmezés szerint minden HDInsight-fürtben megtalálható. A lekérdezés első elküldésekor a Jupyter spark-alkalmazást hoz létre a jegyzetfüzethez. Ez körülbelül 30 másodpercet vesz igénybe. Miután a Spark-alkalmazás készen áll, a lekérdezés végrehajtása körülbelül egy másodperc, és az eredményeket. A kimenet a következőképpen fog kinézni:

    ![Apache Hive-lekérdezés a HDInsightban](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Hive-lekérdezés a HDInsightban")

    Minden alkalommal, amikor a Jupyterben lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik.

1. Futtasson egy másik lekérdezést a `hivesampletable` adatainak megtekintéséhez.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A képernyő frissül, és megjeleníti a lekérdezés kimenetét.

    ![Hive-lekérdezés kimenete a HDInsightban](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Hive-lekérdezés kimenete a HDInsightban")

1. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. A notebook leállítása felszabadítja a fürt erőforrásait, beleértve a Spark-alkalmazást is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat kell fizetnie, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

Az Azure Portalon keresse meg a fürtöt, és válassza a **Törlés**lehetőséget.

![Az Azure Portal hdinsight-fürt törlése](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésével törli a HDInsight-fürtöt és az alapértelmezett tárfiókot is.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Apache Spark-fürtöt a HDInsightban, és hogyan futtathat egy egyszerű Spark SQL-lekérdezést. A következő oktatóanyagra lépve megtudhatja, hogyan használhatja a HDInsight-fürtöt a mintaadatokon futó interaktív lekérdezések futtatásához.

> [!div class="nextstepaction"]
> [Interaktív lekérdezések futtatása az Apache Sparkon](./apache-spark-load-data-run-query.md)

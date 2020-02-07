---
title: 'Rövid útmutató: Apache Spark-fürt létrehozása sablon használatával – Azure HDInsight'
description: Ez a rövid útmutató bemutatja, hogyan használható egy Resource Manager-sablon egy Azure Spark-fürt létrehozásához az Azure HDInsightban, illetve egy egyszerű Spark SQL-lekérdezés futtatásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/12/2019
ms.custom: mvc
ms.openlocfilehash: 2637603fa303d57340aa36786443508f1930a481
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049898"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Rövid útmutató: Apache Spark-fürt létrehozása az Azure HDInsight Resource Manager-sablon használatával

Ebben a rövid útmutatóban egy Azure Resource Manager sablonnal hoz létre egy Apache Spark-fürtöt az Azure HDInsight-ben. Ezután létrehozhat egy Jupyter-jegyzetfüzetet, és használhatja a Spark SQL-lekérdezések futtatásához Apache Hive táblákon. Az Azure HDInsight egy felügyelt, teljes körű, nyílt forráskódú elemzési szolgáltatás vállalatok részére. A HDInsight Apache Spark keretrendszere lehetővé teszi a gyors adatelemzést és a fürtözést a memóriabeli feldolgozás használatával. A Jupyter notebook lehetővé teszi az adatkezelést, a kód és a Markdown szöveg egyesítését, valamint egyszerű vizualizációk készítését.

[Áttekintés: Apache Spark az Azure HDInsight](apache-spark-overview.md) | [Apache Spark](https://spark.apache.org/) | [Apache Hive](https://hive.apache.org/) | [Jupyter notebook](https://jupyter.org/) | [Azure Gyorsindítás sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-an-apache-spark-cluster"></a>Apache Spark-fürt létrehozása

Hozzon létre egy Apache Spark-fürtöt a HDInsight egy Azure Resource Manager sablon használatával. A sablon a [githubon](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/)található. A fürt JSON-szintaxisához és tulajdonságaihoz lásd: [Microsoft. HDInsight/fürtök](/azure/templates/microsoft.hdinsight/clusters).

A fürt Azure Storage-blobokat használ fürttárolóként. A 2. generációs Data Lake Storage használatáról [a fürtök HDInsightban történő beállításáról szóló rövid útmutatóban](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) talál további információt.

> [!IMPORTANT]  
> A HDInsight-fürtök számlázása percenként történik, akár használja őket, akár nem. Mindig törölje a fürtöt, ha már nem használja. További információkért lásd a cikk [Az erőforrások eltávolítása](#clean-up-resources) című szakaszát.

1. Az alábbi hivatkozásra kattintva megnyithatja a sablont az Azure Portalon egy új böngészőlapon:

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank">Üzembe helyezés az Azure-ban</a>

2. Írja be a következő értékeket:

    | Tulajdonság | Érték |
    |---|---|
    |**Előfizetés**|Válassza ki a fürt létrehozásához használt Azure-előfizetést. A rövid útmutatóhoz használt előfizetés: **&lt;Azure-előfizetés neve>** . |
    | **Erőforráscsoport**|Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévőt. Az erőforráscsoport kezeli a projektek Azure-erőforrásait. A rövid útmutatóhoz használt új erőforráscsoport neve: **myspark20180403rg**.|
    | **Hely**|Válasszon egy helyet az erőforráscsoportnak. A sablon ezt a helyet használja a fürt létrehozásához, valamint az alapértelmezett fürttárolóhoz. A rövid útmutatóhoz használt hely az **USA 2. keleti régiója**.|
    | **ClusterName**|Adja meg a létrehozni kívánt fürt nevét. A rövid útmutatóhoz használt új fürt neve: **myspark20180403**.|
    | **A fürt bejelentkezési neve és jelszava**|Az alapértelmezett bejelentkezési név a rendszergazda. Válassza ki a fürt bejelentkezési jelszavának jelszavát. A rövid útmutatóhoz használt bejelentkezési név: **admin**.|
    | **SSH-felhasználónév és -jelszó**|Válasszon egy jelszót az SSH-felhasználónak. A rövid útmutatóhoz használt SSH-felhasználónév: **sshuser**.|

    ![Spark-fürt létrehozása a HDInsight-ben Azure Resource Manager sablon használatával](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Spark-fürt létrehozása a HDInsight-ben Azure Resource Manager sablon használatával")

3. Jelölje be az **Elfogadom a fenti feltételeket** és a **Rögzítés az irányítópulton** lehetőséget, majd válassza a **Vásárlás** lehetőséget. Egy új csempe jelenik meg **Deploying Template deployment** (Üzembe helyezés – Sablon telepítése) címmel. A fürt létrehozása nagyjából 20 percet vesz igénybe. A következő munkamenetre csak a fürt létrehozását követően lehet továbblépni.

Ha a HDInsight-fürtök létrehozásával kapcsolatos problémába ütközne, előfordulhat, hogy nem rendelkezik a megfelelő engedélyekkel. További információért tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="install-intellijeclipse-for-spark-applications"></a>A IntelliJ/Eclipse telepítése Spark-alkalmazásokhoz

A Azure Toolkit for IntelliJ/Eclipse beépülő modullal fejlesztheti a [Scala](https://www.scala-lang.org/)-ben írt Spark-alkalmazásokat, majd beküldheti őket egy Azure HDInsight-fürtbe közvetlenül az IntelliJ/Eclipse integrált fejlesztői környezetből (ide). További információ: [IntelliJ használata Spark-alkalmazások létrehozásához/leadásához](./apache-spark-intellij-tool-plugin.md) és [Eclipse használata Spark-alkalmazások létrehozásához/leadásához](./apache-spark-eclipse-tool-plugin.md).

## <a name="install-vscode-for-pysparkhive-applications"></a>A VSCode telepítése a PySpark/kaptár alkalmazások számára

Megtudhatja, hogyan használhat Azure HDInsight-eszközöket a Visual Studio Code (VSCode) alkalmazáshoz, hogy Hive batch-feladatokat, interaktív Hive-lekérdezéseket, PySpark-köteget és PySpark interaktív szkripteket hozzon létre és adjon le. Az Azure HDInsight-eszközök a VSCode által támogatott platformokon telepíthetők. Ezek közé tartozik a Windows, a Linux és a macOS. További információ: [VSCode használata PySpark-alkalmazások létrehozásához/leadásához](../hdinsight-for-vscode.md).

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

A [Jupyter notebook](https://jupyter.org/) egy interaktív notebook-környezet, amely különböző programozási nyelveket támogat. A notebook lehetővé teszi az adatokkal való interakciót, a kódok Markdown való egyesítését, valamint az egyszerű vizualizációk elvégzését.

1. Nyissa meg az [Azure Portal](https://portal.azure.com).

2. Válassza a **HDInsight-fürtök** lehetőséget, majd a létrehozott fürtöt.

    ![HDInsight-fürt megnyitása a Azure Portal](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. A portálon, a **fürt irányítópultok** szakaszban válassza a **Jupyter notebook**lehetőséget. Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

   ![Az interaktív Spark SQL-lekérdezés futtatásához Jupyter Notebook megnyitása](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Az interaktív Spark SQL-lekérdezés futtatásához Jupyter Notebook megnyitása")

4. Új notebook létrehozásához válassza a **New** > **PySpark** (Új > PySpark) lehetőséget.

   ![Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter Notebook létrehozása az interaktív Spark SQL-lekérdezés futtatásához")

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

    Ha a HDInsight-fürttel Jupyter Notebook használ, egy előre beállított `spark`-munkamenetet kap, amellyel a Spark SQL használatával futtathat kaptár-lekérdezéseket. A `%%sql` megadja a Jupyter-notebook számára, hogy az előre beállított `spark`-munkamenetet használja a Hive-lekérdezés futtatásához. A lekérdezés lekérdezi az első 10 sort egy Hive-táblából (**hivesampletable**), amely alapértelmezés szerint minden HDInsight-fürtben megtalálható. Amikor először küldi el a lekérdezést, a Jupyter létrehoz egy Spark-alkalmazást a jegyzetfüzethez. Ez körülbelül 30 másodpercet vesz igénybe. Miután a Spark-alkalmazás elkészült, a lekérdezés végrehajtása körülbelül egy másodperc alatt történik, és létrehozza az eredményeket. A kimenet a következőképpen fog kinézni:

    ![Apache Hive lekérdezés a HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Struktúra-lekérdezés a HDInsight")

    Minden alkalommal, amikor a Jupyterben lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik.

1. Futtasson egy másik lekérdezést a `hivesampletable` adatainak megtekintéséhez.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```

    A képernyő frissül, és megjeleníti a lekérdezés kimenetét.

    ![Struktúra-lekérdezés kimenete a HDInsight-ben](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Struktúra-lekérdezés kimenete a HDInsight-ben")

1. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. A jegyzetfüzet leállítása a fürt erőforrásainak kiadását, beleértve a Spark-alkalmazást is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A HDInsight az Azure Storage vagy az Azure Data Lake Store tárhelyein tárolja az adatokat és a Jupyter-notebookokat, így a nem használt fürtök biztonságosan törölhetők. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. Ha azt tervezi, hogy rögtön elvégzi a [További lépések](#next-steps) szakaszban található oktatóanyagot is, akkor érdemes lehet megtartani a fürtöt.

Lépjen vissza az Azure Portalra és válassza a **Törlés** lehetőséget.

![HDInsight-fürt törlése Azure Portal](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésével törli a HDInsight-fürtöt és az alapértelmezett Storage-fiókot is.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Apache Spark-fürtöt a HDInsight-ben, és hogyan futtathat egy alapszintű Spark SQL-lekérdezést. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan futtathat HDInsight-fürtöket interaktív lekérdezések futtatásához a mintaadatok használatával.

> [!div class="nextstepaction"]
>[Interaktív lekérdezések futtatása Apache Sparkon](./apache-spark-load-data-run-query.md)

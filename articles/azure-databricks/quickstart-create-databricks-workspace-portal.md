---
title: 'Gyors útmutató: Spark-feladatok futtatása Azure databricksen az Azure portal használatával'
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure Portal egy Azure Databricks-munkaterület és egy Apache Spark-fürt létrehozásához, illetve Spark-feladatok futtatásához.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 43133810c6f8b7cb9fdacb2503103e09f345acfc
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551195"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Gyors útmutató: Spark-feladatok futtatása Azure databricksen az Azure portal használatával

Ez a rövid útmutató bemutatja, hogyan hozható létre Azure Databricks-munkaterület, illetve azon belül egy Apache Spark-fürt. Végezetül azt is ismertetjük, hogyan futtathat Spark-feladatokat a Databricks-fürtön. További információt az Azure Databricksről [az Azure Databrickset](what-is-azure-databricks.md) ismertető cikkben talál.

Ebben a rövid útmutatóban a Spark-feladat részeként, Boston biztonsági adatok elemzésével betekintést jelentéskészítési módjait.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban egy Azure Databricks-munkaterületet fog létrehozni az Azure Portal használatával.

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Elemzés** > **Azure Databricks** elemet.

    ![Databricks az Azure Portalon](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks az Azure Portalon")

2. Az **Azure Databricks szolgáltatás** pontban adja meg az értékeket Databricks-munkaterület létrehozásához.

    ![Azure Databricks-munkaterület létrehozása](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Azure Databricks-munkaterület létrehozása")

    Adja meg a következő értékeket:
    
    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../azure-resource-manager/resource-group-overview.md). |
    |**Hely**     | Válassza az **USA 2. nyugati régióját**. A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).        |
    |**Tarifacsomag**     |  Választhat **Standard**, **prémium**, vagy **próbaverzió**. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

    Válassza a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Létrehozás** gombra.

4. A munkaterület létrehozása eltarthat néhány percig. Munkaterület létrehozása során, a telepítés állapotát megtekintheti **értesítések**.

    ![Databricks üzembe helyezési csempe](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Databricks üzembe helyezési csempe")

## <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

> [!NOTE]
> Ha egy ingyenes fiókot használna az Azure Databricks-fürt létrehozásához, a fürt létrehozása előtt nyissa meg a saját profilját, és módosítsa az előfizetését **használatalapú fizetésre**. További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket.

1. Az Azure Portalon lépjen a korábban létrehozott Databricks-munkaterülethez, majd kattintson a **Munkaterület indítása** elemre.

2. A rendszer átirányítja az Azure Databricks portáljára. A portálon kattintson **új fürt**.

    ![Databricks az Azure-on](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks az Azure-on")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-on](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-on")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

   * Adjon egy nevet a fürtnek.
   * Ebben a cikkben hozzon létre egy fürtöt **5.2** modul.
   * Mindenképpen jelölje be a **Leállítás \_\_ percnyi tétlenség után** jelölőnégyzetet. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.
    
     Válassza a **Fürt létrehozása** lehetőséget. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

További információt a fürtök létrehozásáról a [Spark-fürtök az Azure Databricks használatával történő létrehozását](https://docs.azuredatabricks.net/user-guide/clusters/create.html) ismertető szakaszban talál.

## <a name="run-a-spark-sql-job"></a>Spark SQL-feladat futtatása

Az alábbi lépésekkel hozzon létre egy notebookot a Databricksben, konfigurálja a notebookot adatokat olvasni az Azure Open adatkészleteket, és ezt követően futtassa a Spark SQL-feladatot az adatokon.

1. A bal oldali panelen válassza ki a **Azure Databricks**. Az a **gyakori feladatok**válassza **új Notebook**.

    ![Notebook létrehozása a Databricksben](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Notebook létrehozása a Databricksben")

2. Az a **jegyzetfüzet létrehozása** párbeszédpanel mezőben adjon meg egy nevet, válassza ki **Python** , a nyelvet, és válassza a korábban létrehozott Spark-fürtön.

    ![Notebook létrehozása a Databricksben](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Notebook létrehozása a Databricksben")

    Kattintson a **Létrehozás** gombra.

3. Ebben a lépésben hozzon létre egy Spark DataFrame Boston biztonsági adatait [nyissa meg az Azure-adatkészletek](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks), és az adatok lekérdezése SQL használatával.

   A következő parancs beállítja a az Azure storage-hozzáférési adatokat. Illessze be a PySpark-kód az első olyan cellára, majd használja **Shift + Enter** a kód futtatásához.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   A következő parancs lehetővé teszi, hogy az Apache Spark a Blob storage-ból távolról olvasni. A PySpark-kód illessze be a következő cella, és használjon **Shift + Enter** a kód futtatásához.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   A következő parancs létrehoz egy adathalmaz. A PySpark-kód illessze be a következő cella, és használjon **Shift + Enter** a kód futtatásához.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Az első 10 sort az adatok visszatérési SQL-utasítás futtatásával nevű ideiglenes nézetből **forrás**. A PySpark-kód illessze be a következő cella, és használjon **Shift + Enter** a kód futtatásához.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Így egy, az alábbi képernyőképhez hasonló táblázatos kimenet jelenik meg (csak egyes oszlopok láthatók):

    ![Mintaadatok](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "JSON-mintaadatok")

6. Mostantól létrehozhat vizuális megjelenítését, ezeket az adatokat a biztonsági események jelentett a polgárok csatlakozás alkalmazás és az városa feldolgozó alkalmazás helyett más források megjelenítése. A táblázatos kimenet alján válassza ki a **sávdiagram** ikonra, majd kattintson **ábrázolási beállítások**.

    ![Oszlopdiagram létrehozása](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Oszlopdiagram létrehozása")

8. A **Ábrázolás testreszabása** lapon húzza az értékeket a megfelelő helyre a képernyőképen látható módon.

    ![Testre szabhatja a kördiagram](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "oszlopdiagram testreszabása")

   * Állítsa be **kulcsok** való **forrás**.
   * Állítsa be **értékek** való **< \id >**.
   * Az **Összesítés** mezőben adja meg a **COUNT** értéket.
   * Állítsa be **típusának megjelenítéséhez** való **tortadiagram**.

     Kattintson az **Alkalmaz** gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a cikkel, leállíthatja a fürtöt. Ehhez az Azure Databricks-munkaterület bal oldali panelén kattintson a **Fürtök** elemre. A leállítani kívánt fürtnél vigye az egérmutatót a **Műveletek** oszlopban található három pont fölé, majd kattintson a **Leállítás** ikonra.

![Databricks-fürt leállítása](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ön nem állítja le manuálisan a fürt automatikusan leáll, ha a kiválasztott megadott a **leállítása után \_ \_ ennyi perc inaktivitás** jelölőnégyzetet a fürt létrehozásakor. Ebben az esetben a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>További lépések

Ebben a cikkben egy Spark-fürt létrehozása az Azure Databricksben, és futtatta egy Spark-feladatot az Azure Open adatkészletekből származó adatok használatával. A [Spark-adatforrások](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) áttekintésével azt is megismerheti, hogyan importálhat adatokat más adatforrásokból az Azure Databricksbe. Folytassa a következő cikkel annak megismeréséhez, hogyan végezhet ETL-műveletet (adatok kinyerését, átalakítását és betöltését) az Azure Databricks használatával.

> [!div class="nextstepaction"]
>[Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](databricks-extract-load-sql-data-warehouse.md)

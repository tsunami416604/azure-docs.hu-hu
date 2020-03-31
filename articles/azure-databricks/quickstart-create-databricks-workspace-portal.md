---
title: Rövid útmutató – Spark-feladat futtatása az Azure Databricks szolgáltatáson az Azure Portalon
description: Ez a rövid útmutató bemutatja, hogyan használhatja az Azure Portalon egy Azure Databricks-munkaterület, egy Apache Spark-fürt, és egy Spark-feladat futtatásához.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 03/23/2020
ms.custom: mvc
ms.openlocfilehash: d6af521238a034bc22612335119f08284b87eb4b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132705"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Rövid útmutató: Spark-feladatok futtatása Azure Databricksen az Azure Portal használatával

Ebben a rövid útmutatóban az Azure Portalsegítségével hozzon létre egy Azure Databricks-munkaterületet egy Apache Spark-fürttel. Futtat egy feladatot a fürtön, és egyéni diagramok használatával valós idejű jelentéseket készít bostoni biztonsági adatokból.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

> [!Note]
> Ez az oktatóanyag nem hajtható végre **az Azure ingyenes próba-előfizetésével.**
> Ha van ingyenes fiókja, nyissa meg a profilját, és módosítsa az előfizetését **a felosztó-kirovó szolgáltatásra.** További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket. Ezután [távolítsa el a költségkeretet,](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)és kérjen [kvótanövelést](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) a régióban a vCPU-khoz. Az Azure Databricks-munkaterület létrehozásakor kiválaszthatja a **próbaverziós (prémium – 14 napos ingyenes dbári)** díjszabási szintet, hogy a munkaterület 14 napig hozzáférést biztosítson az ingyenes prémium szintű Azure Databricks KBB-khoz.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban egy Azure Databricks-munkaterületet fog létrehozni az Azure Portal használatával.

1. Az Azure Portalon válassza az**Analytics** > Erőforrás > létrehozása**Azure Databricks** **lehetőséget.**

    ![Databricks az Azure Portalon](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks az Azure Portalon")

2. Az **Azure Databricks szolgáltatás** pontban adja meg az értékeket Databricks-munkaterület létrehozásához.

    ![Azure Databricks-munkaterület létrehozása](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Azure Databricks-munkaterület létrehozása")

    Adja meg a következő értékeket:
    
    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../azure-resource-manager/management/overview.md). |
    |**Helyen**     | Válassza az **USA 2. nyugati régióját**. A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).        |
    |**Árképzési szint**     |  Válasszon **a Standard**, **Premium**vagy **Trial**. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Válassza **a Véleményezés + Létrehozás**lehetőséget, majd a Create **lehetőséget.** A munkaterület létrehozása eltarthat néhány percig. A munkaterület létrehozása során megtekintheti a központi telepítési állapotot az **Értesítések**területen. A folyamat befejezése után a rendszer automatikusan hozzáadja a felhasználói fiókot rendszergazdai felhasználóként a munkaterületen.

    ![Databricks telepítési csempe](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Databricks telepítési csempe")

    Ha egy munkaterület telepítése sikertelen, a munkaterület továbbra is hibás állapotban jön létre. Törölje a sikertelen munkaterületet, és hozzon létre egy új munkaterületet, amely megoldja a telepítési hibákat. A sikertelen munkaterület törlésekor a felügyelt erőforráscsoport és a sikeresen üzembe helyezett erőforrások is törlődnek.

## <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

> [!NOTE]
> Ha egy ingyenes fiókot használna az Azure Databricks-fürt létrehozásához, a fürt létrehozása előtt nyissa meg a saját profilját, és módosítsa az előfizetését **használatalapú fizetésre**. További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket.

1. Az Azure Portalon lépjen a korábban létrehozott Databricks-munkaterülethez, majd kattintson a **Munkaterület indítása** elemre.

2. A rendszer átirányítja az Azure Databricks portáljára. A portálon kattintson az **Új fürt gombra.**

    ![Databricks az Azure-ban](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks az Azure-ban")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-ban](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-ban")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

   * Adjon egy nevet a fürtnek.
   * Ebben a cikkben hozzon létre egy fürtöt **5.3-as** futásidejű.
   * Győződjön meg arról, hogy bejelöli a **Percek \_ \_ utáni inaktivitás** jelölőnégyzetet. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.
    
     Válassza a **Fürt létrehozása** lehetőséget. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

További információt a fürtök létrehozásáról a [Spark-fürtök az Azure Databricks használatával történő létrehozását](/azure/databricks/clusters/create) ismertető szakaszban talál.

## <a name="run-a-spark-sql-job"></a>Spark SQL-feladat futtatása

A következő feladatokat hozzon létre egy jegyzetfüzetet databricks, konfigurálja a jegyzetfüzetet, hogy adatokat olvasson egy Azure Open datasets, majd futtassa a Spark SQL-feladat az adatokon.

1. A bal oldali ablaktáblában válassza az **Azure Databricks**lehetőséget. A **Gyakori feladatok területen**válassza az **Új jegyzetfüzet lehetőséget.**

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

2. A **Jegyzetfüzet létrehozása** párbeszédpanelen adjon meg egy nevet, válassza a **Python** nyelvet, és válassza ki a korábban létrehozott Spark-fürtöt.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Jegyzetfüzet létrehozása a Databricks-ben")

    Kattintson a **Létrehozás** gombra.

3. Ebben a lépésben hozzon létre egy Spark DataFrame bostoni biztonsági adatok at [Azure Open Datasets,](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)és az SQL segítségével az adatok lekérdezése.

   A következő parancs beállítja az Azure storage hozzáférési adatait. Illessze be ezt a PySpark-kódot az első cellába, és a **Shift+Enter billentyűkombinációval** futtassa a kódot.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   A következő parancs lehetővé teszi, hogy a Spark távolról olvasson a Blob storage-ból. Illessze be ezt a PySpark-kódot a következő cellába, és a **Shift+Enter billentyűkombinációval** futtassa a kódot.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   A következő parancs létrehoz egy DataFrame-et. Illessze be ezt a PySpark-kódot a következő cellába, és a **Shift+Enter billentyűkombinációval** futtassa a kódot.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Sql utasítás futtatása adja vissza a legfelső 10 adatsort a **forrásnak**nevezett ideiglenes nézetből. Illessze be ezt a PySpark-kódot a következő cellába, és a **Shift+Enter billentyűkombinációval** futtassa a kódot.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Így egy, az alábbi képernyőképhez hasonló táblázatos kimenet jelenik meg (csak egyes oszlopok láthatók):

    ![Mintaadatok](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Minta JSON-adatok")

6. Most hozzon létre egy vizuális ábrázolása ezeket az adatokat, hogy hány biztonsági események jelentik a Citizens Connect app és a City Worker App helyett más forrásokból. A táblázatos kimenet alján jelölje ki a **Sávdiagram** ikont, majd kattintson a **Telek beállításai gombra**.

    ![Sávdiagram létrehozása](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Sávdiagram létrehozása")

8. A **Ábrázolás testreszabása** lapon húzza az értékeket a megfelelő helyre a képernyőképen látható módon.

    ![Kördiagram testreszabása](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Sávdiagram testreszabása")

   * Állítsa be a **kulcsokat** a **forráshoz.**
   * Az **értékek beállítása** **<\id>**.
   * Az **Összesítés** mezőben adja meg a **COUNT** értéket.
   * A **Megjelenítési típus** beállítása **Kördiagramra**.

     Kattintson az **Alkalmaz** gombra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a cikkel, leállíthatja a fürtöt. Ehhez az Azure Databricks-munkaterület bal oldali panelén kattintson a **Fürtök** elemre. A leállítani kívánt fürtnél vigye az egérmutatót a **Műveletek** oszlopban található három pont fölé, majd kattintson a **Leállítás** ikonra.

![Databricks-fürt leállítása](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha nem szakítja meg manuálisan a fürtöt, az automatikusan leáll, feltéve, hogy a fürt létrehozásakor bejelölte a **Percek \_ \_ utáni inaktivitás** után jelölőnégyzetet. Ebben az esetben a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>További lépések

Ebben a cikkben létrehozott egy Spark-fürtaz Azure Databricks és futtatott egy Spark-feladat az Azure Open Datasets adatok használatával. A [Spark-adatforrások](/azure/databricks/data/data-sources/index) áttekintésével azt is megismerheti, hogyan importálhat adatokat más adatforrásokból az Azure Databricksbe. Folytassa a következő cikkel annak megismeréséhez, hogyan végezhet ETL-műveletet (adatok kinyerését, átalakítását és betöltését) az Azure Databricks használatával.

> [!div class="nextstepaction"]
>[Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](databricks-extract-load-sql-data-warehouse.md)

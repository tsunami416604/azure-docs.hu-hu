---
title: Rövid útmutató – Spark-feladatok futtatása Azure Databricks használatával Azure Portal
description: Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Portal egy Azure Databricks munkaterület, egy Apache Spark fürt és egy Spark-feladatok futtatásához.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 03/23/2020
ms.custom: mvc, tracking-python
ms.openlocfilehash: 6c7226a56d5811438ec9d0703e0b8242df13e17b
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84559305"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Rövid útmutató: Spark-feladatok futtatása Azure Databricksen az Azure Portal használatával

Ebben a rövid útmutatóban a Azure Portal használatával hoz létre egy Apache Spark-fürtöt tartalmazó Azure Databricks-munkaterületet. Futtat egy feladatot a fürtön, és egyéni diagramokkal valós idejű jelentéseket hozhat létre a Boston biztonsági adataiból.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

> [!Note]
> Ez az oktatóanyag nem hajtható végre az **Azure ingyenes próbaverziós előfizetésével**.
> Ha ingyenes fiókkal rendelkezik, lépjen a profilba, és változtassa meg az előfizetését **az utólagos**elszámolású verzióra. További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket. Ezután [távolítsa el a](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)költségkeretet, és [igényeljen kvóta-növekedést](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) a régiójában lévő vCPU. A Azure Databricks munkaterületének létrehozásakor kiválaszthatja a **próbaverzió (prémium-14 napos ingyenes dBu)** díjszabását, hogy a munkaterület 14 napig elérhető legyen az ingyenes prémium Azure Databricks dBu.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban egy Azure Databricks-munkaterületet fog létrehozni az Azure Portal használatával.

1. A Azure Portal válassza az **erőforrás létrehozása**  >  **elemzési**  >  **Azure Databricks**lehetőséget.

    ![Databricks Azure Portal](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks Azure Portal")

2. Az **Azure Databricks szolgáltatás** pontban adja meg az értékeket Databricks-munkaterület létrehozásához.

    ![Azure Databricks-munkaterület létrehozása](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Azure Databricks-munkaterület létrehozása")

    Adja meg a következő értékeket:
    
    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport olyan tároló, amely egy adott Azure-megoldás kapcsolódó erőforrásait tartalmazza. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../azure-resource-manager/management/overview.md). |
    |**Hely**     | Válassza az **USA 2. nyugati régióját**. A további elérhető régiókért tekintse meg az [elérhető Azure-szolgáltatások régiók szerinti bontását](https://azure.microsoft.com/regions/services/).        |
    |**Díjszabási csomag**     |  Válassza a **standard**, **prémium**vagy **próbaverzió**lehetőséget. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás**lehetőséget. A munkaterület létrehozása eltarthat néhány percig. A munkaterület létrehozása során megtekintheti a központi telepítés állapotát az **értesítésekben**. A folyamat befejezése után a rendszer automatikusan hozzáadja a felhasználói fiókot a munkaterületen rendszergazdai felhasználóként.

    ![Databricks üzembe helyezési csempe](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Databricks üzembe helyezési csempe")

    Ha a munkaterület telepítése meghiúsul, a munkaterület még sikertelen állapotban jön létre. Törölje a sikertelen munkaterületet, és hozzon létre egy új munkaterületet, amely feloldja a telepítési hibákat. A sikertelen munkaterület törlésekor a felügyelt erőforráscsoport és a sikeresen telepített erőforrások is törlődnek.

## <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

> [!NOTE]
> Ha egy ingyenes fiókot használna az Azure Databricks-fürt létrehozásához, a fürt létrehozása előtt nyissa meg a saját profilját, és módosítsa az előfizetését **használatalapú fizetésre**. További információkért lásd az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) ismertető cikket.

1. Az Azure Portalon lépjen a korábban létrehozott Databricks-munkaterülethez, majd kattintson a **Munkaterület indítása** elemre.

2. A rendszer átirányítja az Azure Databricks portáljára. A portálon kattintson az **új fürt**elemre.

    ![Databricks az Azure-ban](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks az Azure-ban")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-ban](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-ban")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

   * Adjon egy nevet a fürtnek.
   * Ehhez a cikkhez hozzon létre egy **5,3** futtatókörnyezettel rendelkező fürtöt.
   * Győződjön meg arról, hogy a **megszakítás \_ \_ perc inaktivitás után** jelölőnégyzet be van jelölve. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.
    
     Válassza a **Fürt létrehozása** lehetőséget. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

További információt a fürtök létrehozásáról a [Spark-fürtök az Azure Databricks használatával történő létrehozását](/azure/databricks/clusters/create) ismertető szakaszban talál.

## <a name="run-a-spark-sql-job"></a>Spark SQL-feladat futtatása

A következő feladatok végrehajtásával hozzon létre egy jegyzetfüzetet a Databricks, konfigurálja úgy a jegyzetfüzetet, hogy adatokat olvasson be egy Azure Open-adatkészletből, majd futtasson egy Spark SQL-feladatot az adatokon.

1. A bal oldali ablaktáblán válassza a **Azure Databricks**lehetőséget. Az **Általános feladatok**területen válassza az **új jegyzetfüzet**lehetőséget.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

2. A **Jegyzetfüzet létrehozása** párbeszédpanelen adjon meg egy nevet, válassza ki a **Python** nyelvet, és válassza ki a korábban létrehozott Spark-fürtöt.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Jegyzetfüzet létrehozása a Databricks-ben")

    Kattintson a **Létrehozás** gombra.

3. Ebben a lépésben hozzon létre egy Spark-DataFrame az [Azure Open-adatkészletek](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)Boston biztonsági adatainak használatával, és használja az SQL-t az adatok lekérdezéséhez.

   A következő parancs az Azure Storage elérési adatait állítja be. Illessze be ezt a PySpark-kódot az első cellába, és a **SHIFT + ENTER** billentyűkombinációt használva futtassa a kódot.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   A következő parancs lehetővé teszi, hogy a Spark távolról is beolvassa a blob Storage-ból. Illessze be ezt a PySpark-kódot a következő cellába, és a **SHIFT + ENTER** billentyűkombinációt használva futtassa a kódot.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   A következő parancs létrehoz egy DataFrame. Illessze be ezt a PySpark-kódot a következő cellába, és a **SHIFT + ENTER** billentyűkombinációt használva futtassa a kódot.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Az SQL-utasítás futtatása a **forrás**nevű ideiglenes nézetből származó adatok első 10 sorát adja vissza. Illessze be ezt a PySpark-kódot a következő cellába, és a **SHIFT + ENTER** billentyűkombinációt használva futtassa a kódot.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. Így egy, az alábbi képernyőképhez hasonló táblázatos kimenet jelenik meg (csak egyes oszlopok láthatók):

    ![Mintaadatok](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "JSON-adatminta")

6. Most létre kell hoznia egy vizualizációs ábrázolást, amely megmutatja, hogy hány biztonsági eseményt jelentettek a polgárok az alkalmazás és a városi feldolgozói alkalmazás használatával más források helyett. A táblázatos kimenet alján válassza a **oszlopdiagram** ikont, majd kattintson a **ábrázolási beállítások**elemre.

    ![Sávdiagram létrehozása](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Sávdiagram létrehozása")

8. A **Ábrázolás testreszabása** lapon húzza az értékeket a megfelelő helyre a képernyőképen látható módon.

    ![Tortadiagram testreszabása](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Oszlopdiagram testreszabása")

   * **Kulcsok** beállítása a **forráshoz**.
   * **Értékek** beállítása **< \id>**.
   * Az **Összesítés** mezőben adja meg a **COUNT** értéket.
   * A **megjelenítési típus** beállítása a **tortadiagramra**

     Kattintson az **Alkalmaz** gombra.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha végzett a cikkel, leállíthatja a fürtöt. Ehhez az Azure Databricks-munkaterület bal oldali panelén kattintson a **Fürtök** elemre. A leállítani kívánt fürtnél vigye az egérmutatót a **Műveletek** oszlopban található három pont fölé, majd kattintson a **Leállítás** ikonra.

![Databricks-fürt leállítása](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha nem állítja be manuálisan a fürtöt, a rendszer automatikusan leállítja, ha a fürt létrehozásakor bejelölte a **megszakítás \_ \_ perc inaktivitás után** jelölőnégyzetet. Ebben az esetben a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy Spark-fürtöt hozott létre a Azure Databricksban, és egy Spark-feladatot futtatott az Azure Open-adatkészletek adatainak használatával. A [Spark-adatforrások](/azure/databricks/data/data-sources/index) áttekintésével azt is megismerheti, hogyan importálhat adatokat más adatforrásokból az Azure Databricksbe. Folytassa a következő cikkel annak megismeréséhez, hogyan végezhet ETL-műveletet (adatok kinyerését, átalakítását és betöltését) az Azure Databricks használatával.

> [!div class="nextstepaction"]
>[Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](databricks-extract-load-sql-data-warehouse.md)

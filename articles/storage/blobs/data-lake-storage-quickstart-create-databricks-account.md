---
title: 'Rövid útmutató: Spark-feladatok futtatása Azure Databricksen az Azure Portal használatával | Microsoft Docs'
description: Ez a rövid útmutató bemutatja, hogyan használható az Azure Portal egy Azure Databricks-munkaterület és egy Apache Spark-fürt létrehozásához, illetve Spark-feladatok futtatásához.
services: storage
author: jamesbak
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/06/2018
ms.openlocfilehash: 14ea060ca29d181023086ad241db59d275d2c581
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975880"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Rövid útmutató: Spark-feladatok futtatása Azure Databricksen az Azure Portal használatával

Ez a rövid útmutató bemutatja, hogyan futtathat az Azure Databricks használatával Apache Spark-feladatokat elemzések végrehajtásához az előzetes verziójú Azure Data Lake Storage Gen2-kompatibilis tárfiókban tárolt adatokon.

A Spark-feladat részeként egy rádióadó előfizetési adatait elemezzük, hogy a demográfiai adatok alapján betekintést nyerjünk az ingyenes/fizetős használatba.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

- [Data Lake Storage Gen2-kompatibilis tárfiók létrehozása](data-lake-storage-quickstart-create-account.md)

## <a name="set-aside-storage-account-configuration"></a>Tárfiók-konfiguráció feljegyzése

> [!IMPORTANT]
> Az oktatóanyag során szüksége lesz a tárfiók nevére és hozzáférési kulcsára. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, és szűrjön a *tár* kifejezésre. Válassza a **Tárfiókok** lehetőséget, és keresse meg az oktatóanyaghoz létrehozott fiókot.
>
> Az **Áttekintés** területről másolja be a tárfiók **nevét** egy szövegszerkesztőbe. Ezután válassza a **Hozzáférési kulcsok** lehetőséget, és másolja ki a **key1** értékét a szövegszerkesztőbe, mivel a későbbi parancsokhoz mindkét értékre szükség lesz.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban egy Azure Databricks-munkaterületet fog létrehozni az Azure Portal használatával.

1. Az Azure Portalon válassza az **Erőforrás létrehozása** > **Elemzés** > **Azure Databricks** elemet.

    ![Databricks az Azure Portalon](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks az Azure Portalon")

2. Az **Azure Databricks szolgáltatás** pontban adja meg az értékeket Databricks-munkaterület létrehozásához.

    ![Azure Databricks-munkaterület létrehozása](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Azure Databricks-munkaterület létrehozása")

    Adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../../azure-resource-manager/resource-group-overview.md). |
    |**Hely**     | Válassza az **USA 2. nyugati régióját**. Nyugodtan választhat egy másik nyilvános régiót is igény szerint.        |
    |**Tarifacsomag**     |  Válassza a **Standard** vagy a **Prémium** előfizetést. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

    Válassza a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Létrehozás** gombra.

3. A munkaterület létrehozása eltarthat néhány percig. A munkaterület létrehozása alatt a portál jobb oldalán megjelenik az **Üzembehelyezési kérés elküldése az Azure Databricksnek** csempe. Lehetséges, hogy jobbra kell görgetnie az irányítópulton, hogy megjelenjen a csempe. Megjelenik egy folyamatjelző is a képernyő tetejéhez közel. Mindkét területen nyomon követheti a folyamat előrehaladását.

    ![Databricks üzembe helyezési csempe](./media/data-lake-storage-quickstart-create-databricks-account/databricks-deployment-tile.png "Databricks üzembe helyezési csempe")

## <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

1. Az Azure Portalon lépjen a létrehozott Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A rendszer átirányítja az Azure Databricks portáljára. A portálon válassza az **Új** > **Fürt** elemet.

    ![Databricks az Azure-on](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks az Azure-on")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-on](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-on")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

    * Adjon egy nevet a fürtnek.
    * Hozzon létre egy fürtöt **5.1 béta** modul.
    * Mindenképpen jelölje be a **Leállítás 120 percnyi tétlenség után** jelölőnégyzetet. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.

4. Válassza a **Fürt létrehozása** lehetőséget. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

További információt a fürtök létrehozásáról a [Spark-fürtök az Azure Databricks használatával történő létrehozását](https://docs.azuredatabricks.net/user-guide/clusters/create.html) ismertető szakaszban talál.

## <a name="create-storage-account-file-system"></a>Tárfiók fájlrendszerének létrehozása

Ebben a szakaszban létrehoz egy jegyzetfüzetet az Azure Databricks-munkaterületen, majd kódrészleteket futtat a tárfiók konfigurálásához.

1. Az [Azure Portalon](https://portal.azure.com) lépjen a létrehozott Azure Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A bal oldali panelen válassza a **Munkaterület** elemet. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Notebook létrehozása a Databricksben](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Notebook létrehozása a Databricksben")

3. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza a **Scala** nyelvet, majd válassza ki a korábban létrehozott Spark-fürtöt.

    ![Notebook létrehozása a Databricksben](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Notebook létrehozása a Databricksben")

    Kattintson a **Létrehozás** gombra.

4. Csatlakozzon a Databricks-munkaterület az ADLS Gen2 fiókjához. Ehhez három támogatott mechanizmussal: OAuth, a közvetlen hozzáférés az OAuth és a közvetlen hozzáférés használata a megosztott kulcsos csatlakoztatására. 

    Minden egyes mechanizmus az alábbi példában látható. Ha teszteli az példák, ne felejtse el cserélje le a zárójelben látható zárójelben a mintában a saját értékeire:

    **Csatlakoztassa az OAuth használatával**     
        
    ```scala
    %python%
    configs = {"fs.azure.account.auth.type": "OAuth",
        "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
        "fs.azure.account.oauth2.client.id": "<service-client-id>",
        "fs.azure.account.oauth2.client.secret": "<service-credentials>",
        "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant-id>/oauth2/token"}
    
    dbutils.fs.mount(
        source = "abfss://<file-system-name>@<account-name>.dfs.core.windows.net/[<directory-name>]",
        mount_point = "/mnt/<mount-name>",
        extra_configs = configs)
    ```

    **Közvetlen hozzáférés OAuth-val**

    ```scala
    spark.conf.set("fs.azure.account.auth.type.<account-name>.dfs.core.windows.net": "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type.<account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id.<account-name>.dfs.core.windows.net": "<service-client-id>")
    spark.conf.set("fs.azure.account.oauth2.client.secret.<account-name>.dfs.core.windows.net": "<service-credentials>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint.<account-name>.dfs.core.windows.net": "https://login.microsoftonline.com/<tenant-id>/oauth2/token")

    dbutils.fs.ls("abfss://<file-system-name>@<account-name>.dfs.core.windows.net/")
    ```
        
    **Közvetlen hozzáférés a megosztott kulcs** 

    ```scala    
    spark.conf.set("fs.azure.account.key.<account-name>.dfs.core.windows.net", "<account-key>")

    dbutils.fs.ls("abfs://<file-system-name>@<account-name>.dfs.core.windows.net/")
    ```

5. Írja be a kódot a első cellába, majd nyomja le az **SHIFT + ENTER** a futtatáshoz.

Létrejön a fájlrendszer a tárfiókhoz.

## <a name="ingest-sample-data"></a>Mintaadatok betöltése

Mielőtt ehhez a szakaszhoz hozzáfogna, a következő előfeltételeknek kell eleget tennie:

Írja be az alábbi kódot egy jegyzetfüzetcellába:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

A cellába, nyomja le az ENTER **SHIFT + ENTER** a kód futtatásához.

Most egy új cellába vtár alkönyvtára, adja meg a következő kódra, és cserélje le az értékeket zárójelben korábban használt ugyanazokat az értékeket:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

A cellába, nyomja le az ENTER **SHIFT + ENTER** a kód futtatásához.

## <a name="run-a-spark-sql-job"></a>Spark SQL-feladat futtatása

A következő feladatok végrehajtásával futtathat Spark SQL-feladatot az adatokon.

1. SQL-utasítás futtatásával hozzon létre egy ideiglenes táblát a JSON-mintaadatfájl, a **small_radio_json.json** adataiból. Az alábbi kódrészletben cserélje le a helyőrzőket a fájlrendszer és a tárfiók nevére. A korábban létrehozott jegyzetfüzet segítségével illessze be a kódrészletet a jegyzetfüzet egyik új kódcellájába, majd nyomja le a SHIFT + ENTER billentyűkombinációt.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<file-system-name>@<account-name>.dfs.core.windows.net/<PATH>/small_radio_json.json"
    )
    ```

    A parancs sikeres végrehajtása után a JSON-fájl összes adata táblaként jelenik meg a Databricks-fürtben.

    Az `%sql` nyelvi varázsparancs segítségével SQL-kódot futtathat egy másik típusba tartozó notebookról is. További információért olvassa el a [többféle nyelv notebookokban történő használatát](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook) ismertető cikket.

2. A futtatott lekérdezés jobb megértéséhez vessünk egy pillantást a JSON-mintafájl adatairól készült pillanatfelvételre. Illessze be a következő kódtöredéket a kódcellába, majd nyomja le a **SHIFT + ENTER** billentyűparancsot.

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

3. Így egy, az alábbi képernyőképhez hasonló táblázatos kimenet jelenik meg (csak egyes oszlopok láthatók):

    ![JSON-mintaadatok](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "JSON-mintaadatok")

    Többek között, a mintaadatok rögzíti a választógomb csatorna tartalmazzák (oszlopnév, **nemek**) és a saját előfizetés-e ingyenes vagy fizetős (oszlopnév, **szint**).

4. A következőkben vizuálisan jelenítjük meg ezeket az adatokat annak megfelelően, hogy az egyes nemek szerint hány felhasználó rendelkezik ingyenes fiókkal, illetve hányan fizetnek az előfizetésért. A táblázatos kimenet alján kattintson az **Oszlopdiagram** ikonra, majd az **Ábrázolási beállítások** elemre.

    ![Oszlopdiagram létrehozása](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Oszlopdiagram létrehozása")

5. A **Ábrázolás testreszabása** lapon húzza az értékeket a megfelelő helyre a képernyőképen látható módon.

    ![Oszlopdiagram testreszabása](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Oszlopdiagram testreszabása")

    - A **Kulcsok** mezőben adja meg a **gender** értéket.
    - Az **Adatsorozat-csoportok** mezőben adja meg a **level** értéket.
    - Az **Értékek** mezőben adja meg a **level** értéket.
    - Az **Összesítés** mezőben adja meg a **COUNT** értéket.

6. Kattintson az **Alkalmaz** gombra.

7. A kimenetben a következő képernyőképen látható vizuális megjelenítés jelenik meg:

     ![Oszlopdiagram testreszabása](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Oszlopdiagram testreszabása")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a cikkel, leállíthatja a fürtöt. Az Azure Databricks-munkaterületen kattintson a **Fürtök** elemre, majd keresse meg a leállítani kívánt fürtöt. Vigye az egérmutatót a **Műveletek** oszlopban a három pont fölé, és kattintson a **Leállítás** ikonra.

![Databricks-fürt leállítása](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha Ön nem állítja le manuálisan a fürt automatikusan leáll, a megadott választotta a **leállítása után \_ \_ ennyi perc inaktivitás** jelölőnégyzetet a fürt létrehozásakor. Ha bejelöli ezt a lehetőséget, a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>További lépések

Ennek a cikknek a segítségével létrehozott egy Spark-fürtöt az Azure Databricksben, illetve futtatott egy Spark-feladatot a Data Lake Storage Gen2-kompatibilis tárfiók adatainak felhasználásával. A [Spark-adatforrások](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) áttekintésével azt is megismerheti, hogyan importálhat adatokat más adatforrásokból az Azure Databricksbe. Folytassa a következő cikkel annak megismeréséhez, hogyan végezhet ETL-műveletet (adatok kinyerését, átalakítását és betöltését) az Azure Databricks használatával.

> [!div class="nextstepaction"]
>[Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)

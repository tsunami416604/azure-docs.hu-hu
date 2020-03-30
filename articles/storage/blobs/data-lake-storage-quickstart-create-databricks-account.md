---
title: 'Rövid útmutató: Adatok elemzése az Azure Data Lake Storage Gen2 szolgáltatásban az Azure Databricks használatával | Microsoft dokumentumok'
description: Ismerje meg, hogyan futtathat egy Spark-feladatot az Azure Databricks-en az Azure Portal és egy Azure Data Lake Storage Gen2 tárfiók használatával.
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/17/2020
ms.reviewer: jeking
ms.openlocfilehash: 346795b79a78589d949b035a803a67a9e5a2e8e5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77470734"
---
# <a name="quickstart-analyze-data-with-databricks"></a>Rövid útmutató: Adatok elemzése a Databricks segítségével

Ebben a rövid útmutatóban egy Apache Spark-feladatot futtat az Azure Databricks használatával, hogy elemzéseket végezzen egy tárfiókban tárolt adatokon. A Spark-feladat részeként elemezheti a rádiócsatorna-előfizetési adatokat, hogy betekintést nyerjen az ingyenes/fizetős használatba a demográfiai adatok alapján.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* Az Azure Data Lake Gen2 tárfiók neve. [Hozzon létre egy Azure Data Lake Storage Gen2 tárfiókot.](data-lake-storage-quickstart-create-account.md)

* A **Storage Blob Data Contributor**hozzárendelt szerepkörrel rendelkező Azure-szolgáltatásnév bérlői azonosítója, alkalmazásazonosítója és jelszava. [Egyszerű szolgáltatás létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md).

  > [!IMPORTANT]
  > Rendelje hozzá a szerepkört a Data Lake Storage Gen2 tárfiók hatókörében. Szerepkört rendelhet a fölérendelt erőforráscsoporthoz vagy -előfizetéshez, de engedélyekkel kapcsolatos hibákat fog kapni, amíg ezek a szerepkör-hozzárendelések propagálnak a tárfiókba.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban egy Azure Databricks-munkaterületet fog létrehozni az Azure Portal használatával.

1. Az Azure Portalon válassza az**Analytics** > Erőforrás > létrehozása**Azure Databricks** **lehetőséget.**

    ![Databricks az Azure Portalon](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks az Azure Portalon")

2. Az **Azure Databricks szolgáltatás** pontban adja meg az értékeket Databricks-munkaterület létrehozásához.

    ![Azure Databricks-munkaterület létrehozása](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Azure Databricks-munkaterület létrehozása")

    Adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Munkaterület neve**     | Adja meg a Databricks-munkaterület nevét.        |
    |**Előfizetés**     | Válassza ki a legördülő menüből a saját Azure-előfizetését.        |
    |**Erőforráscsoport**     | Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni. Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. További információért olvassa el az [Azure-erőforráscsoportok áttekintését](../../azure-resource-manager/management/overview.md). |
    |**Helyen**     | Válassza az **USA 2. nyugati régióját**. Nyugodtan választhat egy másik nyilvános régiót is igény szerint.        |
    |**Árképzési szint**     |  Válassza a **Standard** vagy a **Prémium** előfizetést. További információkért a csomagokkal kapcsolatban tekintse meg a [Databricks díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/databricks/).       |

3. A fiók létrehozása eltarthat néhány percig. A művelet állapotának figyeléséhez tekintse meg a folyamatjelző sávot a tetején.

4. Válassza a **Rögzítés az irányítópulton**, majd a **Létrehozás** lehetőséget.

## <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

1. Az Azure Portalon lépjen a létrehozott Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A rendszer átirányítja az Azure Databricks portáljára. A portálon válassza az **Új** > **fürt**lehetőséget.

    ![Databricks az Azure-ban](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks az Azure-ban")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-ban](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-ban")

    Adjon meg értékeket a következő mezőkben, és fogadja el az alapértelmezett értékeket a többi mezőben:

    - Adjon egy nevet a fürtnek.
     
    - Mindenképpen jelölje be a **Leállítás 120 percnyi tétlenség után** jelölőnégyzetet. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.

4. Válassza a **Fürt létrehozása** lehetőséget. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

További információt a fürtök létrehozásáról a [Spark-fürtök az Azure Databricks használatával történő létrehozását](https://docs.azuredatabricks.net/user-guide/clusters/create.html) ismertető szakaszban talál.

## <a name="create-storage-account-container"></a>Tárfiók-tároló létrehozása

Ebben a szakaszban létrehoz egy jegyzetfüzetet az Azure Databricks-munkaterületen, majd kódrészleteket futtat a tárfiók konfigurálásához.

1. Az [Azure Portalon](https://portal.azure.com) lépjen a létrehozott Azure Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A bal oldali panelen válassza a **Munkaterület** elemet. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

3. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza a **Scala** nyelvet, majd válassza ki a korábban létrehozott Spark-fürtöt.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Jegyzetfüzet létrehozása a Databricks-ben")

    Kattintson a **Létrehozás** gombra.

4. Másolja és illessze be a következő kódblokkot az első cellába, de még ne futtassa ezt a kódot.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```
5. Ebben a kódblokkban `storage-account-name` `appID`cserélje `password`le `tenant-id` a , , és a helyőrző értékeket ebben a kódblokkban az egyszerű szolgáltatás létrehozásakor gyűjtött értékekre. Állítsa `container-name` a helyőrző értéket bármilyen nevet, amelyet a tárolónak adni szeretne.

6. A **BLOKKban** lévő kód futtatásához nyomja le a SHIFT + ENTER billentyűket.

## <a name="ingest-sample-data"></a>Mintaadatok betöltése

Mielőtt ehhez a szakaszhoz hozzáfogna, a következő előfeltételeknek kell eleget tennie:

Írja be az alábbi kódot egy jegyzetfüzetcellába:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

A cellában nyomja le a **SHIFT + ENTER billentyűkombinációt** a kód futtatásához.

Most egy új cellában írja be a következő kódot, és cserélje le a zárójelben megjelenő értékeket a korábban használt értékekkel:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")

A cellában nyomja le a **SHIFT + ENTER billentyűkombinációt** a kód futtatásához.

## <a name="run-a-spark-sql-job"></a>Spark SQL-feladat futtatása

A következő feladatok végrehajtásával futtathat Spark SQL-feladatot az adatokon.

1. SQL-utasítás futtatásával hozzon létre egy ideiglenes táblát a JSON-mintaadatfájl, a **small_radio_json.json** adataiból. Az alábbi kódtöredékben cserélje le a helyőrzőket a tároló és a tárfiók nevére. A korábban létrehozott jegyzetfüzet segítségével illessze be a kódrészletet a jegyzetfüzet egyik új kódcellájába, majd nyomja le a SHIFT + ENTER billentyűkombinációt.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json"
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

    ![Minta JSON-adatok](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Minta JSON-adatok")

    A mintaadatok többek között a rádiócsatorna közönségének nemét (oszlopnév, **nem)** rögzítik, valamint azt, hogy előfizetésük ingyenes vagy fizetős -e (oszlopnév, **szint).**

4. A következőkben vizuálisan jelenítjük meg ezeket az adatokat annak megfelelően, hogy az egyes nemek szerint hány felhasználó rendelkezik ingyenes fiókkal, illetve hányan fizetnek az előfizetésért. A táblázatos kimenet alján kattintson az **Oszlopdiagram** ikonra, majd az **Ábrázolási beállítások** elemre.

    ![Sávdiagram létrehozása](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Sávdiagram létrehozása")

5. A **Ábrázolás testreszabása** lapon húzza az értékeket a megfelelő helyre a képernyőképen látható módon.

    ![Sávdiagram testreszabása](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Sávdiagram testreszabása")

    - A **Kulcsok** mezőben adja meg a **gender** értéket.
    - Az **Adatsorozat-csoportok** mezőben adja meg a **level** értéket.
    - Az **Értékek** mezőben adja meg a **level** értéket.
    - Az **Összesítés** mezőben adja meg a **COUNT** értéket.

6. Kattintson az **Alkalmaz** gombra.

7. A kimenetben a következő képernyőképen látható vizuális megjelenítés jelenik meg:

     ![Sávdiagram testreszabása](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Sávdiagram testreszabása")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután befejezte ezt a cikket, megszüntetheti a fürtöt. Az Azure Databricks-munkaterületen kattintson a **Fürtök** elemre, majd keresse meg a leállítani kívánt fürtöt. Vigye az egérmutatót a **Műveletek** oszlopban a három pont fölé, és kattintson a **Leállítás** ikonra.

![Databricks-fürt leállítása](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha nem szakítja meg manuálisan a fürtöt, akkor automatikusan leáll, feltéve, hogy a fürt létrehozásakor bejelölte a **Leállítás \_ \_ percek után** a inaktivitás után jelölőnégyzetet. Ha bejelöli ezt a lehetőséget, a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>További lépések

Ennek a cikknek a segítségével létrehozott egy Spark-fürtöt az Azure Databricksben, illetve futtatott egy Spark-feladatot a Data Lake Storage Gen2-kompatibilis tárfiók adatainak felhasználásával.

Folytassa a következő cikkel annak megismeréséhez, hogyan végezhet ETL-műveletet (adatok kinyerését, átalakítását és betöltését) az Azure Databricks használatával.

> [!div class="nextstepaction"]
>[Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával.](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)

- Ha meg szeretné tudni, hogyan importálhat adatokat más adatforrásokból az Azure Databricks be, olvassa el a [Spark adatforrások.](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html)

- Az Azure Data Lake Storage Gen2 Azure Databricks-munkaterületről való elérésének egyéb módjairól az [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)webhelyen olvashat.

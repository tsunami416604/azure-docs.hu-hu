---
title: 'Gyors útmutató: Adatok elemzése az Azure Data Lake Storage Gen2 Azure Databricks használatával |} A Microsoft Docs'
description: Ismerje meg, a Spark-feladatok futtatása Azure Databricksen az Azure portal és a egy Azure Data Lake Storage Gen2 storage-fiók használatával.
services: storage
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/15/2019
ms.openlocfilehash: 1b5f05f3cddea986230327165399b0fe530e361b
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56588075"
---
# <a name="quickstart-analyze-data-in-azure-data-lake-storage-gen2-by-using-azure-databricks"></a>Gyors útmutató: Adatok elemzése az Azure Data Lake Storage Gen2 Azure Databricks használatával

Ez a rövid útmutató bemutatja, hogyan egy Azure Databricks használatával, amely rendelkezik az Azure Data Lake Storage Gen2 engedélyezve van a tárfiókban tárolt adatokon végrehajtandó analytics Apache Spark-feladat futtatásához.

A Spark-feladat részeként egy rádióadó előfizetési adatait betekintést ingyenes/fizetős használati demográfiája alapján lesz elemez.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Hozzon létre egy Data Lake Gen2 tárfiókot. Lásd: [a rövid útmutató: Az Azure Data Lake Storage Gen2-storage-fiók létrehozása](data-lake-storage-quickstart-create-account.md)

  Illessze be a tárfiók nevét egy szövegfájlba. Szüksége lesz rá hamarosan.

*  Hozzon létre egy egyszerű szolgáltatást. Lásd: [hogyan: A portál használatával hozzon létre egy Azure AD alkalmazás és -szolgáltatásnév erőforrások eléréséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Van néhány adott tudnivaló, mivel ebben a cikkben hajtsa végre a lépéseket kell.

   :heavy_check_mark: A lépések végrehajtásakor a [alkalmazások szerepkörhöz rendeléséhez](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) szakaszt a cikk, ügyeljen arra, hogy rendelje hozzá a **Storage-Blobadatok Közreműködője** szerepkört a szolgáltatásnévhez.

   > [!IMPORTANT]
   > Ellenőrizze, hogy a szerepkört a Data Lake Storage Gen2 storage-fiók hatókörében. Szerepkör hozzárendelése a szülő erőforráscsoportba vagy előfizetésbe, de kap engedélyekkel kapcsolatos hibákat addig, amíg azokat a szerepkör-hozzárendeléseket a tárfiókhoz való propagálása.

   :heavy_check_mark: A lépések végrehajtásakor a [értékek beolvasása bejelentkezés](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) szakaszában a cikk, illessze be a bérlő Azonosítóját, Alkalmazásazonosító és hitelesítési kulcs értékeit egy szövegfájlba. Kell azokat, hamarosan.

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

3. Egy kis időt a munkaterület létrehozásához vesz igénybe. A munkaterület létrehozása folyamatban van, míg a **üzemelő példány elküldése az Azure Databricks** csempe jobb oldalán. Megtekintheti a címet, akkor előfordulhat, hogy görgessen jobbra az irányítópulton. Egy folyamatjelző sávot a képernyő tetején megjelenő is van. Mindkét területen nyomon követheti a folyamat előrehaladását.

    ![Databricks üzembe helyezési csempe](./media/data-lake-storage-quickstart-create-databricks-account/databricks-deployment-tile.png "Databricks üzembe helyezési csempe")

## <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

1. Az Azure Portalon lépjen a létrehozott Databricks-munkaterülethez, majd válassza a **Munkaterület indítása** elemet.

2. A rendszer átirányítja az Azure Databricks portáljára. A portálon válassza az **Új** > **Fürt** elemet.

    ![Databricks az Azure-on](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks az Azure-on")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-on](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-on")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

    * Adjon egy nevet a fürtnek.
    * Hozzon létre egy fürtöt **5.1** modul.
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

4. Másolja és illessze be az alábbi kódblokkot az első olyan cellára, de még ne futtassa ezt a kódot.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<application-id>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<authentication-key>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```

    > [!NOTE]
    > A kódblokk közvetlenül hozzáfér a Data Lake Gen2 végpont OAuth használatával, de más módon való csatlakozáshoz a Data Lake Storage Gen2-fiókot a Databricks-munkaterület. Például sikerült csatlakoztathatja a fájlrendszert az OAuth használatával, vagy egy közvetlen hozzáférés használata a megosztott kulcsot. <br>Ezek a módszerek példák, olvassa el a [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) a cikk az Azure Databricks webhelyén.

5. A kódblokk, cserélje le a `storage-account-name`, `application-id`, `authentication-id`, és `tenant-id` helyőrző értékeket az egyszerű szolgáltatás létrehozása során gyűjtött értékek a kód blokk. Állítsa be a `file-system-name` nevét bármilyen, a helyőrző értékét szeretné adni a fájlrendszerben.

    > [!NOTE]
    > Éles környezetben, fontolja meg, a hitelesítési kulcs tárolása az Azure Databricksben. Ezután adja hozzá egy keresse meg a kulcs a kódblokk, a hitelesítési kulcs helyett. Ez a rövid útmutató befejezése után tekintse meg a [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) cikk webhelyen az Azure Databricks erre a megközelítésre példa látható.

6. Nyomja le az **SHIFT + ENTER** kulcsok a kód futtatásához a blokk.

## <a name="ingest-sample-data"></a>Mintaadatok betöltése

Mielőtt ehhez a szakaszhoz hozzáfogna, a következő előfeltételeknek kell eleget tennie:

Írja be az alábbi kódot egy jegyzetfüzetcellába:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

A cellába, nyomja le az ENTER **SHIFT + ENTER** a kód futtatásához.

Most már alatti Ez egy új cellába, adja meg a következő kódot, és cserélje le az értékeket, amelyeket korábban használt ugyanazon értékekkel zárójelben jelennek meg:

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
     path  "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/<PATH>/small_radio_json.json"
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

Miután végzett ezzel a cikkel, leállíthatja a fürtöt. Az Azure Databricks-munkaterületen kattintson a **Fürtök** elemre, majd keresse meg a leállítani kívánt fürtöt. Vigye az egérmutatót a **Műveletek** oszlopban a három pont fölé, és kattintson a **Leállítás** ikonra.

![Databricks-fürt leállítása](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Databricks-fürt leállítása")

Ha Ön nem állítja le manuálisan a fürt automatikusan leáll, a megadott választotta a **leállítása után \_ \_ ennyi perc inaktivitás** jelölőnégyzetet a fürt létrehozásakor. Ha bejelöli ezt a lehetőséget, a fürt automatikusan leáll, ha a megadott ideig inaktív volt.

## <a name="next-steps"></a>További lépések

Ennek a cikknek a segítségével létrehozott egy Spark-fürtöt az Azure Databricksben, illetve futtatott egy Spark-feladatot a Data Lake Storage Gen2-kompatibilis tárfiók adatainak felhasználásával. A [Spark-adatforrások](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) áttekintésével azt is megismerheti, hogyan importálhat adatokat más adatforrásokból az Azure Databricksbe. Folytassa a következő cikkel annak megismeréséhez, hogyan végezhet ETL-műveletet (adatok kinyerését, átalakítását és betöltését) az Azure Databricks használatával.

> [!div class="nextstepaction"]
>[Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)

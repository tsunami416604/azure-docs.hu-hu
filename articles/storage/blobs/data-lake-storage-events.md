---
title: 'Oktatóanyag: Valósítsa meg a Data Lake rögzítési mintáját az Azure Databricks-különbözeti tábla frissítéséhez | Microsoft dokumentumok'
description: Ez az oktatóanyag bemutatja, hogyan használhatja az Event Grid-előfizetést, egy Azure-függvényt és egy Azure Databricks-feladatot az Azure DataLake Storage Gen2-ben tárolt táblába.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.openlocfilehash: 85fad873b6c176d2278ea48709d2892ab515a025
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303307"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Oktatóanyag: Valósítsa meg a Data Lake rögzítési mintáját a Databricks Delta tábla frissítéséhez

Ez az oktatóanyag bemutatja, hogyan kezelhetők az események egy hierarchikus névtérrel rendelkező tárfiókban.

Egy kis megoldást hozhat létre, amely lehetővé teszi a felhasználó számára, hogy feltöltsön egy Databricks Delta táblát egy vesszővel tagolt értékfájl (csv) feltöltésével, amely leírja az értékesítési rendelést. Ezt a megoldást egy Event Grid-előfizetés, egy Azure-függvény és egy Azure Databricks-beli [feladat](https://docs.azuredatabricks.net/user-guide/jobs.html) összekapcsolásával hozhatja létre.

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Hozzon létre egy Azure Grid-előfizetést, amely meghívja az Azure-függvényt.
> * Hozzon létre egy Azure-függvényt, amely értesítést kap egy eseményről, majd futtatja a feladatot az Azure Databricks-ben.
> * Hozzon létre egy Databricks-feladatot, amely beillesztegy ügyfélrendelést egy Databricks-különbözeti táblába, amely a tárfiókban található.

Ezt a megoldást fordított sorrendben fogjuk elkészíteni, kezdve az Azure Databricks munkaterülettel.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

* Hozzon létre egy hierarchikus névtérrel (Azure Data Lake Storage Gen2) tartalmazó tárfiókot. Ez az oktatóanyag `contosoorders`a . Győződjön meg arról, hogy a felhasználói fiók rendelkezik a [Storage Blob Data Contributor szerepkör](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) hozzárendelve.

  Lásd: [Azure Data Lake Storage Gen2 fiók létrehozása.](data-lake-storage-quickstart-create-account.md)

* Hozzon létre egy egyszerű szolgáltatás. [Lásd: Hogyan: A portál használatával hozzon létre egy Azure AD-alkalmazást és egyszerű szolgáltatást, amely képes hozzáférni az erőforrásokhoz.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

  Van néhány konkrét dolog, amit meg kell tennie, ahogy végrehajtja a cikkben leírt lépéseket.

  :heavy_check_mark: Az [alkalmazás hozzárendelése a](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) cikk szerepkörhöz szakaszában leírt lépések végrehajtásakor győződjön meg arról, hogy a Storage Blob Data **Contributor** szerepkört hozzárendeli az egyszerű szolgáltatáshoz.

  > [!IMPORTANT]
  > Győződjön meg arról, hogy rendelje hozzá a szerepkört a Data Lake Storage Gen2 tárfiók hatókörében. Szerepkört rendelhet a fölérendelt erőforráscsoporthoz vagy -előfizetéshez, de engedélyekkel kapcsolatos hibákat fog kapni, amíg ezek a szerepkör-hozzárendelések propagálnak a tárfiókba.

  :heavy_check_mark: A cikk bejelentkezési [értékeinek bekerülési értékeinek](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) végrehajtásakor illessze be a bérlői azonosítót, az alkalmazásazonosítót és a jelszóértékeket egy szöveges fájlba. Ezekre az értékekre később szüksége lesz.

## <a name="create-a-sales-order"></a>Értékesítési rendelés létrehozása

Először hozzon létre egy olyan csv-fájlt, amely leírja az értékesítési rendelést, majd töltse fel a fájlt a tárfiókba. Később a fájlból származó adatokat fogja használni a Databricks Delta tábla első sorának feltöltéséhez.

1. Nyissa meg az Azure Storage Explorert. Ezután keresse meg a tárfiókot, és a **Blob-tárolók** szakaszban hozzon létre egy új tároló nevű **adatokat.**

   ![adatmappa](./media/data-lake-storage-events/data-container.png "adatmappa")

   A Storage Explorer használatával kapcsolatos további tudnivalókért olvassa el [az Azure Storage Explorer használatával az Azure Data Lake Storage Gen2-fiókban tárolt adatok kezelését című témakört.](data-lake-storage-explorer.md)

2. Az **adattárolóban** hozzon létre egy bemenet nevű **mappát.**

3. Illessze be a következő szöveget egy szövegszerkesztőbe.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Mentse ezt a fájlt a helyi számítógépre, és adja meg a **name data.csv**.

5. A Tárolókezelőben töltse fel ezt a fájlt a **beviteli** mappába.  

## <a name="create-a-job-in-azure-databricks"></a>Feladat létrehozása az Azure Databricks-ben

Ebben a szakaszban a következő feladatokat hajthatja végre:

* Hozzon létre egy Azure Databricks-munkaterületet.
* Hozzon létre egy notebookot.
* Databricks Delta tábla létrehozása és feltöltése.
* Adjon hozzá olyan kódot, amely sorokat szúr be a Databricks Delta táblába.
* Hozzon létre egy feladatot.

### <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban egy Azure Databricks-munkaterületet fog létrehozni az Azure Portal használatával.

1. Az Azure Portalon válassza az**Analytics** > Erőforrás > létrehozása**Azure Databricks** **lehetőséget.**

    ![Databricks az Azure Portalon](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks az Azure Portalon")

2. Az **Azure Databricks szolgáltatás** pontban adja meg az értékeket Databricks-munkaterület létrehozásához.

    ![Azure Databricks-munkaterület létrehozása](./media/data-lake-storage-events/new-databricks-service.png "Azure Databricks-munkaterület létrehozása")

    A munkaterület létrehozása eltarthat néhány percig. A művelet állapotának figyeléséhez tekintse meg a folyamatjelző sávot a tetején.

### <a name="create-a-spark-cluster-in-databricks"></a>Spark-fürt létrehozása a Databricks használatával

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a létrehozott Azure Databricks-munkaterületet, majd válassza **a Munkaterület indítása**lehetőséget.

2. A rendszer átirányítja az Azure Databricks portáljára. A portálon válassza az **Új** > **fürt**lehetőséget.

    ![Databricks az Azure-ban](./media/data-lake-storage-events/databricks-on-azure.png "Databricks az Azure-ban")

3. Az **Új fürt** lapon adja meg a fürt létrehozásához szükséges értékeket.

    ![Databricks Spark-fürt létrehozása az Azure-ban](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Databricks Spark-fürt létrehozása az Azure-ban")

    Fogadja el az összes alapértelmezett értéket, kivéve a következőket:

    * Adjon egy nevet a fürtnek.
    * Mindenképpen jelölje be a **Leállítás 120 percnyi tétlenség után** jelölőnégyzetet. Adja meg az időtartamot (percben), amelynek elteltével le kell állítani a fürtöt, amennyiben az használaton kívül van.

4. Válassza a **Fürt létrehozása** lehetőséget. Ha a fürt már fut, notebookokat csatlakoztathat hozzá, illetve Spark-feladatokat futtathat.

További információt a fürtök létrehozásáról a [Spark-fürtök az Azure Databricks használatával történő létrehozását](https://docs.azuredatabricks.net/user-guide/clusters/create.html) ismertető szakaszban talál.

### <a name="create-a-notebook"></a>Jegyzetfüzet létrehozása

1. A bal oldali panelen válassza a **Munkaterület** elemet. A **Munkaterület** legördülő menüből válassza a **Létrehozás** > **Jegyzetfüzet** lehetőséget.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

2. A **Jegyzetfüzet létrehozása** párbeszédpanelen adja meg a jegyzetfüzet nevét. Válassza ki a **Python** nyelvet, majd válassza ki a korábban létrehozott Spark-fürtöt.

    ![Jegyzetfüzet létrehozása a Databricks-ben](./media/data-lake-storage-events/new-databricks-notebook.png "Jegyzetfüzet létrehozása a Databricks-ben")

    Kattintson a **Létrehozás** gombra.

### <a name="create-and-populate-a-databricks-delta-table"></a>Databricks Delta tábla létrehozása és feltöltése

1. A létrehozott jegyzetfüzetben másolja és illessze be a következő kódblokkot az első cellába, de még ne futtassa ezt a kódot.  

   Cserélje `appId`le `password` `tenant` a , , helyőrző értékeket ebben a kódblokkban az oktatóanyag előfeltételeinek végrehajtása során gyűjtött értékekre.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Ez a kód létrehoz egy **source_file**nevű widgetet. Később hozzon létre egy Azure-függvényt, amely meghívja ezt a kódot, és átadja a fájl elérési útját a widget.  Ez a kód is hitelesíti a szolgáltatás névszerint a tárfiókkal, és létrehoz néhány változót, amely más cellákban fog használni.

    > [!NOTE]
    > Éles környezetben érdemes lehet a hitelesítési kulcsot az Azure Databricks tárolja. Ezután a hitelesítési kulcs helyett adjon hozzá egy feltekintő kulcsot a kódblokkhoz. <br><br>A kódsor `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")`használata helyett például a következő kódsort kell `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))`használnia: . <br><br>Miután befejezte ezt az oktatóanyagot, tekintse meg az [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) cikket az Azure Databricks-webhelyen, hogy példákat a megközelítés.

2. A **BLOKKban** lévő kód futtatásához nyomja le a SHIFT + ENTER billentyűket.

3. Másolja a következő kódblokkot egy másik cellába, majd nyomja le a **SHIFT + ENTER** billentyűt a blokkban lévő kód futtatásához.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   Ez a kód létrehozza a Databricks Delta táblát a tárfiókban, majd betölti a korábban feltöltött csv-fájl ból származó kezdeti adatokat.

4. Miután a kódblokk sikeresen futott, távolítsa el ezt a kódblokkot a jegyzetfüzetből.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Sorokat beszúrni tartalmazó kód hozzáadása a Databricks Delta táblához

1. Másolja és illessze be a következő kódblokkot egy másik cellába, de ne futtassa ezt a cellát.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Ez a kód adatokat szúr be egy ideiglenes táblanézetbe egy csv-fájlból származó adatok használatával. A csv-fájl elérési útja a korábbi lépésben létrehozott beviteli vezérlőből származik.

2. Adja hozzá a következő kódot az ideiglenes táblanézet tartalmának a Databricks Delta táblával való egyesítéséhez.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>Feladat létrehozása

Hozzon létre egy feladatot, amely a korábban létrehozott jegyzetfüzetet futtatja. Később hozzon létre egy Azure-függvényt, amely futtatja ezt a feladatot, amikor egy esemény jön létre.

1. Kattintson **a Feladatok gombra.**

2. A **Feladatok** lapon kattintson a **Feladat létrehozása gombra.**

3. Adjon nevet a feladatnak, `upsert-order-data` majd válassza ki a munkafüzetet.

   ![Feladat létrehozása](./media/data-lake-storage-events/create-spark-job.png "Feladat létrehozása")

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Hozzon létre egy Azure-függvényt, amely futtatja a feladatot.

1. A Databricks munkaterület felső sarkában válassza a Személyek ikont, majd a **Felhasználói beállítások lehetőséget.**

   ![Fiók kezelése](./media/data-lake-storage-events/generate-token.png "Felhasználói beállítások")

2. Kattintson az **Új token létrehozása** gombra, majd a **Létrehozás** gombra.

   Győződjön meg róla, hogy másolja a token biztonságos helyre. Az Azure-függvénynek szüksége van erre a jogkivonatra a Databricks hitelesítéséhez, hogy futtathassa a feladatot.
  
3. Válassza az **Erőforrás létrehozása** gomb található az Azure Portal bal felső sarkában, majd válassza a Számítási > **függvény alkalmazás.**

   ![Azure-függvény létrehozása](./media/data-lake-storage-events/function-app-create-flow.png "Azure-függvény létrehozása")

4. A **Függvényalkalmazás létrehozása** lapján győződjön meg arról, hogy a **.NET Core lehetőséget** választja a futásidejű veremhez, és győződjön meg arról, hogy konfigurálegy Application Insights-példányt.

   ![A függvényalkalmazás konfigurálása](./media/data-lake-storage-events/new-function-app.png "A függvényalkalmazás konfigurálása")

5. A Függvényalkalmazás **Áttekintés lapján** kattintson a **Konfiguráció gombra.**

   ![A függvényalkalmazás konfigurálása](./media/data-lake-storage-events/configure-function-app.png "A függvényalkalmazás konfigurálása")

6. Az **Alkalmazás beállításai** lapon válassza az **Új alkalmazásbeállítás** gombot az egyes beállítások hozzáadásához.

   ![Konfigurációs beállítás hozzáadása](./media/data-lake-storage-events/add-application-setting.png "Konfigurációs beállítás hozzáadása")

   Adja hozzá a következő beállításokat:

   |Beállítás neve | Érték |
   |----|----|
   |**DBX_INSTANCE**| Az adattéglák munkaterületének régiója. Például:`westus2.azuredatabricks.net`|
   |**DBX_PAT**| A korábban létrehozott személyes hozzáférési jogkivonat. |
   |**DBX_JOB_ID**|A futó feladat azonosítója. A mi esetünkben `1`ez az érték .|
7. A függvényalkalmazás áttekintő lapján kattintson az **Új funkció** gombra.

   ![Új függvény](./media/data-lake-storage-events/new-function.png "Új függvény")

8. Válassza **az Azure Event Grid Trigger**lehetőséget.

   Telepítse a **Microsoft.Azure.WebJobs.Extensions.EventGrid** bővítményt, ha a rendszer erre kéri. Ha telepítenie kell, akkor újra ki kell választania az **Azure Event Grid Trigger t** a függvény létrehozásához.

   Megjelenik **az Új függvény** ablaktábla.

9. Az **Új függvény** ablaktáblán nevezze el az **UpsertOrder függvényt,** majd kattintson a **Létrehozás** gombra.

10. Cserélje le a kódfájl tartalmát erre a kódra, majd kattintson a **Mentés** gombra:

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   Ez a kód elemzi a létrehozott tárolási esemény adatait, majd létrehoz egy kérési üzenetet az eseményt kiváltó fájl URL-címével. Az üzenet részeként a függvény egy értéket ad át a **korábban** létrehozott source_file widgetnek. a függvénykód elküldi az üzenetet a Databricks-feladatnak, és a korábban beszerzett jogkivonatot használja hitelesítésként.

## <a name="create-an-event-grid-subscription"></a>Event Grid-előfizetés létrehozása

Ebben a szakaszban egy Event Grid-előfizetést hoz létre, amely meghívja az Azure-függvényt, amikor fájlokat tölt fel a tárfiókba.

1. A függvénykód lapon kattintson az **Eseményrács előfizetés hozzáadása** gombra.

   ![Új esemény-előfizetés](./media/data-lake-storage-events/new-event-subscription.png "Új esemény-előfizetés")

2. Az **Esemény-előfizetés létrehozása** lapon nevezze el az előfizetést, majd a lap mezőivel válassza ki a tárfiókot.

   ![Új esemény-előfizetés](./media/data-lake-storage-events/new-event-subscription-2.png "Új esemény-előfizetés")

3. A **Szűrő eseménytípusokra** legördülő listában jelölje ki a **Létrehozott Blob**és a **Törölt paca** eseményeket, majd kattintson a **Létrehozás** gombra.

## <a name="test-the-event-grid-subscription"></a>Az Event Grid-előfizetés tesztelése

1. Hozzon létre `customer-order.csv`egy nevű fájlt, illessze be a következő adatokat a fájlba, és mentse a helyi számítógépre.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. A Tárterületen töltse fel ezt a fájlt a tárfiók **bemeneti** mappájába.

   A fájl feltöltése a **Microsoft.Storage.BlobCreated** eseményt veti ki. Az Event Grid értesíti az összes előfizetőt az eseményről. A mi esetünkben az Azure Függvény az egyetlen előfizető. Az Azure függvény elemzi az esemény paramétereit, hogy melyik esemény történt. Ezután továbbítja a fájl URL-címét a Databricks feladat. A Databricks-feladat beolvassa a fájlt, és hozzáad egy sort a Databricks-különbözeti táblához, amely a tárfiókban található.

3. Annak ellenőrzéséhez, hogy a feladat sikeres volt-e, nyissa meg az adattéglák munkaterületét, kattintson a **Feladatok** gombra, majd nyissa meg a feladatot.

4. Válassza ki a feladatot a feladatoldal megnyitásához.

   ![Spark-feladat](./media/data-lake-storage-events/spark-job.png "Spark-feladat")

   Amikor a feladat befejeződik, megjelenik a befejezési állapot.

   ![Sikeresen befejeződött a feladat](./media/data-lake-storage-events/spark-job-completed.png "Sikeresen befejeződött a feladat")

5. Egy új munkafüzetcellában futtassa ezt a lekérdezést egy cellában a frissített különbözeti tábla megtekintéséhez.

   ```
   %sql select * from customer_data
   ```

   A visszaadott tábla a legutóbbi rekordot jeleníti meg.

   ![A legutóbbi rekord megjelenik a táblában](./media/data-lake-storage-events/final_query.png "A legutóbbi rekord megjelenik a táblában")

6. A rekord frissítéséhez hozzon `customer-order-update.csv`létre egy nevű fájlt, illessze be az alábbi adatokat a fájlba, és mentse a helyi számítógépre.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Ez a csv fájl majdnem megegyezik az előzővel, `228` kivéve, hogy a rendelés mennyisége a értékről a értékre `22`változik.

7. A Tárterületen töltse fel ezt a fájlt a tárfiók **bemeneti** mappájába.

8. Futtassa újra a `select` lekérdezést a frissített különbözeti tábla megtekintéséhez.

   ```
   %sql select * from customer_data
   ```

   A visszaadott tábla a frissített rekordot mutatja.

   ![A frissített rekord megjelenik a táblában](./media/data-lake-storage-events/final_query-2.png "A frissített rekord megjelenik a táblában")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje az erőforráscsoportot és az összes kapcsolódó erőforrást. Ehhez jelölje ki a tárfiók erőforráscsoportját, és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Reagálás Blob Storage-eseményekre](storage-blob-event-overview.md)

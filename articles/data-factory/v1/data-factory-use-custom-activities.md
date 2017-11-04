---
title: "Egyéni tevékenységek használata Azure Data Factory-folyamatban"
description: "Megtudhatja, hogyan hozzon létre egyéni tevékenységeket, és használja őket az Azure Data Factory-folyamathoz."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 0794952fdfbcc49cc66273be2d46484014ae1677
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Egyéni tevékenységek használata Azure Data Factory-folyamatban
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](data-factory-use-custom-activities.md)
> * [2. verzió – Előzetes verzió](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [egyéni tevékenységeket a V2](../transform-data-using-dotnet-custom-activity.md).

Egy Azure Data Factory-folyamathoz használható tevékenységeknek két típusa van.

- [Adatok mozgása tevékenységek](data-factory-data-movement-activities.md) közötti áthelyezése [támogatott forrás és a fogadó adattárolókhoz](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Adatok átalakítása tevékenységek](data-factory-data-transformation-activities.md) adatok átalakítására a számítási szolgáltatásokat, például Azure HDInsight, az Azure Batch és az Azure Machine Learning segítségével. 

Adatok áthelyezése az adat-előállító nem támogatja az adattárat, hozzon létre egy **egyéni tevékenység** a saját adatok mozgása logika és használja a tevékenység egy folyamatot. Hasonlóképpen átalakító/folyamat számára adatokat úgy, hogy a Data Factory nem támogatja, egyéni tevékenység létrehozása a saját adatok átalakítása logikával, és használja a tevékenységet egy folyamaton belül. 

Konfigurálhat egy egyéni tevékenység segítségével futtatja egy **Azure Batch** készlete virtuális gépek vagy a Windows-alapú **Azure HDInsight** fürt. Azure Batch használatakor csak egy meglévő Azure Batch-készlet is használhatja. Mivel a HDInsight használata esetén használható egy meglévő HDInsight-fürtre vagy olyan fürt, amely automatikusan jön létre, igény szerinti futásidőben.  

A következő forgatókönyv részletesen bemutatja egy egyéni .NET tevékenység létrehozásáért és az egyéni tevékenység egy folyamaton belül. A forgatókönyv egy **Azure Batch** társított szolgáltatás. Egy Azure HDInsight használata társított szolgáltatás helyette, típusú társított szolgáltatás létrehozása **HDInsight** (egyéni HDInsight-fürt) vagy **HDInsightOnDemand** (Data Factory létrehoz egy HDInsight-fürt igény szerinti). Ezt követően konfigurálja a kapcsolódó HDInsight szolgáltatásokat egyéni tevékenység. Lásd: [használata Azure HDInsight összekapcsolt szolgáltatások](#use-hdinsight-compute-service) című szakaszban talál információt az egyéni tevékenység futtatásához Azure HDInsight eszközzel.

> [!IMPORTANT]
> - Az egyéni .NET-tevékenységek futtatásához csak a Windows-alapú HDInsight-fürtökön. Ez a korlátozás a megoldás, hogy a térkép csökkentése tevékenység használja egyéni Java-kódot futtathatnak egy Linux-alapú HDInsight-fürtöt. Egy másik lehetőség, hogy a virtuális gépek Azure Batch-készlet használja egy HDInsight-fürt használata helyett egyéni tevékenységek futtatásához.
> - Nincs lehetőség egyéni tevékenység az adatkezelési átjárót a helyszíni adatforrások eléréséhez használható. Jelenleg [az adatkezelési átjáró](data-factory-data-management-gateway.md) csak a másolási tevékenység és a tárolt eljárási tevékenység támogatja az adat-előállítóban.   

## <a name="walkthrough-create-a-custom-activity"></a>Forgatókönyv: egyéni tevékenység létrehozása
### <a name="prerequisites"></a>Előfeltételek
* A Visual Studio 2012/2013 vagy 2015
* Az [Azure .NET SDK](https://azure.microsoft.com/downloads/) letöltése és telepítése.

### <a name="azure-batch-prerequisites"></a>Azure Batch-Előfeltételek
A forgatókönyv futtatása az egyéni .NET-tevékenységek használata az Azure Batch számítási erőforrásként. **Az Azure Batch** van egy platform szolgáltatás a felügyeleti teendők központjaként párhuzamosan futó és nagy teljesítményű számítástechnikai (HPC) alkalmazások hatékonyan a felhőben. Az Azure Batch ütemezi a számítási igényű munkát egy felügyelt futtathatnak **virtuális gépek**, és képes automatikusan méretezési számítási erőforrásokat a feladatok igényeinek. Lásd: [Azure Batch alapjai] [ batch-technical-overview] cikk részletes áttekintés az Azure Batch szolgáltatás.

Az oktatóanyag az Azure Batch-fiók létrehozása virtuális gépek készletét. A lépések a következők:

1. Hozzon létre egy **Azure Batch-fiók** használatával a [Azure-portálon](http://portal.azure.com). Lásd: [létrehozása és kezelése az Azure Batch-fiók] [ batch-create-account] miként.
2. Jegyezze fel az Azure Batch-fiók neve, fiókkulcs, URI és az alkalmazáskészlet neve. Már szükség csatolt Azure Batch szolgáltatás létrehozása.
    1. A kezdőlapon Azure Batch-fiókhoz, megjelenik egy **URL-cím** a következő formátumban: `https://myaccount.westus.batch.azure.com`. Ebben a példában **myaccount** az Azure Batch-fiók neve. A társított szolgáltatás definíciójának segítségével URI megadása nélkül a fiók nevét az URL-címet. Például: `https://<region>.batch.azure.com`.
    2. Kattintson a **kulcsok** a bal oldali menüben, és másolja a **elsődleges ELÉRÉSI kulcsot**.
    3. Egy létező alkalmazáskészlet használatához kattintson **készletek** a menüben, és jegyezze fel a **azonosító** a készlet. Ha egy meglévő készlet nem rendelkezik, helyezze át a következő lépéssel.     
2. Hozzon létre egy **Azure Batch-készlet**.

   1. Az a [Azure-portálon](https://portal.azure.com), kattintson a **Tallózás** a bal oldali menüben, majd kattintson a **Batch-fiókok**.
   2. Válassza ki az Azure Batch-fiók megnyitása a **Batch-fiók** panelen.
   3. Kattintson a **készletek** csempére.
   4. Az a **készletek** panelen kattintson a Hozzáadás gombra az eszköztáron a készlet hozzáadása gombra.
      1. Adja meg a készlet (alkalmazáskészlet azonosítója) Azonosítóját. Megjegyzés: a **a készlet Azonosítóját**; kell azt a Data Factory megoldás létrehozásakor.
      2. Adja meg **Windows Server 2012 R2** az operációsrendszer-családot beállítás.
      3. Válassza ki a **csomóponti tarifacsomagot**.
      4. Adja meg **2** , értékét a **cél dedikált** beállítást.
      5. Adja meg **2** , értékét a **csomópontonkénti tevékenységek maximális** beállítást.
   5. A készlet létrehozásához kattintson az **OK** gombra.
   6. Jegyezze fel a **azonosító** a készlet. 



### <a name="high-level-steps"></a>Magas szintű lépései
Ez a forgatókönyv részeként elvégezhető két magas szintű lépései a következők: 

1. Egyszerű adat-átalakítási/feldolgozó logika tartalmazó egyéni tevékenység létrehozása.
2. Hozzon létre egy Azure data factory egy folyamatot, amely az egyéni tevékenység használja.

### <a name="create-a-custom-activity"></a>Egyéni tevékenység létrehozása
.NET egyéni tevékenység, hozzon létre egy **.NET Class Library** egy osztály, amely megvalósítja az, hogy a projekt **IDotNetActivity** felületet. Ez az interfész csak egy metódusa: [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) , és az aláírása:

```csharp
public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
```


A metódus négy paramétereket fogadja:

- **linkedServices**. Ez a tulajdonság egy-egy adattároló kapcsolódó szolgáltatások bemeneti/kimeneti adatkészletek a tevékenység által hivatkozott enumerálható lista.   
- **adatkészletek**. Ez a tulajdonság egy bemeneti/kimeneti adatkészletek tevékenység enumerálható listája. A helyek és a bemeneti és kimeneti adatkészletek által megadott sémák használhatja ezt a paramétert.
- **tevékenység**. Ez a tulajdonság adja meg az aktuális tevékenység. Az egyéni tevékenység társított kiterjesztett tulajdonságok eléréséhez használható. Lásd: [további tulajdonságok hozzáférés](#access-extended-properties) részleteiről.
- **naplózó**. Ez az objektum lehetővé teszi a felhasználó napló a következő feldolgozási sor az adott felület hibakeresési megjegyzések írását.

A metódus visszaadja a szótár részére láncolni egyéni tevékenységek együtt a jövőben használható. Ez a funkció még nem használható, így egy üres szótár visszaadásának metódus.  

### <a name="procedure"></a>Eljárás
1. Hozzon létre egy **.NET Class Library** projekt.
   <ol type="a">
     <li>Indítsa el <b>Visual Studio 2017</b> vagy <b>Visual Studio 2015-öt</b> vagy <b>Visual Studio 2013</b> vagy <b>Visual Studio 2012</b>.</li>
     <li>Kattintson a <b>File</b> (Fájl) menüre, mutasson a <b>New</b> (Új) elemre, és kattintson a <b>Project</b> (Projekt) lehetőségre.</li>
     <li>Bontsa ki a <b>Sablonok</b> lehetőséget, és válassza a <b>Visual C#</b> lehetőséget. Ebben a bemutatóban használhat C#, de bármilyen .NET nyelvi használhatja az egyéni tevékenység fejlesztéséhez.</li>
     <li>Válassza ki <b>Class Library</b> a jobb oldali projekttípusok közül. VS 2017, válassza a <b>Class Library (.NET-keretrendszer)</b> </li>
     <li>Adja meg <b>MyDotNetActivity</b> a a <b>neve</b>.</li>
     <li>Válassza ki <b>C:\ADFGetStarted</b> a a <b>hely</b>.</li>
     <li>A projekt létrehozásához kattintson az <b>OK</b> gombra.</li>
   </ol>
2.Kattintson a **eszközök**, mutasson a **NuGet-Csomagkezelő**, és kattintson a **Csomagkezelő konzol**.
3. A Package Manager Console hajtható végre a következő parancs futtatásával importálja **Microsoft.Azure.Management.DataFactories**.

    ```PowerShell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importálás a **Azure Storage** NuGet-csomagot a projekthez.

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Data Factory szolgáltatás indítója windowsazure.Storage kifejezésre 4.3 verziója szükséges. Ha Azure Storage szerelvény újabb verziójára történő hivatkozás az egyéni tevékenység projektben, hibaüzenet jelenik meg a tevékenység végrehajtásakor. A hiba elhárításához lásd: [Appdomain elkülönítési](#appdomain-isolation) szakasz. 
5. Adja hozzá a következő **használatával** utasítást, hogy a forrásfájl, a projektben.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Módosítsa a nevét a **névtér** való **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Változtassa meg az osztály nevét **MyDotNetActivity** és a Származtatás a **IDotNetActivity** csatoló, ahogy az a következő kódrészletet:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Megvalósítása (Hozzáadás) a **Execute** metódusában a **IDotNetActivity** a csatoló a **MyDotNetActivity** osztályhoz, és másolja az alábbi példakód a metódust.

    Az alábbi minta előfordulási a keresési kifejezés ("Microsoft") található, az minden egyes blob egy adatszelet társított.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    
    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];
    
        // to log information, use the logger object
        // log all extended properties            
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }
    
        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;
        
        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset 
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);
    
        // get the first Azure Storate linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;
    
        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;
    
        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.
    
        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
        // initialize the continuation token before using it in the do-while loop.
        BlobContinuationToken continuationToken = null;
        do
        {   // get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                     true,
                                     BlobListingDetails.Metadata,
                                     null,
                                     continuationToken,
                                     null,
                                     null);
    
            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
               // with the data slice. definition of the method is shown in the next step.
    
            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
        } while (continuationToken != null);
    
        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;
    
        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path   
        logger.Write("Writing blob to the folder: {0}", folderPath);
    
        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);
    
        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.  
    
        return new Dictionary<string, string>();
    }
    ```
9. Adja hozzá a következő segédmódszereket: 

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    
    private static string GetFolderPath(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset   
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        // return the folder path found in the type properties
        return blobDataset.FolderPath;
    }
    
    /// <summary>
    /// Gets the fileName value from the input/output dataset.   
    /// </summary>
    
    private static string GetFileName(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }
    
        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }
    
        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>
    
    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
        string output = string.Empty;
        logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
        foreach (IListBlobItem listBlobItem in Bresult.Results)
        {
            CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
            if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
            {
                string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                logger.Write("input blob text: {0}", blobText);
                string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                var matchQuery = from word in source
                                 where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                 select word;
                int wordCount = matchQuery.Count();
                output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
            }
        }
        return output;
    }
    ```

    A GetFolderPath metódus visszaadja az elérési út a mappába, amely a DataSet adatkészlet mutat, és a GetFileName metódus a/fájlját, hogy az adatkészlet nevét adja vissza. Ha Ön havefolderPath meghatározása változókkal például {Year}, {Month}, {Day} stb., a módszer eloszlás, ez a karakterlánc nem törli őket futásidejű értékekkel. Lásd: [további tulajdonságok hozzáférés](#access-extended-properties) című szakaszban talál információt elérése SliceStart, SliceEnd, stb.    

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    A Calculate metódus kulcsszó a bemeneti fájlok (a mappában található a bináris objektumok) a Microsoft-példányok száma számítja ki. A keresési kifejezés ("Microsoft") nem változtatható a kódban.
10. Fordítsa le a projektet. Kattintson a **Build** a menüre, majd a **megoldás fordítása**.

    > [!IMPORTANT]
    > 4.5.2-es verzió .NET-keretrendszer célkeretrendszerként a projekthez: kattintson a jobb gombbal a projektre, majd kattintson **tulajdonságok** a megcélzott keretrendszer beállításához. Adat-előállító nem támogatja a lefordított elleni .NET-keretrendszer verziója 4.5.2 később egyéni tevékenységeket.

11. Indítsa el **Windows Explorer**, és navigáljon a **bin\debug** vagy **bin\release** mappa build típusától függően.
12. Hozzon létre egy zip fájlt **MyDotNetActivity.zip** , amely tartalmazza a bináris fájlok a <project folder>\bin\Debug mappába. Tartalmazza a **MyDotNetActivity.pdb** fájlt úgy, hogy be további részletekről, mint a sor száma, amelyek a probléma oka, hogy hiba történt a forráskód. 

    > [!IMPORTANT]
    > Az egyéni tevékenység zip-fájljában lévő összes fájlnak a **legfelső szinten** kell lennie, almappák nélkül.

    ![Bináris kimeneti fájlok](./media/data-factory-use-custom-activities/Binaries.png)
14. Hozzon létre egy blob-tároló nevű **customactivitycontainer** Ha még nem létezik. 
15. A customactivitycontainer egy blobot, töltse fel a MyDotNetActivity.zip egy **általános célú** AzureStorageLinkedService által hivatkozott Azure blob storage (nem közbeni/ritkán Blob-tároló).  

> [!IMPORTANT]
> Ha a .NET tevékenység projekt hozzáadása egy megoldást a Visual Studio Data Factory projektet tartalmaz, és adjon hozzá egy hivatkozást a Data Factory-alkalmazás projekt .NET tevékenység projekthez, nem kell hajtsa végre az utolsó két lépést a zip manuális létrehozása fájl, majd ismét feltölteni az általános célú Azure blob storage. Ha közzéteszi a Visual Studio használatával a Data Factory entitások, ezeket a lépéseket automatikusan történik a közzétételi folyamat. További információkért lásd: [adat-előállító projektre a Visual Studio](#data-factory-project-in-visual-studio) szakasz.

## <a name="create-a-pipeline-with-custom-activity"></a>Hozzon létre egy folyamatot egyéni tevékenység
Egyéni tevékenység létrehozása és egy blob-tárolóba, a bináris fájljait a zip-fájl feltöltése a **általános célú** Azure Storage-fiókot. Ebben a szakaszban hoz létre egy Azure data factory egy folyamatot, amely az egyéni tevékenység használja.

Az egyéni tevékenység bemeneti adatkészletet blobok (fájlok) a customactivityinput mappában a blob Storage tárolóban adftutorial tároló jelöli. A kimeneti adatkészlet a tevékenység kimeneti BLOB adftutorial tároló a blob Storage tárolóban customactivityoutput mappában jelöli.

Hozzon létre **file.txt** a következő tartalommal rendelkező fájlt, és töltse fel a **customactivityinput** mappában található a **adftutorial** tároló. A adftutorial tároló létrehozása, ha még nem létezik. 

```
test custom activity Microsoft test custom activity Microsoft
```

A bemeneti mappa felel meg az Azure Data Factory szelet akkor is, ha a mappa két vagy több fájlt. A folyamat minden szelet feldolgozása után az egyéni tevékenység az, hogy a szelet bemeneti mappa összes blobjának telepítéseket.

Egy kimeneti fájlt a adftutorial\customactivityoutput mappában (azonos számú blobot a bemeneti mappában) egy vagy több sort tartalmazó jelenik meg:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


A jelen szakaszban végrehajtandó lépések a következők:

1. Hozzon létre egy **adat-előállító**.
2. Hozzon létre **összekapcsolt szolgáltatások** a virtuális gépek az Azure Batch-készlet, amely az egyéni tevékenység fut, és az Azure Storage a bemeneti/kimeneti BLOB tároló.
3. Hozzon létre a bemeneti és kimeneti **adatkészletek** , amelyek bemeneti és az egyéni tevékenység jelölik.
4. Hozzon létre egy **csővezeték** , amely használja az egyéni tevékenység.

> [!NOTE]
> Hozzon létre a **file.txt** és feltöltése egy blob-tárolóba, ha még nem tette meg. Lásd az előző részben található útmutatást.   

### <a name="step-1-create-the-data-factory"></a>1. lépés: Az adat-előállító létrehozása
1. Az Azure-portálon való bejelentkezés után hajtsa végre az alábbi lépéseket:
   1. A bal oldali menüben kattintson a **NEW** (ÚJ) elemre.
   2. Kattintson a **adatok + analitika** a a **új** panelen.
   3. Kattintson a **Data Factory** elemre a **Data analytics** (Adatelemzés) panelen.
   
    ![Új Azure Data Factory menü](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. Az a **új adat-előállító** panelen adjon meg **CustomActivityFactory** nevét. Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a hibaüzenetet kapja: **nem érhető el adat-előállító "CustomActivityFactory"**, változtassa meg a data factory nevét (például **yournameCustomActivityFactory**), és próbálja meg újra létrehozni.

    ![Új Azure Data Factory panel](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Kattintson a **ERŐFORRÁSCSOPORT-név**, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot.
4. Ellenőrizze, hogy használja a megfelelő **előfizetés** és **régió** hol szeretné létrehozni az adat-előállítóban.
5. Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.
6. Megjelenik a data factory létrehozása a **irányítópult** az Azure portálról.
7. Után az adat-előállító létrehozása sikerült, tekintse meg a Data Factory panel, amelyen az adat-előállítóban tartalmát jeleníti meg.
    
    ![A Data Factory panel](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>2. lépés: A társított szolgáltatások létrehozásához
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. Ebben a lépésben kapcsolhat az Azure Storage-fiók és az Azure Batch-fiók a data factory.

#### <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása
1. Kattintson a **Szerző és központi telepítése** csempét a **adat-előállító** paneljén **CustomActivityFactory**. A Data Factory Editor láthatja.
2. Kattintson a **az új adattároló** a parancs megnyitásához, és válassza a **az Azure storage**. A szerkesztőben megjelenik az Azure Storage társított szolgáltatás létrehozására szolgáló JSON-parancsfájl.
    
    ![Az új adattároló - Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Cserélje le `<accountname>` az Azure storage-fiók nevére és `<accountkey>` az Azure storage-fiókjának elérési kulcsával. A tárelérési kulcs lekérésével kapcsolatos információk: [Tárelérési kulcsok megtekintése, másolása és újragenerálása](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

    ![Az Azure Storage szolgáltatás tetszését](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére.

#### <a name="create-azure-batch-linked-service"></a>Csatolt Azure Batch szolgáltatás létrehozása
1. Kattintson a Data Factory Editor **... További** a parancssávon kattintson **új számítási**, majd válassza ki **Azure Batch** a menüből.

    ![Új számítási - Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. A következő módosításokat a JSON-parancsfájlba:

   1. Adja meg az Azure Batch-fiók nevét a **accountName** tulajdonság. A **URL-cím** a a **Azure Batch-fiók panelen** a következő formátumban: `http://accountname.region.batch.azure.com`. Az a **batchUri** tulajdonság a JSON-ban, el kell távolítania `accountname.` az URL-címet, és használja a `accountname` a a `accountName` JSON tulajdonság.
   2. Adja meg a szükséges Azure Batch-fiók kulcsot a **accessKey** tulajdonság.
   3. Adja meg a létrehozott alkalmazáskészlet nevét előfeltételei részeként a **poolName** tulajdonság. A készlet neve helyett a készlet Azonosítóját is megadható.
   4. Adja meg az Azure Batch URI Azonosítót a **batchUri** tulajdonság. Példa: `https://westus.batch.azure.com`.  
   5. Adja meg a **AzureStorageLinkedService** a a **linkedServiceName** tulajdonság.

        ```json
        {
         "name": "AzureBatchLinkedService",
         "properties": {
           "type": "AzureBatch",
           "typeProperties": {
             "accountName": "myazurebatchaccount",
             "batchUri": "https://westus.batch.azure.com",
             "accessKey": "<yourbatchaccountkey>",
             "poolName": "myazurebatchpool",
             "linkedServiceName": "AzureStorageLinkedService"
           }
         }
        }
        ```

       Az a **poolName** tulajdonság, azt is megadhatja a tárolókészlet neve helyett a készlet Azonosítóját.

      > [!IMPORTANT]
      > A Data Factory szolgáltatásnak nem támogatja egy igény szerinti lehetőséget az Azure hdinsight azonban nem. Csak a saját Azure Batch-készlet használhatja az Azure data factory.   
    

### <a name="step-3-create-datasets"></a>3. lépés: Adatkészletek létrehozása
Ebben a lépésben hoz létre a bemeneti és kimeneti adatok adatkészletek.

#### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása
1. A Data Factory **szerkesztőjében** kattintson a **... További** a parancssávon kattintson **új adatkészlet**, majd válassza ki **Azure Blob Storage tárolóban** a legördülő menüből.
2. A jobb oldali JSON cserélje le a következő JSON kódrészletet:

    ```json
    {
     "name": "InputDataset",
     "properties": {
         "type": "AzureBlob",
         "linkedServiceName": "AzureStorageLinkedService",
         "typeProperties": {
             "folderPath": "adftutorial/customactivityinput/",
             "format": {
                 "type": "TextFormat"
             }
         },
         "availability": {
             "frequency": "Hour",
             "interval": 1
         },
         "external": true,
         "policy": {}
     }
    }
    ```

   Ez a forgatókönyv a kezdő időpont későbbi részében hozzon létre egy folyamatot: 2016-11-16T00:00:00Z és záró idő: 2016-11-16T05:00:00Z. Ütemezett eredményezett adatokat óránként, így öt bemeneti/kimeneti szeletek (közötti **00**: 00:00 -> **05**: 00:00).

   A **gyakoriság** és **időköz** az bemeneti adatkészlet állítsa **óra** és **1**, ami azt jelenti, hogy a bemeneti szelet elérhető óránként. Az ebben a példában a fájl (file.txt) a intputfolder is.

   Az alábbiakban a minden szelet, amely a fenti JSON-részlet SliceStart rendszer változó által képviselt kezdési idejét.
3. Kattintson a **telepítés** létrehozása és telepítése az eszköztáron a **InputDataset**. Győződjön meg arról, hogy a szerkesztő címsorában megjelenik a **TABLE CREATED SUCCESSFULLY** (A TÁBLA SIKERESEN LÉTREJÖTT) üzenet.

#### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
1. Az a **Data Factory editor**, kattintson a **... További** a parancssávon kattintson **új adatkészlet**, majd válassza ki **Azure Blob Storage tárolóban**.
2. A JSON-parancsfájl, a jobb oldali cserélje le a következő JSON-parancsfájl:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
                "partitionedBy": [
                    {
                        "name": "slice",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy-MM-dd-HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

     Kimeneti hely **adftutorial/customactivityoutput/** és kimeneti fájlnév: éééé-HH-NN-HH.txt, ha éééé-hh-nn ÓÓ az év, hónap, dátum és az éppen létrehozott szelet óra. Lásd: [fejlesztői leírás] [ adf-developer-reference] részleteiről.

    Egy kimeneti blob/fájl az egyes bemeneti szeletek jön létre. Ez hogyan kimeneti fájl neve az egyes szeletek. A kimeneti fájlok akkor jönnek létre, egy kimeneti mappában: **adftutorial\customactivityoutput**.

   | Szelet | Kezdési idő | Kimeneti fájlja |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Ne feledje, hogy egy bemeneti mappában lévő összes fájlt a fent említett kezdési idő feltüntetésével szelet részét. A szelet feldolgozása után az egyéni tevékenység minden fájl megvizsgálja, és hozza létre a keresési kifejezés ("Microsoft") előfordulásainak száma a kimeneti fájl egy sorban. Ha a inputfolder három fájlok, vannak-e három sort a kimeneti fájl az egyes óránkénti szeletek: 2016-11-16:01:00:00.txt 2016-11-16-00.txt, stb.
3. Központi telepítése a **OutputDataset**, kattintson a **telepítés** a parancssávon.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Hozzon létre és futtasson egy folyamatot, amely használja az egyéni tevékenység
1. Kattintson a Data Factory Editor **... További**, majd válassza ki **új adatcsatorna** a parancssávon. 
2. A jobb oldali JSON cserélje le a következő JSON-parancsfájl:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Vegye figyelembe a következő szempontokat:

   * **Egyidejűségi** értéke **2** , hogy két szeletek párhuzamosan dolgozza fel az Azure Batch-készletben 2 virtuális gép.
   * Egy tevékenység szerepel a tevékenységek szakasz és a típusa: **DotNetActivity**.
   * **AssemblyName** értéke a dll-fájl neve: **MyDotnetActivity.dll**.
   * **EntryPoint** értéke **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** értéke **AzureStorageLinkedService** a blob-tároló, amely tartalmazza az egyéni tevékenység zip-fájl mutat. Különböző Azure Storage-fiókok használatakor a bemeneti/kimeneti fájlok és az egyéni tevékenység zip-fájlt hoz létre egy másik Azure tárolás társított szolgáltatása. Ez a cikk feltételezi, hogy a azonos Azure Storage-fiókot használja.
   * **PackageFile** értéke **customactivitycontainer/MyDotNetActivity.zip**. A a formátumban: containerforthezip/nameofthezip.zip.
   * Az egyéni tevékenység veszi **InputDataset** bemenetként és **OutputDataset** output típusúként.
   * A linkedServiceName tulajdonságot az egyéni tevékenység mutat a **AzureBatchLinkedService**, amely közli az Azure Data Factory, amely az egyéni tevékenység az Azure Batch virtuális gépek futtatásához szükséges.
   * **isPaused** tulajdonsága **hamis** alapértelmezés szerint. A folyamat a rendszer azonnal futtatja ebben a példában a szeletek indítsa el a régebbi, mert. Ez a tulajdonság igaz értékre a feldolgozási sor szüneteltetése, és állítsa vissza újraindítására hamis beállíthatja.
   * A **start** idő és **end** idők **öt** egymástól óra és szeletek előállítása hourly, így öt szeletek hozzák létre a folyamatot.
3. Kattintson újra az adatcsatornát, **telepítés** a parancssávon.

### <a name="monitor-the-pipeline"></a>A folyamat figyelése
1. A Data Factory panelen az Azure portálon kattintson **Diagram**.

    ![Diagram csempe](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. A Diagram nézetben kattintson a OutputDataset.

    ![Diagramnézet](./media/data-factory-use-custom-activities/diagram.png)
3. Megtekintheti, hogy az öt kimeneti szeletek kész állapotban van-e. Ha nem üzemkész állapotban, hogy még nem készült még. 

   ![Kimeneti szeletek](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Győződjön meg arról, hogy a kimeneti fájlok akkor jönnek létre, a blob Storage tárolóban lévő a **adftutorial** tároló.

   ![egyéni tevékenység kimenetét][image-data-factory-ouput-from-custom-activity]
5. A kimeneti fájl megnyitásakor, a következő kimeneti hasonló kimenetnek kell megjelennie:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Használja a [Azure-portálon] [ azure-preview-portal] vagy az Azure PowerShell-parancsmagok segítségével figyelheti az adat-előállító, folyamatok és adatkészletek. Láthatja, hogy az üzenetek a **ActivityLogger** a naplók (kifejezetten felhasználói-0.log) tölthet le a portál vagy parancsmagok használatával az egyéni tevékenység a kódban.

   ![az egyéni tevékenység naplók letöltése][image-data-factory-download-logs-from-custom-activity]

Lásd: [figyelő és folyamatok kezelése](data-factory-monitor-manage-pipelines.md) adathalmazok és adatcsatornák figyelemmel kísérésére részletes leírást.      

## <a name="data-factory-project-in-visual-studio"></a>Data Factory projektre a Visual Studióban  
Hozzon létre, és tegye közzé a Data Factory entitásokat Visual Studio használatával Azure portál használata helyett. Részletes információkat létrehozása és közzététele a Data Factory entitások Visual Studio használatával, lásd: [felépítheti első folyamatát Visual Studio használatával](data-factory-build-your-first-pipeline-using-vs.md) és [adatok másolása az Azure Blob az Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) cikkek.

Adat-előállító projekt létrehozása a Visual Studio, hajtsa végre az alábbi kiegészítő lépéseket:
 
1. Adja hozzá a Data Factory projektet a Visual Studio megoldás, amely az egyéni tevékenység projektet tartalmaz. 
2. Adjon hozzá egy hivatkozást a .NET tevékenység projektre a Data Factory projektből. Kattintson jobb gombbal a Data Factory projekt **Hozzáadás**, és kattintson a **hivatkozás**. 
3. Az a **hivatkozás hozzáadása** párbeszédpanelen jelölje ki a **MyDotNetActivity** projektre, majd kattintson az **OK**.
4. Hozza létre, és a megoldás közzététele.

    > [!IMPORTANT]
    > Ha közzéteszi a Data Factory entitások, automatikusan létrejön egy zip-fájlt, és blobtárolóba feltöltött: customactivitycontainer. Ha a blob-tároló nem létezik, automatikusan létrejön túl.  


## <a name="data-factory-and-batch-integration"></a>Adat-előállító és kötegelt integrációja
A Data Factory szolgáltatásnak hoz létre egy feladatot az Azure Batch nevű: **adf-poolname: feladat-xxx**. Kattintson a **feladatok** a bal oldali menüből. 

![Az Azure Data Factory - kötegelt feladatok](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Egy feladat minden egyes tevékenység futtatásához a szelet jön létre. Ha készen áll a feldolgozásra öt szeletek, ez a feladat öt feladatok jönnek létre. A Batch-készlet több számítási csomópontok szerepelnek, ha két vagy több szeletek párhuzamosan futtatható. Ha egyes számítási csomópontjain maximális feladatok értéke > 1, az azonos számítási futó egynél több szelet is lehet.

![Az Azure Data Factory - kötegelt munka feladatai](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

A következő ábra bemutatja az Azure Data Factory és kötegelt feladatok közötti kapcsolat.

![Adat-előállító & kötegelt](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Hibák elhárítása
Néhány alapvető technikák áll:

1. Ha hibaüzenet jelenik meg, előfordulhat, hogy használni egy gyakran használt adatok/ritkán blob-tároló egy általános célú Azure blob storage használata helyett. A zip-fájlt feltölteni egy **általános célú Azure Storage-fiók**. 
 
    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ``` 
2. Ha a következő hibát látja, ellenőrizze, hogy az osztály a CS-fájl neve megegyezik a megadott név a **EntryPoint** az adatcsatorna JSON tulajdonság. A forgatókönyv osztály neve van: MyDotNetActivity, illetve a belépési pont a JSON-ban: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Ha felelnek meg, ellenőrizze, hogy a bináris fájlok a a **gyökérmappa** zip-fájlban. Ez azt jelenti, hogy a zip-fájl megnyitásakor, megtekintheti az összes fájl a gyökérmappában található, és nem minden almappa.   
3. Ha nincs beállítva a bemeneti szelet **készen**, győződjön meg arról, hogy helyesen-e a bemeneti gyökérmappa-szerkezetében és **file.txt** szerepel a bemeneti mappákat.
3. Az a **Execute** metódus az egyéni tevékenység, használja a **IActivityLogger** objektum, amely segít a problémák elhárításához adatok naplózására. A felhasználó naplófájlok megjelennek a naplózott üzeneteket (egy vagy több fájlt nevű: felhasználó-0.log, felhasználó-1.log, felhasználó-2.log, stb.).

   Az a **OutputDataset** panelen a szelet megtekintéséhez kattintson a **ADATSZELET** , hogy a szelet paneljét. Látni **tevékenységek** az adott szeletek. Meg kell jelennie egy tevékenység Futtatás újrapróbálása. Kattintson a Futtatás parancsra a parancssávon, ha egy másik, azonos szeletre vonatkozó tevékenységfuttatási is elindítható.

   Ha a tevékenység futtatása gombra kattint, megjelenik a **tevékenység futtatása részletei** naplófájlok listáját tartalmazó panel. Megjelenik a user_0.log fájlban naplózott üzeneteket. Ha hiba történik, megjelenik az három tevékenység fut oka az újrapróbálkozások maximális számát a feldolgozási sor/tevékenységben JSON 3 értékre van beállítva. Amikor a tevékenység futtatása gombra kattint, láthatja a hiba elhárítása érdekében tekintse át a naplófájlokat.

   A naplófájlok, kattintson a **felhasználói-0.log**. A jobb oldali panelen a következők használatával eredményeit a **IActivityLogger.Write** metódust. Ha az összes üzenet nem látható, ellenőrizze, hogy van-e további naplófájlokat nevű: user_1.log, user_2.log stb. Ellenkező esetben a kód nem sikerült az utolsó üzenet bejelentkezést követően.

   Ezenkívül ellenőrizze **rendszer-0.log** rendszer hibaüzeneteket és kivételeket.
4. Tartalmazza a **PDB** , hogy a hiba részletei információkat, mint a zip-fájlban szereplő fájl **hívási verem** Ha hiba történik.
5. Az egyéni tevékenység zip-fájljában lévő összes fájlnak a **legfelső szinten** kell lennie, almappák nélkül.
6. Győződjön meg arról, hogy a **assemblyName** (MyDotNetActivity.dll) **entryPoint**(MyDotNetActivityNS.MyDotNetActivity) **packageFile** (customactivitycontainer / MyDotNetActivity.zip), és **packageLinkedService** (kell mutatnia a **általános célú**Azure blob-tároló, amely a zip-fájl tartalmazza) helyes az értékük értékre van beállítva.
7. Ha kijavított egy hibát, és újra fel szeretné dolgozni a szeletet, kattintson a jobb gombbal a szeletre az **OutputDataset** panelen, és kattintson a **Futtatás** parancsra.
8. Ha a következő hibát látja, használja az Azure Storage csomag verziója > 4.3.0. Data Factory szolgáltatás indítója windowsazure.Storage kifejezésre 4.3 verziója szükséges. Lásd: [Appdomain elkülönítési](#appdomain-isolation) résznél a munkahelyi körül, ha az Azure Storage szerelvény újabb verzióját kell használnia. 

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral, 
    ```

    Ha a 4.3.0 használható Azure Storage csomag verziója, távolítsa el a meglévő hivatkozást verzió > 4.3.0 az Azure Storage-csomaghoz. Ezután futtassa a következő parancsot a NuGet-Csomagkezelő konzolról. 

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    A projekt felépítése. A verzió > 4.3.0 Azure.Storage szerelvény a bin\Debug mappa törlése. Hozzon létre egy zip-fájl bináris fájljait és a PDB-fájl. Lecseréli a régi zip-fájl erre a blob-tárolóban (customactivitycontainer). Futtassa újból a szeletet, melyeknél nem sikerült (kattintson a jobb gombbal a szelet, majd kattintson a Futtatás).   
8. Az egyéni tevékenység nem használja a **app.config** fájlt a csomagból. Ezért ha a kódot a kapcsolati karakterláncok a konfigurációs fájlból olvassa be, nem működik futásidőben. Az ajánlott eljárás az Azure Batch használata esetén a titkos kulcsainak tárolásához egy **Azure KeyVault**, egyszerű tanúsítvány-alapú szolgáltatás használatával megvédheti a **keyvault**, és az Azure Batch tanúsítvány terjesztése készlet. A .NET egyéni tevékenysége ezután elérheti a titkos kulcsokat a kulcstartóból a futtatáskor. Ez a megoldás egy általános megoldás, és minden olyan titkos kulcsot, nem csak a kapcsolati karakterlánc típusú méretezheti.

   Van egy egyszerűbb megoldást (de nem ajánlott): hozhat létre egy **Azure SQL társított szolgáltatásnak** kapcsolatikarakterlánc-beállításokat, hozzon létre egy adatkészlet által használt társított szolgáltatás, valamint az adatkészlet láncolt, egy üres bemeneti adatkészlet a Egyéni .NET tevékenység. A társított szolgáltatás kapcsolati karakterlánc az egyéni tevékenység kódban érheti el.  

## <a name="update-custom-activity"></a>Egyéni tevékenység frissítése
Ha frissíti a kódot az egyéni tevékenység, összeállítani, és a blob Storage új bináris fájlokat tartalmazó zip-fájl feltöltése.

## <a name="appdomain-isolation"></a>Az alkalmazástartomány elkülönítési
Lásd: [Cross AppDomain minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) , amely bemutatja, hogyan hozzon létre egy egyéni tevékenységet, amely nem korlátozza a Data Factory indítója által használt szerelvény verzióra történő váltás (Példa: windowsazure.Storage kifejezésre v4.3.0, Newtonsoft.Json v6.0.x, stb.).

## <a name="access-extended-properties"></a>További tulajdonságok hozzáférés
A tevékenység JSON-ban, a következő mintában látható módon a kiterjesztett tulajdonságok deklarálhatnak:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```


A példában két további tulajdonságainak vannak: **SliceStart** és **DataFactoryName**. SliceStart értékét a SliceStart rendszerváltozó alapul. Lásd: [rendszerváltozók](data-factory-functions-variables.md) támogatott rendszerváltozók listáját. DataFactoryName értéke nem módosítható CustomActivityFactory.

További tulajdonságok a eléréséhez a **Execute** metódust, az alábbi kód hasonló használható kódot:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties                               
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Az Azure Batch automatikus skálázás
Az Azure Batch-készlet is létrehozhat **automatikus skálázás** szolgáltatás. Például létrehozhatja az azure batch-készlet 0 dedikált virtuális gépek és az automatikus skálázás képlet függőben lévő feladatok száma alapján. 

A minta képlet itt éri el a következő viselkedés: a készlet létrehozásakor 1 virtuális gép kezdődik. $PendingTasks metrika feladatok száma definiálja a futó + (aszinkron) aktív állapotban.  A képlet átlagos száma függőben lévő feladatok megkeresi az elmúlt 180 másodperc alatt, és ennek megfelelően állítja be TargetDedicated. Biztosítja, hogy TargetDedicated soha nem túllép 25 virtuális gépeket. Igen új feladatok nyújtják, készlet automatikusan növekszik és feladatok befejezését, mint a virtuális gépek válik a szabad egyenként és az automatikus skálázás zsugorítja a virtuális gépek. startingNumberOfVMs és maxNumberofVMs az igényeinek megfelelően kell beállítani.

Automatikus skálázás képlet:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Lásd: [automatikus méretezési számítási csomópontok az Azure Batch-készlet](../../batch/batch-automatic-scaling.md) részleteiről.

Ha az alkalmazáskészlet által használt alapértelmezett [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), a Batch szolgáltatás a virtuális gép előkészítése az egyéni tevékenység futtatása előtt 15 – 30 percet vehet igénybe.  Ha a készlet egy másik autoScaleEvaluationInterval használ, a Batch szolgáltatás autoScaleEvaluationInterval + 10 percet is beletelhet.

## <a name="use-hdinsight-compute-service"></a>HDInsight számítási szolgáltatás használata
A forgatókönyv Azure Batch számítási az egyéni tevékenység futtatásához használt. Is használhatja a saját Windows-alapú HDInsight-fürt vagy adat-előállító igény szerinti Windows-alapú HDInsight-fürtöt, és az egyéni tevékenység, futtassa a HDInsight-fürt rendelkezik. Az alábbiakban a magas szintű lépései a HDInsight-fürtöt használ.

> [!IMPORTANT]
> Az egyéni .NET-tevékenységek futtatásához csak a Windows-alapú HDInsight-fürtökön. Ez a korlátozás a megoldás, hogy a térkép csökkentése tevékenység használja egyéni Java-kódot futtathatnak egy Linux-alapú HDInsight-fürtöt. Egy másik lehetőség, hogy a virtuális gépek Azure Batch-készlet használja egy HDInsight-fürt használata helyett egyéni tevékenységek futtatásához.
 

1. Hozzon létre egy Azure HDInsight társított szolgáltatást.   
2. Használja a HDInsight társított szolgáltatás helyett **AzureBatchLinkedService** az adatcsatorna JSON-NÁ.

Ha azt szeretné, akkor a forgatókönyv teszteléséhez, módosítsa **start** és **end** alkalommal fordult elő a tölcsér, hogy az Azure HDInsight szolgáltatással a forgatókönyv teszteléséhez.

#### <a name="create-azure-hdinsight-linked-service"></a>Azure HDInsight társított szolgáltatás létrehozása
Az Azure Data Factory szolgáltatásnak az igény szerinti fürt létrehozását támogatja, és használja úgy eredményezett kimeneti adatokat bemenet feldolgozása. Használhatja a saját fürt azonos végrehajtásához. Igény szerinti HDInsight-fürt használata esetén a fürt minden egyes szeletre vonatkozó végrehajtásakor létrejön. Mivel a saját HDInsight-fürtöt használ, ha a szelet feldolgozása azonnal készen áll a fürt. Ezért igény-fürtöt használ, akkor előfordulhat, hogy nem jelennek meg kimeneti adatokat leggyorsabban saját fürt használata esetén.

> [!NOTE]
> Futásidőben a .NET tevékenység példánya fut. csak a HDInsight fürt; egy munkavégző csomópont több csomópont futtathatnak nem lehet méretezni. A HDInsight-fürt csomópontjai párhuzamosan .NET tevékenység több példánya is futtathatók.
>
>

##### <a name="to-use-an-on-demand-hdinsight-cluster"></a>Igény szerinti HDInsight-fürt használatára
1. Az a **Azure-portálon**, kattintson a **fejlesztés és üzembe helyezés** a Data Factory kezdőlapján.
2. Kattintson a Data Factory Editor **új számítási** a parancssávon, és válassza ki a **igény szerinti HDInsight-fürt** a menüből.
3. A következő módosításokat a JSON-parancsfájlba:

   1. Az a **nagyobbnak** tulajdonság, adja meg a HDInsight-fürt méretét.
   2. Az a **timeToLive** tulajdonság, adja meg, mennyi ideig az ügyfél üresjáratban lehet, mielőtt az törlődni fog.
   3. Az a **verzió** tulajdonság, adja meg a használni kívánt HDInsight-verzió. Ha kizárja a ezt a tulajdonságot, a legújabb verziót használja.  
   4. Az a **linkedServiceName**, adja meg **AzureStorageLinkedService**.

        ```JSON
        {
           "name": "HDInsightOnDemandLinkedService",
           "properties": {
               "type": "HDInsightOnDemand",
               "typeProperties": {
                   "clusterSize": 4,
                   "timeToLive": "00:05:00",
                   "osType": "Windows",
                   "linkedServiceName": "AzureStorageLinkedService",
               }
           }
        }
        ```

    > [!IMPORTANT]
    > Az egyéni .NET-tevékenységek futtatásához csak a Windows-alapú HDInsight-fürtökön. Ez a korlátozás a megoldás, hogy a térkép csökkentése tevékenység használja egyéni Java-kódot futtathatnak egy Linux-alapú HDInsight-fürtöt. Egy másik lehetőség, hogy a virtuális gépek Azure Batch-készlet használja egy HDInsight-fürt használata helyett egyéni tevékenységek futtatásához.

4. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére.

##### <a name="to-use-your-own-hdinsight-cluster"></a>A saját HDInsight-fürt használatára:
1. Az a **Azure-portálon**, kattintson a **fejlesztés és üzembe helyezés** a Data Factory kezdőlapján.
2. Az a **Data Factory Editor**, kattintson a **új számítási** a parancssávon, és válassza ki a **HDInsight-fürt** a menüből.
3. A következő módosításokat a JSON-parancsfájlba:

   1. Az a **clusterUri** tulajdonság, a HDInsight meg az URL-CÍMÉT. Például: https://<clustername>.azurehdinsight.net/     
   2. Az a **felhasználónév** tulajdonság, írja be a felhasználónevet, aki hozzáférhet a HDInsight-fürthöz.
   3. Az a **jelszó** tulajdonság, írja be a felhasználó jelszavát.
   4. Az a **LinkedServiceName** tulajdonság, adja meg **AzureStorageLinkedService**.
4. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére.

Lásd: [összekapcsolt szolgáltatások számítási](data-factory-compute-linked-services.md) részleteiről.

Az a **JSON-feldolgozási folyamat**, HDInsight használata (igény vagy a saját) társított szolgáltatáshoz:

```JSON
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "HDInsightOnDemandLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00Z",
    "end": "2016-11-16T05:00:00Z",
    "isPaused": false
  }
}
```

## <a name="create-a-custom-activity-by-using-net-sdk"></a>Egyéni tevékenység létrehozása .NET SDK használatával
A forgatókönyv ebben a cikkben akkor hozzon létre egy adat-előállító egy folyamatot, amely az egyéni tevékenység használ az Azure-portál használatával. A következő kód bemutatja, hogyan helyette .NET SDK használatával a data factory létrehozása. Adatcsatornák programozott módon létrehozásához SDK használatával kapcsolatos további részleteket megtalálja a [.NET API használatával hozzon létre egy folyamatot másolási tevékenység](data-factory-copy-activity-tutorial-using-dotnet-api.md) cikk. 

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>A Visual Studio egyéni tevékenység hibakeresése
A [Azure Data Factory - helyi környezetben](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) mintát a Githubon tartalmaz olyan eszköz, amely lehetővé teszi a Visual Studio .NET tevékenységét egyéni hibakereséséhez.  


## <a name="sample-custom-activities-on-github"></a>Egyéni tevékenységek mintát a Githubon
| Minta | Milyen egyéni tevékenység does |
| --- | --- |
| [HTTP-adatok letöltő](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). |Letölti a adatokat a HTTP-végponttal Azure Blob Storage használatával egyéni tevékenység C# adat-előállítóban. |
| [Twitter véleményeket elemzési minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Meghívja az Azure ML modellje és do véleményeket elemzése, pontozási, előrejelzés stb. |
| [R-parancsfájl futtatása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). |Meghívja az R-parancsfájl RScript.exe futtatásával a HDInsight-fürtre, amelyen már a R telepítve van rajta. |
| [Kereszt-AppDomain .NET tevékenység](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Használja azokat, használja a Data Factory indítója szerelvény különböző verziójú |
| [Újból feldolgozza a modellt az Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Feldolgozza a modellt az Azure Analysis Servicesben. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

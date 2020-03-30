---
title: Egyéni tevékenységek használata Azure Data Factory-folyamatban
description: Ismerje meg, hogyan hozhat létre egyéni tevékenységeket, és hogyan használhatja őket egy Azure Data Factory-folyamatban.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 54cb06f1c77ab68818d8531b57d6eb936deda8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265726"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Egyéni tevékenységek használata Azure Data Factory-folyamatban
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-use-custom-activities.md)
> * [2-es verzió (aktuális verzió)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az Egyéni tevékenységek a V2 alkalmazásban című [témakört.](../transform-data-using-dotnet-custom-activity.md)

Az Azure Data Factory-folyamatban kétféle tevékenységet használhat.

- [Adatmozgatási tevékenységek](data-factory-data-movement-activities.md) az adatok [támogatott forrás- és fogadóadattárak közötti áthelyezéséhez.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [Adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) az adatok átalakításához számítási szolgáltatások, például az Azure HDInsight, az Azure Batch és az Azure Machine Learning használatával.

Ha olyan adattárba szeretne adatokat áthelyezni, amelyet a Data Factory nem támogat, hozzon létre egy **egyéni tevékenységet** a saját adatmozgatási logikájával, és használja a tevékenységet egy folyamatban. Hasonlóképpen az adatok olyan módon történő átalakításához/feldolgozásához, amelyet a Data Factory nem támogat, hozzon létre egy egyéni tevékenységet a saját adatátalakítási logikájával, és használja a tevékenységet egy folyamatban.

Konfigurálhatja az egyéni tevékenység fut egy **Azure Batch-készlet** virtuális gépek. Az Azure Batch használata esetén csak egy meglévő Azure Batch-készlet et használhat.

Az alábbi forgatókönyv lépésenként ismerteti az egyéni .NET-tevékenység létrehozását és az egyéni tevékenység folyamatközbeni használatát. A forgatókönyv egy **Azure Batch-kapcsolt** szolgáltatást használ.

> [!IMPORTANT]
> - Egyéni tevékenységből származó adatkezelési átjáró nem használható a helyszíni adatforrások eléréséhez. Az [Adatkezelési átjáró](data-factory-data-management-gateway.md) jelenleg csak a data factory másolási és tárolt eljárási tevékenységét támogatja.

## <a name="walkthrough-create-a-custom-activity"></a>Forgatókönyv: egyéni tevékenység létrehozása
### <a name="prerequisites"></a>Előfeltételek
* Visual Studio 2012/2013/2015/2017
* Az [Azure .NET SDK](https://azure.microsoft.com/downloads/) letöltése és telepítése.

### <a name="azure-batch-prerequisites"></a>Az Azure Batch előfeltételei
A forgatókönyvben futtatja az egyéni .NET-tevékenységeket az Azure Batch számítási erőforrásként használatával. Az **Azure Batch** platformszolgáltatás lehetővé teszi, hogy hatékonyan futtasson nagyméretű párhuzamos és nagy teljesítményű feldolgozási (HPC) alkalmazásokat a felhőben. Az Azure Batch ütemezi a számítási igényű munkát a virtuális gépek felügyelt **gyűjteményén**való futtatásra, és automatikusan skálázhatja a számítási erőforrásokat a feladatok igényeinek megfelelően. Tekintse meg [az Azure Batch alapjai][batch-technical-overview] ról szóló cikket az Azure Batch szolgáltatás részletes áttekintését.

Az oktatóanyaghoz hozzon létre egy Azure Batch-fiókot virtuális gépek készletével. A lépések a következők:

1. Hozzon létre egy **Azure Batch-fiókot** az [Azure Portalon](https://portal.azure.com)keresztül. Az utasításokat az [Azure Batch-fiók létrehozása és kezelése][batch-create-account] című cikkben találja.
2. Vegye figyelembe az Azure Batch-fiók nevét, a fiókkulcsot, az URI-t és a készlet nevét. Szüksége van rájuk egy Azure Batch-kapcsolt szolgáltatás létrehozásához.
    1. Az Azure Batch-fiók kezdőlapján a következő formátumban `https://myaccount.westus.batch.azure.com`jelenik meg egy **URL-cím:** . Ebben a példában **a myaccount** az Azure Batch-fiók neve. A csatolt szolgáltatásdefinícióban használt URI a fiók neve nélküli URL.URI you use in the linked service definition is the URL without the name of the account. Például: `https://<region>.batch.azure.com`.
    2. Kattintson a bal oldali menü **Billentyűgombparancsára,** és másolja az **ELSŐDLEGES HOZZÁFÉRÉSI KULCSOT**.
    3. Meglévő készlet használatához kattintson a menü **Készletek parancsára,** és jegyezze fel a készlet **azonosítóját.** Ha nem rendelkezik meglévő készlet, lépjen a következő lépésre.
2. Hozzon létre egy **Azure Batch-készletet.**

   1. Az [Azure Portalon](https://portal.azure.com)kattintson a bal oldali menü **Tallózás parancsára,** majd a **Kötegelt fiókok parancsra.**
   2. Válassza ki az Azure Batch-fiókot a **Batch-fiók** panel megnyitásához.
   3. Kattintson **a Medencék** csempe elemre.
   4. Készlet hozzáadásához kattintson a **Készletek** panelen az eszköztár Hozzáadás gombjára.
      1. Adja meg a készlet azonosítóját (készletazonosító). Jegyezze fel **a készlet azonosítóját;** a Data Factory megoldás létrehozásakor szüksége van rá.
      2. Adja meg a **Windows Server 2012 R2 értéket** az operációs rendszer család beállításához.
      3. Válasszon **csomóponti tarifacsomagot.**
      4. Adja meg a **2** értéket a **Dedikált cél** beállításhoz.
      5. Adja meg a **2** értéket a **Maximális feladatok csomópontonként** beállításhoz.
   5. A készlet létrehozásához kattintson az **OK** gombra.
   6. Jegyezze fel a készlet **azonosítóját.**

### <a name="high-level-steps"></a>Magas szintű lépések
Íme a forgatókönyv részeként végrehajtott két magas szintű lépés:

1. Hozzon létre egy egyéni tevékenységet, amely egyszerű adatátalakítási/feldolgozási logikát tartalmaz.
2. Hozzon létre egy Azure-adatfeldolgozó tanonc, amely az egyéni tevékenységet használja.

### <a name="create-a-custom-activity"></a>Egyéni tevékenység létrehozása
.NET egyéni tevékenység létrehozásához hozzon létre egy **.NET Osztálykönyvtár-projektet** egy olyan osztállyal, amely az **IDotNetActivity** felületet valósítja meg. Ez a felület csak egy módszerrel rendelkezik: [A végrehajtás](https://msdn.microsoft.com/library/azure/mt603945.aspx) és az aláírása a következő:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

A módszer négy paramétert vesz igénybe:

- **linkedServices**. Ez a tulajdonság a tevékenység bemeneti/kimeneti adatkészletei által hivatkozott adattárhoz kapcsolódó szolgáltatások számbasorolási listája.
- **adatkészletek .** Ez a tulajdonság a tevékenység bemeneti/kimeneti adatkészleteinek számbavemilegi listája. Ezzel a paraméterrel lejuthat a bemeneti és kimeneti adatkészletek által meghatározott helyek és sémák leése.
- **tevékenység.** Ez a tulajdonság az aktuális tevékenységet jelöli. Az egyéni tevékenységhez társított kiterjesztett tulajdonságok elérésére használható. A részleteket az [Access bővített tulajdonságai](#access-extended-properties) ban találja.
- **logger**. Ez az objektum lehetővé teszi a folyamat felhasználói naplójában megjelenő hibakeresési megjegyzések írását.

A metódus egy szótárat ad vissza, amely az egyéni tevékenységek et a jövőben együtt láncolhatja. Ez a funkció még nincs megvalósítva, ezért egy üres szótárt ad vissza a metódusból.

### <a name="procedure"></a>Eljárás
1. **.NET Osztálytár-projekt** létrehozása.
   <ol type="a">
     <li>Indítsa el a Visual Studiót.</li>
     <li>Kattintson a <b>File</b> (Fájl) menüre, mutasson a <b>New</b> (Új) elemre, és kattintson a <b>Project</b> (Projekt) lehetőségre.</li>
     <li>Bontsa ki a <b>Sablonok</b> lehetőséget, és válassza a <b>Visual C#</b> lehetőséget. Ebben a forgatókönyvben a C#-ot használja, de bármilyen .NET nyelvet használhat az egyéni tevékenység fejlesztéséhez.</li>
     <li>Válassza az <b>Osztálytár</b> elemet a jobb oldali projekttípusok listájából. A Visual Studio alkalmazásban válassza az <b>Osztálykönyvtár (.NET Keretrendszer) lehetőséget.</b> </li>
     <li>Írja be a <b>MyDotNetActivity nevet</b> a <b>nevéhez.</b></li>
     <li>Válassza a <b>C:\ADFGetStarted</b> lehetőséget a <b>helyhez.</b></li>
     <li>A projekt létrehozásához kattintson az <b>OK</b> gombra.</li>
   </ol>

2. Kattintson az **Eszközök** elemre, mutasson a **NuGet Package Manager** (NuGet-csomagkezelő) lehetőségre, majd kattintson a **Package Manager Console** (Csomagkezelő konzol) elemre.

3. A Csomagkezelő konzolon hajtsa végre a következő parancsot a **Microsoft.Azure.Management.DataFactories**importálásához.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importálja az **Azure Storage** NuGet csomagot a projektbe.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > A Data Factory szolgáltatásindítójához a WindowsAzure.Storage 4.3-as verziójára van szükség. Ha az egyéni tevékenységprojektben hozzáad egy hivatkozást az Azure Storage-szerelvény egy újabb verziójára, hibaüzenet jelenik meg a tevékenység végrehajtásakor. A hiba elhárításához olvassa el az [Apptartomány elkülönítése](#appdomain-isolation) című szakaszt.
5. Adja hozzá a következőket **utasításokkal** a projekt forrásfájljához.

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
6. Módosítsa a **névtér** nevét **MyDotNetActivityNS névre.**

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Módosítsa az osztály nevét **MyDotNetActivity** névre, és az **IDotNetActivity** felületről származtatja, ahogy az a következő kódrészletben látható:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Valósítsa meg (adja hozzá) az **IDotNetActivity** felület **Execute** metódusát a **MyDotNetActivity osztályba,** és másolja a következő mintakódot a metódusba.

    A következő minta megszámolja a keresési kifejezés ("Microsoft") előfordulásainak számát az adatszeletekhez társított minden blobban.

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

        // get the first Azure Storage linked service from linkedServices object
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
9. Adja hozzá a következő segítő módszereket:

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

    A GetFolderPath metódus visszaadja annak a mappának az elérési útját, amelyre az adatkészlet mutat, és a GetFileName metódus visszaadja annak a blobnak/fájlnak a nevét, amelyre az adatkészlet mutat. Ha a folderPath definiálja a következő változók használatával: {Year}, {Month}, {Day} stb., a metódus visszaadja a karakterláncot, ahogy van, anélkül, hogy futásidejű értékekre cserélné őket. A [Access extended properties](#access-extended-properties) SliceStart, SliceEnd stb.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    A Számítás módszer kiszámítja a Microsoft kulcsszó példányainak számát a bemeneti fájlokban (a mappában lévő blobok). A keresési kifejezés ("Microsoft") kódolva van a kódban.
10. Fordítsa össze a projektet. Kattintson a menü **Build parancsára,** majd a **Megoldás összeállítása parancsra.**

    > [!IMPORTANT]
    > Állítsa be a .NET Framework 4.5.2-es verzióját a projekt célkeretrendszereként: kattintson a jobb gombbal a projektre, és a **Tulajdonságok** parancsra kattintva állítsa be a célkeretrendszert. A Data Factory nem támogatja a .NET Framework 4.5.2-es verzióknál későbbi verzióira összeállított egyéni tevékenységeket.

11. Indítsa el a **Windows Intézőt**, és keresse meg a **bin\debug** vagy **bin\release** mappát a build típusától függően.
12. Hozzon létre egy Zip fájlt **MyDotNetActivity.zip,** amely tartalmazza az összes bináris a \<projekt mappában\>\bin\Debug mappába. Adja meg a **MyDotNetActivity.pdb** fájlt, hogy további részleteket, például sorszámot kapjon a hiba esetén a problémát okozó forráskódban.

    > [!IMPORTANT]
    > Az egyéni tevékenység zip-fájljában lévő összes fájlnak a **legfelső szinten** kell lennie, almappák nélkül.

    ![Bináris kimeneti fájlok](./media/data-factory-use-custom-activities/Binaries.png)
14. Hozzon létre egy blob tároló nevű **customactivitycontainer,** ha még nem létezik.
15. Töltsön fel MyDotNetActivity.zip blobként a customactivitycontainer egy **általános célú** Azure blob storage (nem hot/cool Blob storage), amely az AzureStorageLinkedService által említett.

> [!IMPORTANT]
> Ha ezt a .NET tevékenységprojektet hozzáadja egy, a Visual Studio egy data factory projektet tartalmazó megoldásához, és a Data Factory alkalmazásprojektből hivatkozik a .NET tevékenységprojektre, akkor nem kell végrehajtania a zip manuális létrehozásának utolsó két lépését. fájlt, és töltse fel az általános célú Azure blob storage.file and uploading it to the general-purpose Azure blob storage. Amikor a Data Factory entitásokat a Visual Studio használatával teszi közzé, ezeket a lépéseket a közzétételi folyamat automatikusan elvégezheti. További információ: [Data Factory project in Visual Studio](#data-factory-project-in-visual-studio) section.

## <a name="create-a-pipeline-with-custom-activity"></a>Folyamat létrehozása egyéni tevékenységgel
Egyéni tevékenységet hozott létre, és feltöltötte a zip-fájlt bináris fájllal egy blobtárolóba egy általános célú Azure **Storage-fiókban.** Ebben a szakaszban hozzon létre egy Azure-adatfeldolgozó, amely az egyéni tevékenységet használó folyamat.

Az egyéni tevékenység bemeneti adatkészlete blobokat (fájlokat) jelöl az adftutorial tároló customactivityinput mappájában a blob storage-ban. A tevékenység kimeneti adatkészlete az adftutorial tároló ablob-tároló customactivityoutput mappájában lévő kimeneti blobokat jelöli.

Hozzon létre **file.txt** fájlt a következő tartalommal, és töltse fel az **adftutorial** tároló **customactivityinput** mappájába. Hozza létre az adftutorial tárolót, ha még nem létezik.

```
test custom activity Microsoft test custom activity Microsoft
```

A bemeneti mappa az Azure Data Factory egy szeletének felel meg, még akkor is, ha a mappa két vagy több fájlt tartalmaz. Amikor a folyamat minden szeletet feldolgoz, az egyéni tevékenység végighalad a szelet beviteli mappájában lévő összes blobon.

Az adftutorial\customactivityoutput mappában egy kimeneti fájl jelenik meg egy vagy több sorral (ugyanaz, mint a bemeneti mappában lévő blobok száma):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Az alábbi lépéseket hajtja végre ebben a szakaszban:

1. Hozzon létre egy **adat-előállító**.
2. **Hozzon** létre csatolt szolgáltatásokat az Azure Batch-készlet virtuális gépek, amelyen az egyéni tevékenység fut, és az Azure Storage, amely a bemeneti/kimeneti blobok.
3. Hozzon létre bemeneti és kimeneti **adatkészleteket,** amelyek az egyéni tevékenység bemeneti és kimeneti adatait képviselik.
4. Hozzon létre egy **folyamatot,** amely az egyéni tevékenységet használja.

> [!NOTE]
> Hozza létre a **file.txt fájlt,** és töltse fel egy blobtárolóba, ha még nem tette meg. Lásd az előző szakasz utasításait.

### <a name="step-1-create-the-data-factory"></a>1. lépés: Az adatgyár létrehozása
1. Az Azure Portalra való bejelentkezés után tegye a következő lépéseket:
   1. Kattintson az **Erőforrás létrehozása** parancsra a bal oldali menüben.
   2. Kattintson a **Data + Analytics** elemre az **Új** panelen.
   3. Kattintson a **Data Factory** elemre a **Data analytics** (Adatelemzés) panelen.

      ![Új Azure Data Factory menü](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. Az **Új adatgyár** panelen adja meg a **CustomActivityFactory** értéket a névhez. Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenet jelenik meg: **A "CustomActivityFactory" adatgyárnév nem érhető el,** módosítsa az adatgyár nevét (például **yournameCustomActivityFactory),** majd próbálkozzon újra a létrehozással.

    ![Új Azure Data Factory panel](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Kattintson **az ERŐFORRÁS-CSOPORT NEVE gombra,** és jelöljön ki egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot.
4. Ellenőrizze, hogy a megfelelő **előfizetést** és **régiót** használja-e, ahol az adat-előállító létrehozását szeretné használni.
5. Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.
6. Láthatja, hogy az adatgyár az Azure Portal **irányítópultján** jön létre.
7. Az adat-előállító sikeres létrehozása után megjelenik a Data Factory panel, amely megmutatja az adatgyár tartalmát.

    ![A Data Factory panel](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>2. lépés: Csatolt szolgáltatások létrehozása
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. Ebben a lépésben összekapcsolhatja az Azure Storage-fiókot és az Azure Batch-fiókot az adat-előállítóval.

#### <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása
1. Kattintson a Author and deploy tile **(Szerzői és üzembe helyezési csempe)** csempére a **Data Factory** panelen a **CustomActivityFactory**számára. Ekkor megjelenik a Data Factory Editor.
2. Kattintson az **Új adattár elemre** a parancssávon, és válassza az **Azure storage**lehetőséget. A szerkesztőben megjelenik az Azure Storage társított szolgáltatás létrehozására szolgáló JSON-parancsfájl.

    ![Új adattár - Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Cserélje `<accountname>` le az Azure storage-fiók nevét és `<accountkey>` az Azure storage-fiók hozzáférési kulcsát. A tárfiók hozzáférési kulcsának [kezelése( Tárfiók-hozzáférési kulcsok kezelése)](../../storage/common/storage-account-keys-manage.md)témakörből megtudhatja, hogy miként szerezheti be a tárfiók hozzáférési kulcsait.

    ![Az Azure Storage kedvelt szolgáltatása](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére.

#### <a name="create-azure-batch-linked-service"></a>Azure Batch-alapú szolgáltatás létrehozása
1. A Data Factory Editor, kattintson **... További a** parancssávon kattintson az **Új számítás gombra,** majd válassza az **Azure Batch** parancsot a menüből.

    ![Új számítás - Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Hajtsa végre a következő módosításokat a JSON-parancsfájlon:

   1. Adja meg az Azure Batch-fiók nevét a **accountName** tulajdonsághoz. Az Azure **Batch-fiók panelurl-címe** a `http://accountname.region.batch.azure.com`következő formátumban van: . **URL** A **BatchUri** tulajdonság a JSON, el `accountname.` kell távolítani az `accountname` URL-címből, és használja a `accountName` JSON tulajdonság.
   2. Adja meg az Azure Batch-fiók kulcsát az **accessKey** tulajdonsághoz.
   3. Adja meg a **készletnév** tulajdonság előfeltételei nek részeként létrehozott készlet nevét. A készlet neve helyett a készlet azonosítóját is megadhatja.
   4. Adja meg az Azure Batch URI-t a **batchUri** tulajdonsághoz. Példa: `https://westus.batch.azure.com`.
   5. Adja meg az **AzureStorageLinkedService** a **linkedServiceName** tulajdonsághoz.

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

       A **készletnév** tulajdonsághoz a készlet neve helyett a készlet azonosítóját is megadhatja.

### <a name="step-3-create-datasets"></a>3. lépés: Adatkészletek létrehozása
Ebben a lépésben a bemeneti és kimeneti adatokat jelölő adatkészleteket hoz létre.

#### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása
1. A **szerkesztő** a Data Factory, kattintson **... További a** parancssávon kattintson az **Új adatkészlet gombra**, és válassza az **Azure Blob storage** lehetőséget a legördülő menüből.
2. Cserélje le a jobb oldali ablaktáblában lévő JSON-t a következő JSON-kódrészletre:

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

   A forgatókönyv későbbi részében hozzon létre egy folyamatot a kezdési idővel: 2016-11-16T00:00:00Z és befejezési idő: 2016-11-16T05:00:00Z. A tervek szerint óránként hoz létre adatokat, így öt bemeneti/kimeneti szelet van **(00:00:00**és > 05:00:00 között). **05**

   A bemeneti adatkészlet **gyakorisága** és **időköze** **Óra** és **1,** ami azt jelenti, hogy a bemeneti szelet óránként érhető el. Ebben a példában ugyanaz a fájl (file.txt) található az intputmappában.

   Itt vannak az egyes szeletek kezdési időpontjai, amelyeket a SliceStart rendszerváltozó képvisel a fenti JSON-kódrészletben.
3. Kattintson a **Telepítés gombra** az eszköztáron a **Beviteliadatkészlet**létrehozásához és telepítéséhez. Győződjön meg arról, hogy a szerkesztő címsorában megjelenik a **TABLE CREATED SUCCESSFULLY** (A TÁBLA SIKERESEN LÉTREJÖTT) üzenet.

#### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
1. A **Data Factory szerkesztőben**kattintson **a ... További információk** a parancssávon kattintson az **Új adatkészlet**elemre, és válassza az Azure Blob **storage lehetőséget.**
2. Cserélje le a json-parancsfájlt a jobb oldali ablaktáblára a következő JSON-parancsfájlra:

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

     Kimeneti hely **adftutorial/customactivityoutput/** és kimeneti fájl neve yyyy-MM-dd-HH.txt, ahol yyyy-MM-dd-HH az év, hónap, dátum és óra a szelet készül. A részleteket lásd a [Fejlesztői kézikönyvben.][adf-developer-reference]

    Minden bemeneti szelethez létrejön egy kimeneti blob/fájl. Itt van, hogyan kell egy kimeneti fájlt elnevezni az egyes szeletekhez. Az összes kimeneti fájl egy kimeneti mappában jön létre: **adftutorial\customactivityoutput**.

   | Szelet | Kezdési idő | Kimeneti fájl |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Ne feledje, hogy a bemeneti mappában lévő összes fájl egy szelet része, amelya fent említett kezdési időpontokat tartalmaz. A szelet feldolgozásakor az egyéni tevékenység átvizsgálja az egyes fájlokat, és létrehoz egy sort a kimeneti fájlban a keresési kifejezés előfordulásainak számával ("Microsoft"). Ha három fájl van a bemeneti mappában, akkor minden óránkénti szelethez három sor van a kimeneti fájlban: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt stb.
3. A **OutputDataset**telepítéséhez kattintson a **Központi telepítés gombra** a parancssávon.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Egyéni tevékenységet használó folyamat létrehozása és futtatása
1. A Data Factory Editor, kattintson **... További**, majd válassza az **Új folyamat lehetőséget** a parancssávon.
2. Cserélje le a jobb oldali ablaktáblában lévő JSON-t a következő JSON-parancsfájlra:

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

   * **Egyidejűség** van beállítva **2** úgy, hogy két szelet feldolgozása párhuzamosan 2 virtuális gép az Azure Batch-készletben.
   * A tevékenységek részben van egy tevékenység, amely a következő típusú: **DotNetActivity**.
   * **Az AssemblyName** beállítása a DLL neve: **MyDotnetActivity.dll**.
   * **Az EntryPoint** beállítása **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** van beállítva, hogy **AzureStorageLinkedService,** amely rámutat arra a blob storage, amely tartalmazza az egyéni tevékenység zip fájlt. Ha különböző Azure Storage-fiókokat használ a bemeneti/kimeneti fájlokhoz és az egyéni tevékenységzip-fájlhoz, hozzon létre egy másik Azure Storage-kapcsolt szolgáltatást. Ez a cikk feltételezi, hogy ugyanazt az Azure Storage-fiókot használja.
   * **A PackageFile** beállítása **customactivitycontainer/MyDotNetActivity.zip**. Ez a formátum: containerforthezip / nameofthezip.zip.
   * Az egyéni tevékenység az **InputDataset-et** bemenetként, **a OutputDataset-et** pedig kimenetként veszi fel.
   * Az egyéni tevékenység linkedServiceName tulajdonsága az **AzureBatchLinkedService-re**mutat, amely közli az Azure Data Factoryval, hogy az egyéni tevékenységnek az Azure Batch virtuális gépeken kell futnia.
   * **Az isPaused** tulajdonság alapértelmezés szerint **hamis.** A folyamat azonnal fut ebben a példában, mert a szeletek a múltban kezdődnek. Ezt a tulajdonságot true értékre állíthatja a folyamat szüneteltetéséhez, és hamis ra állíthatja vissza az újraindításhoz.
   * A **kezdési** és **befejezési** idők **egymástól öt** óra, a szeletek pedig óránként készülnek, így a csővezeték öt szeletet állít elő.
3. A folyamat üzembe helyezéséhez kattintson a **központi telepítés gombra** a parancssávon.

### <a name="monitor-the-pipeline"></a>A folyamat figyelése
1. Az Azure Portal Data Factory paneljén kattintson a **Diagram gombra.**

    ![Diagram csempe](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. A Diagram nézetben kattintson a Kimenetiadatkészletre.

    ![Diagramnézet](./media/data-factory-use-custom-activities/diagram.png)
3. Látnia kell, hogy az öt kimeneti szelet kész állapotban van. Ha nem a Ready állapotban vannak, még nem készültek el.

   ![Kimeneti szeletek](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Ellenőrizze, hogy a kimeneti fájlok a blob storage az **adftutorial** tárolóban jönnek létre.

   ![kimenet egyéni tevékenységből][image-data-factory-output-from-custom-activity]
5. Ha megnyitja a kimeneti fájlt, a kimenetnek a következőkimenethez hasonlónak kell lennie:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Az [Azure Portalon][azure-preview-portal] vagy az Azure PowerShell-parancsmagok segítségével figyelheti az adat-előállító, a folyamatok és az adatkészletek. Az **ActivityLogger** től érkező üzeneteket az egyéni tevékenység kódjában láthatja a naplókban (különösen a felhasználó-0.log), amelyet a portálról vagy parancsmagok használatával tölthet le.

   ![naplók letöltése egyéni tevékenységből][image-data-factory-download-logs-from-custom-activity]

Az adatkészletek és folyamatok figyelésének részletes lépéseit a [Folyamatok figyelése](data-factory-monitor-manage-pipelines.md) és kezelése című témakörben található.

## <a name="data-factory-project-in-visual-studio"></a>Data Factory projekt a Visual Studióban
A Data Factory-entitásokat az Azure Portal használata helyett a Visual Studio használatával hozhat létre és tehet heti közzé. A Data Factory-entitások Visual Studio használatával történő létrehozásáról és közzétételéről az [Első folyamat létrehozása a Visual Studio használatával](data-factory-build-your-first-pipeline-using-vs.md) című témakörben talál részletes információt, és adatok [másolása az Azure Blobból az Azure SQL-cikkekbe](data-factory-copy-activity-tutorial-using-visual-studio.md) című témakörben talál részletes információt.

A Data Factory-projekt Visual Studio-ban való létrehozásakor tegye a következő további lépéseket:

1. Adja hozzá a Data Factory projektet az egyéni tevékenységprojektet tartalmazó Visual Studio-megoldáshoz.
2. Hivatkozás hozzáadása a Data Factory projekt .NET tevékenységprojektjéhez. Kattintson a jobb gombbal a Data Factory projektre, mutasson a **Hozzáadás**pontra, majd kattintson **a Hivatkozás parancsra.**
3. A **Hivatkozás hozzáadása** párbeszédpanelen jelölje ki a **MyDotNetActivity projektet,** majd kattintson az **OK**gombra.
4. Hozza létre és tegye közzé a megoldást.

    > [!IMPORTANT]
    > A Data Factory-entitások közzétételekor a rendszer automatikusan létrehoz egy zip-fájlt, és feltölti a blobtárolóba: customactivitycontainer. Ha a blob tároló nem létezik, akkor automatikusan jön létre is.

## <a name="data-factory-and-batch-integration"></a>Adatgyár és kötegintegráció
A Data Factory szolgáltatás létrehoz egy feladatot az Azure Batch-ben a következő névvel: **adf-poolname: job-xxx**. Kattintson a bal oldali menü **Feladatok** parancsára.

![Azure Data Factory - Kötegelt feladatok](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Egy feladat jön létre a szelet minden egyes tevékenységfuttatásához. Ha öt szelet van előkészítve a feldolgozásra, öt feladat jön létre ebben a feladatban. Ha a Batch készletben több számítási csomópont is található, két vagy több szelet párhuzamosan futhat. Ha a számítási csomópontonkénti maximális feladatok > 1 értékre vannak állítva, akkor egynél több szelet is futtathat ugyanazon a számítási területen.

![Azure Data Factory – kötegelt feldolgozási feladatok](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Az alábbi ábra az Azure Data Factory és a Batch-feladatok közötti kapcsolatot mutatja be.

![Adatgyári & köteg](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Hibák elhárítása
A hibaelhárítás néhány alapvető technikából áll:

1. Ha a következő hiba jelenik meg, előfordulhat, hogy egy hot/cool blob storage használata helyett egy általános célú Azure blob storage használata. Töltse fel a zip fájlt egy **általános célú Azure Storage-fiókba.**

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Ha a következő hiba jelenik meg, ellenőrizze, hogy a CS-fájlban lévő osztály neve megegyezik-e a JSON folyamatban lévő **EntryPoint** tulajdonsághoz megadott névvel. A forgatókönyvben az osztály neve: MyDotNetActivity, és az EntryPoint a JSON-ban: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Ha a nevek megegyeznek, ellenőrizze, hogy az összes bináris fájl a zip fájl **gyökérmappájában** van-e. Ez azt jelenti, hogy amikor megnyitja a zip fájlt, akkor a gyökérmappában lévő összes fájlt látnia kell, nem pedig bármely almappában.
3. Ha a bemeneti szelet nincs **Kész**beállításra, ellenőrizze, hogy a bemeneti mappa szerkezete helyes-e, és a **file.txt** fájl létezik-e a bemeneti mappákban.
3. Az egyéni tevékenység **végrehajtása** metódusában használja az **IActivityLogger** objektumot a problémák elhárítását segítő információk naplózásához. A naplózott üzenetek megjelennek a felhasználói naplófájlokban (egy vagy több nevű fájl: user-0.log, user-1.log, user-2.log stb.).

   A **OutputDataset** panelen kattintson a szeletre az adott szelet **DATA SLICE** paneljének megtekintéséhez. Láthatja, hogy **a tevékenység fut** az adott szelethez. Meg kell jelennie egy tevékenység fut a szeletet. Ha a parancssávon a Futtatás gombra kattint, akkor egy másik tevékenységfuttatást is elindíthat ugyanahhoz a szelethez.

   Amikor a tevékenységfuttatásra kattint, megjelenik a **TEVÉKENYSÉG FUTTATÁSI RÉSZLETEK** panel a naplófájlok listájával. A naplózott üzenetek a user_0.log fájlban jelennek meg. Hiba esetén három tevékenység fut, mert az újrapróbálkozások száma 3-ra van állítva a folyamat/tevékenység JSON.When a error occurs, you see three activity runs because the retry count is set to 3 in the pipeline/activity JSON. Amikor a tevékenységfuttatásra kattint, megjelennek azok a naplófájlok, amelyeket áttekinthet a hiba elhárításához.

   A naplófájlok listájában kattintson a **0.log felhasználóra.** A jobb oldali panelen az **IActivityLogger.Write** metódus használatának eredményei találhatók. Ha nem látja az összes üzenetet, ellenőrizze, hogy van-e további naplófájlja: user_1.log, user_2.log stb. Ellenkező esetben előfordulhat, hogy a kód nem sikerült az utolsó naplózott üzenet után.

   Ezenkívül ellenőrizze a **system-0.log** fájlt, hogy vannak-e rendszerhibaüzenetek és kivételek.
4. A **PDB-fájl** felvétele a zip-fájlba, hogy a hiba részletei olyan információkkal rendelkezhessenek, mint például **a hívásverem,** ha hiba történik.
5. Az egyéni tevékenység zip-fájljában lévő összes fájlnak a **legfelső szinten** kell lennie, almappák nélkül.
6. Győződjön meg arról, hogy az **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivity.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) és **packageLinkedService** (kell mutatnia az **általános célú**Azure blob storage, amely tartalmazza a zip-fájlt) be vannak állítva, hogy helyes értékeket.
7. Ha kijavított egy hibát, és újra fel szeretné dolgozni a szeletet, kattintson a jobb gombbal a szeletre az **OutputDataset** panelen, és kattintson a **Futtatás** parancsra.
8. Ha a következő hibaüzenet jelenik meg, a 4.3.0-s > verziójú Azure Storage-csomagot használja. A Data Factory szolgáltatásindítójához a WindowsAzure.Storage 4.3-as verziójára van szükség. Tekintse meg [az Appdomain elkülönítési](#appdomain-isolation) szakaszban egy work-around, ha az Azure Storage-szerelvény későbbi verzióját kell használnia.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Ha használhatja az Azure Storage-csomag 4.3.0-s verzióját, távolítsa el a > 4.3.0-s verziójú Azure Storage-csomagra vonatkozó meglévő hivatkozást. Ezután futtassa a következő parancsot a NuGet Package Manager konzolról.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    A projekt felépítése. Törölje az Azure.Storage-szerelvényt > 4.3.0-s verziójából a bin\Debug mappából. Hozzon létre egy zip fájlt bináris fájllal és a PDB-fájllal. Cserélje le a régi zip fájlt ezzel a blob tárolóban (customactivitycontainer). Futtassa újra a sikertelen szeleteket (kattintson a jobb gombbal a szeletre, és válassza a Futtatás parancsot).
8. Az egyéni tevékenység nem használja a csomagból származó **app.config** fájlt. Ezért ha a kód beolvassa a konfigurációs fájlból származó kapcsolati karakterláncokat, az futásidőben nem működik. Az Azure Batch használata során ajánlott az **Azure KeyVault**titkos kulcsainak megtartása, a **keyvault**védelme érdekében használjon tanúsítványalapú egyszerű szolgáltatást, és ossza szét a tanúsítványt az Azure Batch-készletben. A .NET egyéni tevékenysége ezután elérheti a titkos kulcsokat a kulcstartóból a futtatáskor. Ez a megoldás egy általános megoldás, és skálázás bármilyen típusú titkos, nem csak a kapcsolat karakterlánc.

   Van egy egyszerűbb megoldás (de nem ajánlott eljárás): létrehozhat egy **Azure SQL-kapcsolt szolgáltatást** kapcsolati karakterlánc-beállításokkal, létrehozhat egy adatkészletet, amely a csatolt szolgáltatást használja, és az adatkészletet egy dummy bemeneti adatkészletként az egyéni .NET tevékenységhez láncolhatja. Ezután elérheti a csatolt szolgáltatás kapcsolati karakterláncát az egyéni tevékenységkódban.

## <a name="update-custom-activity"></a>Egyéni tevékenység frissítése
Ha frissíti az egyéni tevékenység kódját, építse létre, és töltse fel az új bináris fájlokat tartalmazó zip-fájlt a blobstorage-ba.

## <a name="appdomain-isolation"></a>Apptartomány elkülönítése
Lásd: [Cross AppDomain Sample, amely bemutatja,](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) hogyan hozhat létre olyan egyéni tevékenységet, amely nem korlátozza a Data Factory indítója által használt összeállítási verziókat (például: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x stb.).

## <a name="access-extended-properties"></a>Access bővített tulajdonságok
A JSON tevékenységkiterjesztett tulajdonságait deklarálhatja az alábbi minta szerint:

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

A példában két kiterjesztett tulajdonság található: **SliceStart** és **DataFactoryName**. A SliceStart értéke a SliceStart rendszerváltozón alapul. A támogatott rendszerváltozók listáját a [Rendszerváltozók](data-factory-functions-variables.md) című témakörben található. A DataFactoryName értéke a CustomActivityFactory kódolású.

A kiterjesztett tulajdonságok **végrehajtásához** használja a következő kódhoz hasonló kódot:

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

## <a name="auto-scaling-of-azure-batch"></a>Az Azure Batch automatikus méretezése
Az Automatikus **skálázási** funkcióval azure batch készletet is létrehozhat. Létrehozhat például egy azure-kötegkészletet 0 dedikált virtuális géptel és egy automatikus skálázási képlettel a függőben lévő feladatok száma alapján.

A mintaképlet itt a következő viselkedést éri el: Amikor a készlet először jön létre, 1 virtuális gépkel kezdődik. $PendingTasks metrika határozza meg a feladatok számát futó + aktív (várólistára helyezett) állapotban.  A képlet megkeresi a függőben lévő feladatok átlagos számát az elmúlt 180 másodpercben, és ennek megfelelően állítja be a TargetDedicated függvényt. Ez biztosítja, hogy a TargetDedicated soha nem lépi túl a 25 virtuális gépet. Így az új feladatok elküldésével a készlet automatikusan növekszik, és a feladatok befejeződésével a virtuális gépek egyenként válnak ingyenessé, és az automatikus skálázás csökkenti a virtuális gépeket. az startingNumberOfVMs és a maxNumberofVMs az Ön igényeinek megfelelően módosítható.

Automatikus skálázási képlet:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

A részletekért tekintse meg [a számítási csomópontok automatikus méretezése az Azure Batch-készletben.](../../batch/batch-automatic-scaling.md)

Ha a készlet az alapértelmezett [autoScaleEvaluationInterval ,](https://msdn.microsoft.com/library/azure/dn820173.aspx)a Batch szolgáltatás 15–30 percet is igénybe vehet a virtuális gép előkészítése az egyéni tevékenység futtatása előtt.  Ha a készlet egy másik autoScaleEvaluationInterval- ot használ, a Batch szolgáltatás autoScaleEvaluationInterval + 10 percet vehet igénybe.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Egyéni tevékenység létrehozása a .NET SDK használatával
Ebben a cikkben a forgatókönyvben hozzon létre egy adat-előállító egy folyamat, amely az egyéni tevékenység et használja az Azure Portal használatával. A következő kód bemutatja, hogyan lehet létrehozni az adat-előállítót a .NET SDK használatával. További részleteket az SDK segítségével programozott módon hozzon létre [egy folyamat másolási tevékenységet a .NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md) cikk használatával.

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

## <a name="debug-custom-activity-in-visual-studio"></a>Egyéni tevékenység hibakeresése a Visual Studióban
Az [Azure Data Factory – helyi környezet](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) minta a GitHubon tartalmaz egy eszközt, amely lehetővé teszi az egyéni .NET-tevékenységek hibakeresését a Visual Studio-n belül.

## <a name="sample-custom-activities-on-github"></a>Egyéni tevékenységek mintaaa a GitHubon
| Sample | Milyen egyéni tevékenységet végez? |
| --- | --- |
| [HTTP-adatletöltő](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Adatok letöltése http-végpontról az Azure Blob Storage-ba egyéni C# tevékenység használatával a Data Factory.Downloads data from an HTTP Endpoint to Azure Blob Storage using custom C# Activity in Data Factory. |
| [Twitter hangulatelemzés minta](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Meghív egy Azure Machine Learning-stúdiómodellt, és érzelemelemzést, pontozást, előrejelzést stb. |
| [Futtassa az R-parancsfájlt](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Az R-parancsfájl meghívása az RScript.exe futtatásával a HDInsight-fürtön, amelyen már telepítve van r. |
| [Alkalmazástartományon belüli .NET-tevékenység](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |A Data Factory indítóáltal használt verzióktól eltérő összeállítási verziókat használ |
| [Modell újrafeldolgozása az Azure Analysis Services ben](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Újrafeldolgozegy modellt az Azure Analysis Servicesben. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

---
title: Egyéni tevékenységek használata Azure Data Factory-folyamatban
description: Ismerje meg, hogyan hozhat létre egyéni tevékenységeket, és használja őket az Azure Data Factory-folyamatot.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 6d7aeef415fcc53fda2f5d66d748dd38f6d644fb
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576735"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Egyéni tevékenységek használata Azure Data Factory-folyamatban
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](data-factory-use-custom-activities.md)
> * [2-es verzió (aktuális verzió)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [egyéni tevékenységeket a v2-ben](../transform-data-using-dotnet-custom-activity.md).

Két típusa a tevékenységeket, az Azure Data Factory-folyamatban van.

- [Adattovábbítási tevékenységek](data-factory-data-movement-activities.md) közötti áthelyezése [támogatott forrás- és fogadó adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Adat-átalakítási tevékenységeket](data-factory-data-transformation-activities.md) adatok átalakításához a számítási szolgáltatások, például Azure HDInsight, az Azure Batch és az Azure Machine Learning használatával.

Az adatok áthelyezéséhez és- tárolókról egy adattár, amely nem támogatja az adat-előállító létrehozása a **egyéni tevékenység** a saját adatok adatátviteli logic és a tevékenységet a folyamat használja. Hasonlóképpen úgy, hogy a Data Factory által nem támogatott az adatok átalakíthatók/feldolgozhatók, a saját Adatátalakítási logikát az egyéni tevékenységek létrehozása és használja a tevékenységet a folyamat.

Konfigurálhat egyéni tevékenységek futtatását egy **Azure Batch** virtuálisgép-készletek. Azure Batch használatakor csak egy meglévő Azure-Batch-készletben is használhatja.

A következő forgatókönyv részletes útmutatást nyújt a .NET egyéni tevékenység létrehozása, és az egyéni tevékenységek használata egy folyamatban. A forgatókönyv egy **Azure Batch** társított szolgáltatást.

> [!IMPORTANT]
> - Nem alkalmas egy adatkezelési átjárón az egyéni tevékenységek használata a helyszíni adatforrások eléréséhez. Jelenleg [adatkezelési átjáró](data-factory-data-management-gateway.md) csak a másolási tevékenység és a tárolt eljárási tevékenység támogatja az adat-előállítóban.

## <a name="walkthrough-create-a-custom-activity"></a>Forgatókönyv: egyéni tevékenységek létrehozása
### <a name="prerequisites"></a>Előfeltételek
* Visual Studio 2012/2013/2015
* Az [Azure .NET SDK](https://azure.microsoft.com/downloads/) letöltése és telepítése.

### <a name="azure-batch-prerequisites"></a>Az Azure Batch előfeltételei
A forgatókönyv futtatása az Azure Batch számítási erőforrásai használatával egyéni .NET-tevékenységek. **Az Azure Batch** van egy platform szolgáltatást futtató nagy méretű párhuzamos és nagy teljesítményű számítástechnika (HPC) alkalmazásokat futtathat hatékonyan a felhőben. Az Azure Batch számításigényes munkák futtatását egy felügyelt ütemezi **virtuális gépek gyűjteményét**, és képes automatikusan méretezni a számítási erőforrásokat a feladatok igényeinek kielégítése érdekében. Lásd: [Azure Batch alapjai] [ batch-technical-overview] cikk részletes áttekintést az Azure Batch szolgáltatás.

Az oktatóanyag az Azure Batch-fiók létrehozása készletét, virtuális gépeket. A lépések a következők:

1. Hozzon létre egy **Azure Batch-fiók** használatával a [az Azure portal](https://portal.azure.com). Lásd: [létrehozása és kezelése az Azure Batch-fiók] [ batch-create-account] a cikk útmutatást.
2. Jegyezze fel az Azure Batch-fiók neve, fiókkulcs, URI és az alkalmazáskészlet neve. Szükség van rájuk egy Azure Batch-beli társított szolgáltatás létrehozásához.
    1. Az Azure Batch-fiók kezdőlapon látható egy **URL-cím** a következő formátumban: `https://myaccount.westus.batch.azure.com`. Ebben a példában **myaccount** az Azure Batch-fiók neve. Használhatja a társított szolgáltatás definíciójában URI-ja az URL-cím nélkül a fiók nevét. Például: `https://<region>.batch.azure.com`.
    2. Kattintson a **kulcsok** a bal oldali menüben, és másolja a **elsődleges ELÉRÉSI kulcs**.
    3. Egy meglévő készlet használatához kattintson **készletek** a menüben, és jegyezze fel a **azonosító** a készlet. Ha nem rendelkezik egy meglévő készletbe, helyezze át a következő lépéssel.
2. Hozzon létre egy **Azure Batch-készlet**.

   1. Az a [az Azure portal](https://portal.azure.com), kattintson a **Tallózás** a bal oldali menüben, majd kattintson a **Batch-fiókok**.
   2. Az Azure Batch-fiókra a megnyitásához válassza a **Batch-fiók** panelen.
   3. Kattintson a **készletek** csempére.
   4. Az a **készletek** panelen kattintson a Hozzáadás gombra az eszköztáron a készlet hozzáadása.
      1. Adja meg a készlet (készlet azonosítója) Azonosítóját. Megjegyzés: a **annak a készletnek azonosítója**; szüksége lesz rá a Data Factory-megoldás létrehozása során.
      2. Adja meg **Windows Server 2012 R2** az operációs rendszer Termékcsaládját beállításához.
      3. Válassza ki a **csomóponti tarifacsomagot**.
      4. Adja meg **2** as értékét a **cél dedikált** beállítás.
      5. Adja meg **2** as értékét a **csomópontonkénti tevékenységek maximális** beállítás.
   5. A készlet létrehozásához kattintson az **OK** gombra.
   6. Jegyezze fel a **azonosító** a készlet.

### <a name="high-level-steps"></a>Magas szintű lépései
Ez a bemutató részeként végezhet két magas szintű lépései a következők:

1. Hozzon létre egy egyéni tevékenységet, amely egyszerű átalakítása/feldolgozása logikáját tartalmazza.
2. Hozzon létre egy Azure data factory az egyéni tevékenységet használó folyamatot.

### <a name="create-a-custom-activity"></a>Egyéni tevékenységek létrehozása
.NET egyéni tevékenység, hozzon létre egy **.NET osztálytár** projekt egy olyan osztállyal, amely megvalósító **IDotNetActivity** felületet. Ez az interfész már csak egy ezek közül: [Hajtsa végre](https://msdn.microsoft.com/library/azure/mt603945.aspx) , és az aláírása:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

A metódus négy paramétereket fogadja:

- **linkedServices**. Ez a tulajdonság egy Data Store társított szolgáltatások, a tevékenység bemeneti és kimeneti adatkészleteket által hivatkozott enumerálható listáját.
- **az adatkészletek**. Ez a tulajdonság egy enumerálható listáját a tevékenység bemeneti és kimeneti adatkészleteket. Ez a paraméter használatával a helyek és a bemeneti és kimeneti adatkészleteket által definiált sémák.
- **tevékenység**. Ez a tulajdonság a jelenlegi tevékenység jelöli. Az egyéni tevékenység társított kiterjesztett tulajdonságok eléréséhez használható. Lásd: [kiterjesztett tulajdonságok hozzáférés](#access-extended-properties) részleteiről.
- **logger**. Ez az objektum lehetővé teszi, hogy a surface hibakeresési megjegyzések írását a a folyamat a felhasználó bejelentkezése.

A metódus adja vissza egy szótár, amely összekapcsolja az egyéni tevékenységek együtt a jövőben is használható. Ez a funkció még nincs megvalósítva, ezért vissza a metódus egy üres szótárban.

### <a name="procedure"></a>Eljárás
1. Hozzon létre egy **.NET osztálytár** projekt.
   <ol type="a">
     <li>Indítsa el a <b>Visual Studio 2017</b> vagy <b>Visual Studio 2015</b> vagy <b>Visual Studio 2013</b> vagy <b>Visual Studio 2012</b>.</li>
     <li>Kattintson a <b>File</b> (Fájl) menüre, mutasson a <b>New</b> (Új) elemre, és kattintson a <b>Project</b> (Projekt) lehetőségre.</li>
     <li>Bontsa ki a <b>Sablonok</b> lehetőséget, és válassza a <b>Visual C#</b> lehetőséget. Ez az útmutató használata a C#, de bármilyen .NET programozási nyelvvel használhatja az egyéni tevékenység fejlesztéséhez.</li>
     <li>Válassza ki <b>osztálytár</b> projekttípusok jobb listájából. A VS 2017-ben, válasszon <b>osztálytár (.NET-keretrendszer)</b> </li>
     <li>Adja meg <b>MyDotNetActivity</b> számára a <b>neve</b>.</li>
     <li>Válassza ki <b>C:\ADFGetStarted</b> számára a <b>hely</b>.</li>
     <li>A projekt létrehozásához kattintson az <b>OK</b> gombra.</li>
   </ol>

2. Kattintson az **Eszközök** elemre, mutasson a **NuGet Package Manager** (NuGet-csomagkezelő) lehetőségre, majd kattintson a **Package Manager Console** (Csomagkezelő konzol) elemre.

3. A Package Manager Console hajtsa végre a következő parancsot az importálandó **Microsoft.Azure.Management.DataFactories**.

    ```PowerShell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importálás a **Azure Storage** NuGet-csomagot a projekthez.

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Data Factory szolgáltatás indítója WindowsAzure.Storage 4.3 verziója szükséges. Ha az egyéni tevékenység-projektben hozzáad egy hivatkozást az Azure Storage-szerelvény újabb verzióra, hibaüzenet jelenik meg a tevékenység végrehajtása során. A hiba elhárításához lásd: [Appdomain elkülönítési](#appdomain-isolation) szakaszban.
5. Adja hozzá a következő **használatával** -utasítások használatával a forrás fájlt a projektben.

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
6. Módosítsa a nevet a **névtér** való **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Módosítsa a kívánt osztály nevét **MyDotNetActivity** , és amelyek a azt a **IDotNetActivity** illesztő az alábbi kódrészletben látható módon:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. (Hozzáadás) megvalósítása a **Execute** módszer a **IDotNetActivity** a csatoló a **MyDotNetActivity** osztályt, és másolja az alábbi mintakód a metódus.

    Az alábbi minta a keresési kifejezés ("Microsoft") előfordulását megszámlálja az adatszelet társított minden egyes blob.

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
9. Adja hozzá a következő segédmetódusokat:

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

    A GetFolderPath metódus az az elérési utat a mappához, amely az adatkészlet ad vissza, és a GetFileName metódust a blob/fájl, amely az adatkészlet nevét adja vissza. Ha folderPath meghatározása a változókban például {Year}, {Month}, {Day} stb., a metódus adja vissza a karakterláncot, mert a modul értékek lecserélése nélkül. Lásd: [kiterjesztett tulajdonságok hozzáférés](#access-extended-properties) című szakasz részletezi a fér hozzá a SliceStart, SliceEnd stb.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    A Calculate metódus a bemeneti fájlt (a mappában lévő blobok) Microsoft kulcsszó példányainak számát számítja ki. A keresési kifejezés ("Microsoft") nem változtatható a kódban.
10. A projekt fordításához használandó. Kattintson a **összeállítása** a menüben, majd kattintson a **megoldás fordítása**.

    > [!IMPORTANT]
    > A projekt célkeretrendszerként .NET-keretrendszer 4.5.2-es set verziója: kattintson a jobb gombbal a projektre, majd kattintson **tulajdonságok** cílová architektura beállításához. Adat-előállító nem támogatja a lefordított ellen a .NET-keretrendszer-verziókat 4.5.2-es később egyéni tevékenységeket.

11. Indítsa el a **Windows Explorer**, és keresse meg **bin\debug** vagy **bin\release** mappa build típusától függően.
12. Hozzon létre egy zip-fájlt **MyDotNetActivity.zip** , amely tartalmazza az összes bináris fájl a \<projektmappa\>\bin\Debug mappába. Tartalmazza a **MyDotNetActivity.pdb** fájlt annak érdekében, hogy további részleteket, például a sor száma, amelyek a probléma oka, hogy hiba történt a forráskódban.

    > [!IMPORTANT]
    > Az egyéni tevékenység zip-fájljában lévő összes fájlnak a **legfelső szinten** kell lennie, almappák nélkül.

    ![Bináris kimeneti fájlok](./media/data-factory-use-custom-activities/Binaries.png)
14. Hozzon létre egy blobtárolót **customactivitycontainer** Ha azt nem létezik.
15. Az a customactivitycontainer blobként MyDotNetActivity.zip feltöltése egy **általános célú** , amely a következő hivatkozás alapján AzureStorageLinkedService az Azure blob storage (nem gyakori és ritka elérésű Blob storage).

> [!IMPORTANT]
> Ha a .NET-tevékenység projekt hozzáadása egy megoldást a Visual Studióban, amely tartalmazza a Data Factory projektre, és vegyen fel egy hivatkozást a Data Factory-projektre a .NET-tevékenység projekt, nem kell hajtsa végre az utolsó két lépést a zip-fájl manuális létrehozása fájl- és azt az általános célú Azure blobtárolóba való feltöltését. Ha közzéteszi a Data Factory-entitások a Visual Studio használatával, az alábbi lépéseket automatikusan kell elvégeznie a közzétételi folyamat. További információkért lásd: [Data Factory projektre a Visual Studióban](#data-factory-project-in-visual-studio) szakaszban.

## <a name="create-a-pipeline-with-custom-activity"></a>Egyéni tevékenységgel rendelkező folyamat létrehozása
Egyéni tevékenységek létrehozása és a bináris fájlokat a zip-fájl feltöltése a blob-tárolóba egy **általános célú** Azure Storage-fiókot. Ebben a szakaszban egy Azure-beli adat-előállítót egy olyan folyamattal, amely használja az egyéni tevékenység létrehozása.

Az egyéni tevékenység a bemeneti adatkészlet customactivityinput mappájában adftutorial nevű tárolót a blob Storage-blobokat (fájlok) jelöli. A kimeneti adatkészlet a tevékenység kimeneti blobok adftutorial nevű tárolót a blob storage-ban customactivityoutput mappájában jelöli.

Hozzon létre **file.txt** fájlt az alábbi tartalommal, és annak feltöltését az **customactivityinput** mappában található a **adftutorial** tároló. Ha már nem létezik az adftutorial tároló létrehozása

```
test custom activity Microsoft test custom activity Microsoft
```

A bemeneti mappában felel meg az Azure Data Factoryban szelet akkor is, ha a mappa két vagy több fájlt. A folyamat minden egyes szeletet dolgoz fel, ha az egyéni tevékenység végighalad a szelet a bemeneti mappában lévő összes BLOB.

Megjelenik egy adftutorial\customactivityoutput mappában található a fájl kimeneti az egy vagy több sor (ugyanaz, mint a bemeneti mappában lévő blobok száma):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Az alábbiakban a jelen szakaszban végrehajtandó lépések:

1. Hozzon létre egy **adat-előállító**.
2. Hozzon létre **társított szolgáltatásokat** az Azure Batch-készletben található virtuális gépek számára, amely az egyéni tevékenység fut, és az Azure Storage, a bemeneti blobokat tartalmazó.
3. Hozzon létre bemeneti és kimeneti **adatkészletek** , amelyek az és az egyéni tevékenység kimenete.
4. Hozzon létre egy **folyamat** , amely az egyéni tevékenység használja.

> [!NOTE]
> Hozzon létre a **file.txt** és feltöltése a blob-tárolóba, ha ezt még nem tette meg. Tekintse meg az előző szakaszban található.

### <a name="step-1-create-the-data-factory"></a>1. lépés: Az adat-előállító létrehozása
1. Miután bejelentkezett az Azure Portalra, tegye a következőket:
   1. Kattintson a **erőforrás létrehozása** a bal oldali menüben.
   2. Kattintson a **adatok + analitika** a a **új** panelen.
   3. Kattintson a **Data Factory** elemre a **Data analytics** (Adatelemzés) panelen.

    ![Új Azure Data Factory menü](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. Az a **új adat-előállító** panelen adja meg **CustomActivityFactory** neve. Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a hibaüzenetet kapja: **Nem érhető el a Data factory name "CustomActivityFactory"**, módosítsa az adat-előállító nevét (például **yournameCustomActivityFactory**), és próbálkozzon újra a létrehozással.

    ![Új Azure Data Factory panel](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Kattintson a **ERŐFORRÁSCSOPORT-név**, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot.
4. Győződjön meg arról, hogy használja a megfelelő **előfizetés** és **régió** hol szeretné létrehozni az adat-előállító.
5. Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.
6. A létrehozás alatt álló data factory láthatja a **irányítópult** az Azure Portal.
7. Miután az adat-előállító sikeresen létrejött, megjelenik a Data Factory panelre, amely megjeleníti az adat-előállító tartalmát.

    ![A Data Factory panel](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>2. lépés: Társított szolgáltatások létrehozása
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. Ebben a lépésben társítani fogja az Azure Storage-fiók és az Azure Batch-fiókot az adat-előállítóhoz.

#### <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása
1. Kattintson a **Szerző és üzembe helyezése** csempét a **adat-előállító** paneljén **CustomActivityFactory**. Ekkor megjelenik a Data Factory Editor.
2. Kattintson a **új adattároló** a parancs sávot, és válassza **az Azure storage**. A szerkesztőben megjelenik az Azure Storage társított szolgáltatás létrehozására szolgáló JSON-parancsfájl.

    ![Új adattár – Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Cserélje le `<accountname>` Azure storage-fiók nevére és `<accountkey>` az Azure storage-fiók hozzáférési kulccsal. A tárelérési kulcs lekérésével kapcsolatos információk: [Tárelérési kulcsok megtekintése, másolása és újragenerálása](../../storage/common/storage-account-manage.md#access-keys).

    ![Az Azure Storage szolgáltatás tetszett](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére.

#### <a name="create-azure-batch-linked-service"></a>Azure Batch társított szolgáltatás létrehozása
1. A Data Factory Editorban kattintson **... További** a parancssávon kattintson **új számítási**, majd válassza ki **Azure Batch** a menüből.

    ![Új számítás – Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. A JSON-parancsfájl hajtsa végre a következő módosításokat:

   1. Adja meg az Azure Batch-fiók nevét a **accountName** tulajdonság. A **URL-cím** származó a **Azure Batch-fiók panel** van a következő formátumban: `http://accountname.region.batch.azure.com`. Az a **batchUri** a JSON-tulajdonságot, el kell távolítania `accountname.` az URL-cím és a a `accountname` számára a `accountName` JSON-tulajdonságot.
   2. Adja meg az Azure Batch-fiók kulcsa az az **accessKey** tulajdonság.
   3. Adja meg a létrehozott készlet nevét, az Előfeltételek részeként a **poolName** tulajdonság. Emellett megadhatja a készlet neve helyett a készlet azonosítója.
   4. Adja meg az Azure Batch URI az az **batchUri** tulajdonság. Példa: `https://westus.batch.azure.com`.
   5. Adja meg a **AzureStorageLinkedService** számára a **linkedServiceName** tulajdonság.

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

       Az a **poolName** tulajdonságot használja, azt is megadhatja a készlet neve helyett a készlet azonosítója.

### <a name="step-3-create-datasets"></a>3. lépés: Adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre, bemeneti és kimeneti adatokat képviselik.

#### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása
1. A Data Factory **szerkesztőjében** kattintson a **... További** a parancssávon kattintson **új adatkészlet**, majd válassza ki **Azure Blob storage** a legördülő menüből.
2. Cserélje le a JSON a jobb oldali panelen a következő JSON-kódrészletre:

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

   Ebből az útmutatóból kezdő időpont későbbi részében létrehoz egy folyamatot: 2016-11-16T00:00:00Z és a záró időpont: 2016-11-16T05:00:00Z. Ütemezés szerint állítja elő a adatokat óránként, hogy öt bemeneti/kimeneti szeletek (közötti **00**: 00:00 -> **05**: 00:00).

   A **gyakorisága** és **időköz** a bemeneti adatkészlet beállítása **óra** és **1**, ami azt jelenti, hogy a bemeneti szelet érhető el Óránként. Ebben a példában a fájl (file.txt) a intputfolder.

   Az alábbiakban a kezdési idejének az egyes a szeletekhez a SliceStart rendszerváltozó a fenti JSON-kódrészletben képviseli.
3. Kattintson a **telepítés** létrehozása és üzembe helyezése az eszköztáron a **InputDataset**. Győződjön meg arról, hogy a szerkesztő címsorában megjelenik a **TABLE CREATED SUCCESSFULLY** (A TÁBLA SIKERESEN LÉTREJÖTT) üzenet.

#### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
1. Az a **Data Factory editor**, kattintson a **... További** a parancssávon kattintson **új adatkészlet**, majd válassza ki **Azure Blob storage**.
2. Cserélje le a JSON-parancsfájl, a jobb oldali panelen a következő JSON-parancsfájl:

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

     Kimeneti helye **adftutorial/customactivityoutput/** és a kimeneti fájl nevét az ÉÉÉÉ-HH-NN-HH.txt, ahol az ÉÉÉÉ-HH-NN-HH az év, hónap, dátum és a szelet előállítása óránként. Lásd: [– fejlesztői referencia] [ adf-developer-reference] részleteiről.

    Kimeneti blob/fájl jön létre az egyes bemeneti szeletek. Itt látható, hogy az egyes szeletekhez egy kimeneti fájl neve. A kimeneti fájlok jönnek létre egy kimeneti mappában: **adftutorial\customactivityoutput**.

   | Szelet | Kezdő időpont | Kimeneti fájl |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Ne feledje, hogy a bemeneti mappában lévő összes fájlt a fent említett kezdési idejének a szelet részei. A szeletek feldolgozásakor a rendszer az egyéni tevékenység keresztül minden fájlt átvizsgálja, és hoz létre egy sort a kimeneti fájl a keresési kifejezés ("Microsoft") előfordulásainak száma. A inputfolder három fájl is tartalmaz, van-e három sort az egyes óránkénti szeletek a kimeneti fájl: 2016-11-16:01:00:00.txt 2016-11-16-00.txt, stb.
3. Üzembe helyezéséhez a **OutputDataset**, kattintson a **telepítés** a parancssávon.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Létrehozni és futtatni egy folyamatot, amely használja az egyéni tevékenység
1. A Data Factory Editorban kattintson **... További**, majd válassza ki **új adatcsatorna** a parancssávon.
2. Cserélje le a JSON a jobb oldali panelen a következő JSON-parancsfájl:

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

   * **Egyidejűségi** értékre van állítva **2** úgy, hogy két szelet párhuzamosan dolgozza fel az Azure Batch-készletben 2 virtuális gépen.
   * A tevékenységek szakaszban egyetlen tevékenység van, és típusú: **DotNetActivity**.
   * **AssemblyName** értéke az a dll-fájl neve: **MyDotnetActivity.dll**.
   * **EntryPoint** értékre van állítva **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** értékre van állítva **AzureStorageLinkedService** , amely a blob Storage, az egyéni tevékenység zip-fájlt tartalmazó mutat. Ha más Azure Storage-fiókok a bemeneti és kimeneti fájlok és az egyéni tevékenység zip-fájlt használ, hozzon létre egy másik Azure Storage társított szolgáltatás. Ez a cikk azt feltételezi, hogy ugyanazt az Azure Storage-fiókot használ.
   * **PackageFile** értékre van állítva **customactivitycontainer/MyDotNetActivity.zip**. A következő formátumban van: containerforthezip/nameofthezip.zip.
   * Az egyéni tevékenységek **InputDataset** bemenetként, és **OutputDataset** kimenetként.
   * Az egyéni tevékenység linkedServiceName tulajdonságát mutat a **AzureBatchLinkedService**, amely tájékoztatja, hogy az Azure Data Factory, amelyet az egyéni tevékenység az Azure Batch virtuális gépen való futtatáshoz.
   * **isPaused** tulajdonsága **hamis** alapértelmezés szerint. A folyamat azonnal fut ebben a példában, mivel a szeletek indítsa el a múltban. Ezzel a tulajdonsággal a folyamat szüneteltetéséhez és állítsa vissza indítsa újra a hamis értéket Igaz értékre.
   * A **start** idő és **záró** értendő **öt** egymástól óra és szeletek előállítása óránként, így öt szelet előállítása a folyamat.
3. A folyamat üzembe helyezéséhez kattintson **telepítés** a parancssávon.

### <a name="monitor-the-pipeline"></a>A folyamat figyelése
1. Az Azure Portal Data Factory panelen kattintson a **Diagram**.

    ![Diagram csempe](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. A Diagram nézet most kattintson az OutputDataset.

    ![Diagramnézet](./media/data-factory-use-custom-activities/diagram.png)
3. Megtekintheti, hogy öt kimeneti szelet kész állapotban van. Ha nem üzemkész állapotba kerül, azok még nem készült még.

   ![Kimeneti szeletek](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Győződjön meg arról, hogy a kimeneti fájlok jönnek létre a a blob storage-ban a **adftutorial** tároló.

   ![egyéni tevékenység kimenete][image-data-factory-output-from-custom-activity]
5. Ha megnyitja a kimeneti fájl, a következő kimenethez hasonló kimenetnek kell megjelennie:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Használja a [az Azure portal] [ azure-preview-portal] vagy az Azure PowerShell-parancsmagok az adat-előállító folyamatok és adatkészletek figyeléséhez. Láthatja az üzenetek a **ActivityLogger** az egyéni tevékenység a naplók (kifejezetten user-0.log), amely innen tölthető le: a portálon vagy a parancsmagok használata a kódban.

   ![naplók letöltése az egyéni tevékenység][image-data-factory-download-logs-from-custom-activity]

Lásd: [folyamatok figyelése és felügyelete](data-factory-monitor-manage-pipelines.md) lépésenkénti leírását adatkészletek és folyamatok figyelése.

## <a name="data-factory-project-in-visual-studio"></a>Data Factory projektre a Visual Studióban
Hozzon létre, és a Data Factory-entitások közzététele a Visual Studio helyett az Azure portal használatával. Részletes információ a és a Data Factory-entitások közzététele a Visual Studio használatával, lásd: [Visual Studio használatával az első folyamat létrehozása](data-factory-build-your-first-pipeline-using-vs.md) és [adatok másolása az Azure-Blobból Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md) cikkek.

A Data Factory-projekt létrehozása a Visual Studióban, hajtsa végre az alábbi kiegészítő lépéseket:

1. A Data Factory projektre a Visual Studio-megoldás, amely tartalmazza az egyéni tevékenység projekt hozzáadása.
2. Vegyen fel egy hivatkozást a .NET-tevékenység projektet a Data Factory-projektből. Kattintson a jobb gombbal a Data Factory projektre, mutasson a **Hozzáadás**, és kattintson a **referencia**.
3. Az a **hivatkozás hozzáadása** párbeszédpanelen válassza ki a **MyDotNetActivity** projektre, majd kattintson a **OK**.
4. Hozhat létre, és közzéteszi a megoldást.

    > [!IMPORTANT]
    > Ha közzéteszi a Data Factory-entitásokat, egy zip-fájlt, automatikusan létrejön, és a blobtárolóba feltöltött: customactivitycontainer. Ha a blob-tároló nem létezik, automatikusan létrejön túl.

## <a name="data-factory-and-batch-integration"></a>Data Factory és a Batch-integráció
A Data Factory szolgáltatás nevű hoz létre egy feladatot az Azure Batch: **adf-poolname: feladat-xxx**. Kattintson a **feladatok** a bal oldali menüből.

![Az Azure Data Factory - Batch-feladatok](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Egy feladat jön létre minden egyes tevékenység futtatásához egy szeletet. Ha készen áll a dolgozhatók öt szeletek, a feladat öt feladatok jönnek létre. Ha több számítási csomóponton a Batch-készletben, két vagy több szeletek is futtatható egyszerre. A számítási csomópontonkénti maximális feladatok > 1 értékre van állítva, ha az azonos számítási futó egynél több szelet is rendelkezhet.

![Az Azure Data Factory - Batch-feladatok](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

Az alábbi ábra az Azure Data Factory és a kötegelt feladatok közötti kapcsolatot.

![A Data Factory és a Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Hibák elhárítása
Néhány alapvető technikából áll:

1. A következő hibát látja, akkor előfordulhat, hogy használata esetén a gyakori és ritka elérésű blobtároló egy általános célú Azure blob storage használata helyett. A zip-fájlt feltölteni egy **általános célú Azure Storage-fiók**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Ha a következő hibát látja, ellenőrizze, hogy az osztály a CS-fájl neve megegyezik-e a megadott név a **EntryPoint** tulajdonság a folyamat JSON-Fájljában. A forgatókönyv az osztály neve van: A következő MyDotNetActivity, és a belépési pont a JSON-fájlban: MyDotNetActivityNS. **MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   A nevei egyeznek, ha győződjön meg arról, hogy a bináris fájlokat, a **gyökérmappa** zip-fájlban. Azt jelenti amikor megnyitja a zip-fájlt, megtekintheti az összes fájl a gyökérmappában található, nem pedig a bármely almappákban.
3. Ha nincs megadva a bemeneti szelet **készen**, győződjön meg arról, hogy helyesen-e a bemeneti mappa struktúrája és **file.txt** létezik-e a bemeneti mappákban.
3. Az a **Execute** metódus az egyéni tevékenység, használja a **IActivityLogger** objektum naplózza az adatokat, amelyek segítenek elhárítani a problémákat. A naplózott üzenetek jelennek meg az a felhasználó naplófájljaiban (egy vagy több fájlt nevű: user-0.log, user-1.log, user-2.log, stb.).

   Az a **OutputDataset** panelen a szelet megtekintéséhez kattintson a **ADATSZELET** adott szeletre vonatkozó panelen. Látja **tevékenységfuttatások** adott szeletre vonatkozó. A szelet egy tevékenységfuttatás kell megjelennie. Ha a Futtatás gombra a parancssávon, egy másik tevékenység-végrehajtásonként a azonos szelet is elindítható.

   Amikor a tevékenység futtatása gombra kattint, láthatja a **tevékenység Futtatás részletei** naplófájlok listáját tartalmazó panelen. Láthatja a naplózott üzeneteket a user_0.log fájlban. Ha hiba történik, akkor három tevékenységfuttatások megtekintéséhez, mert az újrapróbálkozások számának értéke 3, a folyamat/tevékenység a JSON. Amikor a tevékenység futtatása gombra kattint, láthatja a naplófájlokat a hiba elhárítása tekinthető meg.

   A naplófájlok a listában, kattintson a **user-0.log**. A jobb oldali panelen az eredményeket a rendszer a **IActivityLogger.Write** metódust. Ha nem látja az összes üzenetet, ellenőrizze, hogy nevű további naplófájlok: user_1.log, user_2.log stb. Ellenkező esetben a kódot nem sikerült az utolsó üzenet naplózása után.

   Emellett ellenőrizze **system-0.log** bármilyen rendszer hibaüzenetek és a kivételek.
4. Tartalmazza a **PDB** fájlt a zip-fájlt, hogy a hiba részletei információkat például **hívási verem** hiba bekövetkezésekor.
5. Az egyéni tevékenység zip-fájljában lévő összes fájlnak a **legfelső szinten** kell lennie, almappák nélkül.
6. Ügyeljen arra, hogy a **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip), és **packageLinkedService** (kell mutatnia a **általános célú**a zip-fájlt tartalmazó Azure blob storage) megfelelő értékre van állítva.
7. Ha kijavított egy hibát, és újra fel szeretné dolgozni a szeletet, kattintson a jobb gombbal a szeletre az **OutputDataset** panelen, és kattintson a **Futtatás** parancsra.
8. A következő hibát látja, ha a verzió > 4.3.0 verzióban az Azure Storage csomagot használja. Data Factory szolgáltatás indítója WindowsAzure.Storage 4.3 verziója szükséges. Lásd: [Appdomain elkülönítési](#appdomain-isolation) szakasz a megkerülő, ha az Azure Storage-szerelvény újabb verzióját kell használnia.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Ha a 4.3.0 verzióban is használhatja az Azure-Storage csomag verziója, távolítsa el a meglévő hivatkozást verzió > 4.3.0 verzióban az Azure Storage-csomaghoz. Ezután futtassa a következő parancsot a NuGet Package Manager Console.

    ```PowerShell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    A projekt felépítése. Törölje a verzió > 4.3.0 verzióban Azure.Storage összeállítása a bin\Debug mappából. Hozzon létre egy zip-fájlba bináris fájljait és a PDB-fájl. Cserélje le a régi zip-fájlt erre a blob-tárolóban (customactivitycontainer). Futtassa újra a sikertelen szeletek (kattintson a jobb gombbal a szeletet, és kattintson a Futtatás).
8. Az egyéni tevékenység nem használja a **app.config** fájlt a csomagból. Ezért ha a kód kapcsolati karakterláncokat olvas a konfigurációs fájlban, nem működik, futtatás közben. Az ajánlott eljárás, ha a titkos kulcsok tárolására az Azure Batch használatával egy **Azure KeyVault**, tanúsítványalapú szolgáltatásnévvel használatával védi a **keyvault**, és terjessze a tanúsítványt az Azure Batch készlet. A .NET egyéni tevékenysége ezután elérheti a titkos kulcsokat a kulcstartóból a futtatáskor. Ez a megoldás egy általános megoldás, és minden olyan titkos kulcsot, nem csak a kapcsolati karakterlánc típusú skálázhatja.

   Van egy egyszerűbb megoldást (de nem ajánlott): létrehozhat egy **Azure SQL társított szolgáltatás** kapcsolatikarakterlánc-beállításokat, hozzon létre egy adatkészletet, amely a társított szolgáltatást használja, és összekapcsolja az adatkészlet egy helyőrző bemeneti adatkészletet, mint a Egyéni .NET-tevékenységet. A társított szolgáltatás kapcsolati karakterlánc az egyéni tevékenység kódban érheti el.

## <a name="update-custom-activity"></a>Egyéni tevékenység módosítása
Ha frissíti a kódot az egyéni tevékenység, építse fel, és a blob Storage új bináris fájlokat tartalmazó zip-fájl feltöltése.

## <a name="appdomain-isolation"></a>Appdomain isolation
Lásd: [Adatbázisközi AppDomain minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) , amely ismerteti, amelyek nem csak a szerelvény verziója a Data Factory indítója által használt egyéni tevékenységek létrehozásához (Példa: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, etc.).

## <a name="access-extended-properties"></a>A hozzáférés további tulajdonságok
A tevékenység JSON az alábbi mintában látható módon kiterjesztett tulajdonságok deklarálhatnak:

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

A példában a rendszer két további tulajdonságok: **SliceStart** és **DataFactoryName**. Az érték a SliceStart a SliceStart rendszerváltozóhoz alapul. Lásd: [rendszerváltozók](data-factory-functions-variables.md) támogatott rendszerváltozók listáját. A DataFactoryName értéke kötelezően CustomActivityFactory.

Ezek a további tulajdonságok a eléréséhez a **Execute** módszer használható kódja a következő kódhoz hasonló:

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

## <a name="auto-scaling-of-azure-batch"></a>Automatikus skálázás az Azure Batch
Az Azure Batch-készlet is létrehozhat **automatikus skálázási** funkció. Létrehozhat például egy azure batch-készletet 0 dedikált virtuális gépek és az automatikus skálázás képletét a függőben lévő feladatok száma alapján.

A mintául szolgáló képlet itt éri el a következő viselkedés: Amikor először hozza létre a készletet, 1 virtuális gép kezdődik. $PendingTasks metrika határozza meg, hogy a feladatok száma futó + (sorban áll) aktív állapotban.  A képlet átlagos száma függőben lévő feladatokat megkeresi az elmúlt 180 másodperc alatt, és ennek megfelelően beállítja a TargetDedicated. Biztosítja, hogy TargetDedicated soha nem túllép 25 virtuális gépeket. Tehát új feladatokat az elküldésüket készlet automatikusan nő befejeződött feladatokat, mint a virtuális gépek ingyenes egyenként válnak és az automatikus skálázás zsugorítja ezeken a virtuális gépeken. igény szerinti startingNumberOfVMs és maxNumberofVMs kell beállítani.

Automatikus skálázási képletet:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Lásd: [automatikusan méretezni a számítási csomópontok az Azure Batch-készletben](../../batch/batch-automatic-scaling.md) részleteiről.

Ha a készletet használja az alapértelmezett [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), a Batch szolgáltatás a virtuális gép előkészítése az egyéni tevékenység futtatása előtt 15 – 30 percet is igénybe vehet.  Ha a készlet egy másik autoScaleEvaluationInterval használ, a Batch szolgáltatás autoScaleEvaluationInterval + 10 percig is eltarthat.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Egyéni tevékenységek létrehozása .NET SDK-val
Ebben a cikkben található útmutatások követéséhez adat-előállító létrehozhat olyan folyamattal, amely az egyéni tevékenység használ az Azure portal használatával. A következő kód bemutatja, hogyan az adat-előállító létrehozása .NET SDK-val helyette. SDK használatával hozhat létre programozott módon a folyamatai kapcsolatos további részleteket talál a [másolási tevékenységgel rendelkező folyamat létrehozása .NET API-val](data-factory-copy-activity-tutorial-using-dotnet-api.md) cikk.

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

## <a name="debug-custom-activity-in-visual-studio"></a>Hibakeresés a Visual Studióban egyéni tevékenység
A [Azure Data Factory – helyi környezetben](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) minta a Githubon tartalmaz olyan eszköz, amely lehetővé teszi, hogy hibakeresése a Visual Studión belül egyéni .NET-tevékenységek.

## <a name="sample-custom-activities-on-github"></a>Egyéni tevékenységek minta a Githubon
| Sample | Milyen egyéni tevékenység does |
| --- | --- |
| [HTTP-adatok letöltési segédprogramja](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). |Adatokat tölt le egy HTTP-végpontot az Azure Blob Storage-egyéni C# tevékenységgel a Data Factoryban. |
| [Twitter-Hangulatelemzés minta](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Meghív egy Azure Machine Learning studio-modell, és tegye hangulatelemzés, pontozási, előrejelzési stb. |
| [R-szkript futtatásához](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). |Hívja meg az R-szkriptet a HDInsight-fürtön, amely már rendelkezik az R telepítve van rajta az RScript.exe futtatásával. |
| [Adatbázisközi AppDomain .NET-tevékenység](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Megjelennek a Data Factory indítója által használt másik szerelvény verziót használja |
| [Az Azure Analysis Services-modell újrafeldolgozása](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Újból feldolgozza a modellben az Azure Analysis Servicesben. |

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

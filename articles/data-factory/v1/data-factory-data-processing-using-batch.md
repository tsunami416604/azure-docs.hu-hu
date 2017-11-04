---
title: "Nagy méretű adatkészletekhez adat-előállító és kötegelt feldolgozni |} Microsoft Docs"
description: "Ismerteti, hogyan lehet feldolgozni egy Azure Data Factory-folyamat túl nagy adatmennyiségek Azure kötegelt párhuzamos feldolgozási képességének használatával."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 2ceef65eaf195b605fada2f8dfe511fe33a5daa0
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="process-large-scale-datasets-using-data-factory-and-batch"></a>Nagyméretű adatkészletek feldolgozása a Data Factory és a Batch használatával
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [egyéni tevékenységeket a Data Factory 2-es](../transform-data-using-dotnet-custom-activity.md).

Ez a cikk ismerteti a minta megoldás, amely helyezi át, és feldolgozza a nagy méretű adatkészletek automatikus és ütemezett módon architektúrát. Egy végpont forgatókönyv használatával az Azure Data Factory és az Azure Batch megoldás megvalósításának is tartalmazza.

Ez a cikk nem hosszabb, mint a szokásos cikk, mert a forgatókönyv egy teljes mintát megoldás tartalmaz. Ha még nem ismeri a kötegelt és a Data Factory megismerheti ezeket a szolgáltatásokat, és hogyan működnek együtt. Ha tudja, valamit a szolgáltatásokkal kapcsolatos, és vannak designing/újratervezni a megoldás, akkor előfordulhat, hogy összpontosítson, csak a a [architektúra szakasz](#architecture-of-sample-solution) a cikk egy prototípus vagy megoldás fejlesztői, is érdemes lehet próbálja ki a részletes utasításokat és a [forgatókönyv](#implementation-of-sample-solution). A Microsoft hívhat meg a tartalmat, és használatának kapcsolatos megjegyzéseit.

Először hogyan adat-előállító és a Batch szolgáltatás segítik a felhőben nagy adatkészletek feldolgozással vizsgáljuk meg.     

## <a name="why-azure-batch"></a>Miért érdemes az Azure Batch?
Az Azure Batch lehetővé teszi, hogy hatékonyan futtasson nagyméretű párhuzamos és nagy teljesítményű feldolgozási (HPC) alkalmazásokat a felhőben. A platformszolgáltatás számításigényes munkák futtatását ütemezi virtuális gépek felügyelt gyűjteményében, és automatikusan képes méretezni a számítási erőforrásokat a feladatok igényeinek megfelelően.

A Batch szolgáltatással Azure számítási erőforrásokat határoz meg az alkalmazások párhuzamos és méretezhető futtatásához. Igény szerinti vagy ütemezett feladatokat futtathat, és nem kell manuálisan létrehoznia, konfigurálnia és felügyelnie a HPC-fürtöket, az egyes virtuális gépeket, virtuális hálózatokat vagy az összetett feladat- és tevékenységütemezési infrastruktúrát.

Lásd az alábbi cikkeket, ha nem ismeri az Azure Batch, segít megérteni a megoldás, a cikkben leírt architektúra/végrehajtását.   

* [Az Azure Batch alapjai](../../batch/batch-technical-overview.md)
* [A Batch funkcióinak áttekintése](../../batch/batch-api-basics.md)

(választható) Azure Batch kapcsolatos további tudnivalókért tekintse meg a [Azure Batch képzési terv](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>Miért érdemes az Azure Data Factory?
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása. A Data Factory szolgáltatással kezelt adatok folyamatok, amelyek tárolt adatok mozgatása a helyszíni és felhőalapú adattároló központosított adattárolóhoz hozhat létre (például: Azure Blob Storage), és a folyamat vagy átalakítási adatok szolgáltatásokat, például Azure HDInsight és az Azure Machine Learning használatával. Beütemezhet adatok folyamatok ütemezett módon (óránkénti, napi, heti, stb.) és a figyelő futtatásához, és kezelheti azokat egy pillanat alatt azonosíthatja a problémákat, és hajtsa végre a műveletet.

Lásd az alábbi cikkeket, ha nem ismeri az Azure Data Factoryvel, segít megérteni a megoldás, a cikkben leírt architektúra/végrehajtását.  

* [Az Azure Data Factory bevezetése](data-factory-introduction.md)
* [Felépítheti első folyamatát adatok](data-factory-build-your-first-pipeline.md)   

(választható) Azure Data Factory kapcsolatos további tudnivalókért tekintse meg a [az Azure Data Factory képzési terv](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>Adat-előállító és kötegelt együtt
Adat-előállító beépített tevékenységeket magában foglalja például a másolási tevékenység során történő másolására/áthelyezésére adatok cél adattároló forrás adattároló és a Hadoop-fürtök (HDInsight) használata az Azure-on adatfeldolgozásra történő Hive tevékenység. Lásd: [adatok átalakítása tevékenységek](data-factory-data-transformation-activities.md) támogatott átalakítása tevékenységek listáját.

Lehetővé teszi egyéni .NET tevékenység áthelyezése vagy dolgoz fel adatokat a saját logikával, és ezek a tevékenységek futtatásához Azure HDInsight-fürtöt, vagy a virtuális gépek Azure Batch-készlet létrehozását. Azure Batch használatakor konfigurálhatja az automatikus méretezése a készletet (hozzáadása vagy eltávolítása a virtuális gépek, a munkaterhelés alapján), adja meg a képlet alapján.     

## <a name="architecture-of-sample-solution"></a>A minta megoldás architektúrája
Annak ellenére, hogy ebben a cikkben leírt architektúra a legegyszerűbb megoldás az, az összetett forgatókönyvek esetében például a pénzügyi szolgáltatásokat, kép feldolgozási és megjelenítési és genomikus elemzés modellezési kockázat kapcsolódik.

Az ábrán látható, 1) hogyan koordinálja a Data Factory adatmozgatást és a feldolgozás és a 2.) hogyan Azure Batch dolgozza fel az adatok párhuzamos módon. Letölthető és kinyomtatható a az egyszerűbb használat (11 x 17. vagy A3 méret): [Azure Batch és a Data Factory használatával HPC és az orchestration](http://go.microsoft.com/fwlink/?LinkId=717686).

[![A nagyméretű adatok feldolgozása diagramja](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

Az alábbi lista ismerteti a folyamat alapvető lépéseit. A megoldás tartalmaz a kódot és magyarázataik a végpont megoldás kiépítését.

1. **Konfigurálja az Azure Batch számítási csomópontok (VM) készletét**. A csomópontok száma és mérete az egyes csomópontok is megadhat.
2. **Hozzon létre egy Azure Data Factory példányt** , amelyek megfelelnek az Azure blob storage, Azure Batch számítási szolgáltatás, bemeneti adatokat és egy munkafolyamat/folyamat olyan tevékenységet, amely áthelyezése és az adatok átalakítása entitások van konfigurálva.
3. **Hozzon létre egy egyéni .NET tevékenység a Data Factory-folyamathoz**. A tevékenység a felhasználói kód, amely fut az Azure Batch-készlet.
4. **A bemeneti adatok nagy mennyiségű tárolót, mint az Azure-tárfiókba blobok**. Adatok osztóval időszeletekre logikai (általában idő).
5. **Adat-előállító másolja át a feldolgozott adatok párhuzamosan** a másodlagos helyre.
6. **Adat-előállító futtatja a kötegelt lefoglalta a készlet használatával egyéni tevékenységet**. Adat-előállító egyidejűleg futtatható tevékenységek. Minden tevékenység adatok szelet dolgozza fel. Az eredményeket az Azure storage tárolja.
7. **Adat-előállító harmadik helyre helyezi át a végső eredmények**, vagy terjesztési keresztül egy alkalmazást, vagy más eszközökkel további feldolgozásra.

## <a name="implementation-of-sample-solution"></a>A minta-megoldás megvalósítása
A minta megoldás szándékosan egyszerű és bemutatják a Data Factory és kötegelt adatkészletek feldolgozni együtt használja. A megoldás egyszerűen megszámlálása egy keresési kifejezés ("Microsoft") előfordulását a bemeneti fájlok idősor rendezve. A számát, hogy a kimeneti fájlok kiírja azt.

**Idő**: Ha ismeri az Azure Data Factory és kötegelt alapjait, és végrehajtotta az alábbi előfeltételek, azt becsléséhez a megoldás 1 – 2 órát vesz igénybe.

### <a name="prerequisites"></a>Előfeltételek
#### <a name="azure-subscription"></a>Azure-előfizetés
Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy ingyenes próbafiók néhány perc alatt. Lásd: [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure Storage-fiók
Egy Azure storage-fiókot használ az adatok tárolása az oktatóanyag. Ha egy Azure storage-fiók nem rendelkezik, tekintse meg [hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md#create-a-storage-account). A megoldást használja a blob Storage tárolóban.

#### <a name="azure-batch-account"></a>Azure Batch-fiók
Hozzon létre egy Azure Batch fiók használata a [Azure-portálon](http://portal.azure.com/). Lásd: [létrehozása és kezelése az Azure Batch-fiók](../../batch/batch-account-create-portal.md). Megjegyzés: az Azure Batch nevét és a fiók kulcsot. Is [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) parancsmaggal hozhat létre Azure Batch-fiók. Lásd: [Ismerkedés az Azure Batch PowerShell-parancsmagok](../../batch/batch-powershell-cmdlets-get-started.md) részletes útmutató a parancsmag használatával.

A minta megoldás adatfeldolgozásra történő Azure Batch (közvetve keresztül egy Azure Data Factory-folyamathoz) használ a számítási csomópontok (a virtuális gépek felügyelt gyűjteménye) készlete egy párhuzamos módon.

#### <a name="azure-batch-pool-of-virtual-machines-vms"></a>A virtuális gépek (VM) az Azure Batch-készlet
Hozzon létre egy **Azure Batch-készlet** legalább 2-es számítási csomópontjain.

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a **Tallózás** a bal oldali menüben, majd kattintson a **Batch-fiókok**.
2. Válassza ki az Azure Batch-fiók megnyitása a **Batch-fiók** panelen.
3. Kattintson a **készletek** csempére.
4. Az a **készletek** panelen kattintson a Hozzáadás gombra az eszköztáron a készlet hozzáadása gombra.
   1. Adja meg a készlet Azonosítóját (**alkalmazáskészlet-azonosító**). Megjegyzés: a **a készlet Azonosítóját**; kell azt a Data Factory megoldás létrehozásakor.
   2. Adja meg **Windows Server 2012 R2** az operációsrendszer-családot beállítás.
   3. Válassza ki a **csomóponti tarifacsomagot**.
   4. Adja meg **2** , értékét a **cél dedikált** beállítást.
   5. Adja meg **2** , értékét a **csomópontonkénti tevékenységek maximális** beállítást.
   6. A készlet létrehozásához kattintson az **OK** gombra.

#### <a name="azure-storage-explorer"></a>Azure Storage Explorer
[Az Azure Storage Explorer 6 (eszköz)](https://azurestorageexplorer.codeplex.com/) vagy [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (a ClumsyLeaf szoftver). Ezek az eszközök használata Kérem, és módosítsa az adatokat az Azure Storage-projektek a felhőben üzemeltetett alkalmazások naplófájlokkal együtt.

1. Hozzon létre egy nevű tárolót **mycontainer** privát hozzáférést (nincs névtelen hozzáférés)
2. Ha használ **CloudXplorer**, hozzon létre mappákban és almappáiban az alábbi szerkezettel:

   ![](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder`és `outputfolder` a legfelső szintű mappák `mycontainer`. A `inputfolder` almappái dátum-időbélyegeket (éééé-hh-nn-ÓÓ) rendelkezik.

   Ha használ **Azure Tártallózó**, a következő lépéssel kell névvel rendelkező fájlok feltöltése: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt` és így tovább. Ez a lépés automatikusan a mappákat hoz létre.
3. Hozzon létre egy szövegfájlt **file.txt** a számítógépre, amelyen a kulcsszó tartalmú **Microsoft**. Például: "egyéni tevékenység Microsoft teszt egyéni tevékenység Microsoft teszt".
4. A fájl feltöltése az Azure blob storage a következő bemeneti mappákhoz.

   ![](./media/data-factory-data-processing-using-batch/image4.png)

   Ha használ **Azure Tártallózó**, a fájl feltöltése **file.txt** való **mycontainer**. Kattintson a **másolási** másolatot készít a blob az eszköztáron. Az a **másolási Blob** párbeszédpanelen módosítsa a **cél blob neve** való `inputfolder/2015-11-16-00/file.txt`. Ismételje meg ezt a lépést létrehozása `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt` és így tovább. Ez a művelet automatikusan létrehozza a mappákat.
5. Hozzon létre egy másik tárolót: `customactivitycontainer`. Az egyéni tevékenység zip fájlt feltölteni a tárolóban.

#### <a name="visual-studio"></a>Visual Studio
Telepítse a Microsoft Visual Studio 2012 vagy újabb a egyéni kötegelt tevékenység használható a Data Factory megoldás létrehozásához.

### <a name="high-level-steps-to-create-the-solution"></a>Magas szintű lépései a megoldás létrehozása
1. Hozzon létre egy egyéni tevékenység, amely az adatok feldolgozása logikáját tartalmazza.
2. Hozzon létre egy Azure data factory használja az egyéni tevékenység:

### <a name="create-the-custom-activity"></a>Az egyéni tevékenység létrehozása
A Data Factory egyéni tevékenység Ez a minta-megoldás kulcsfontosságú. A minta megoldás Azure Batch az egyéni tevékenység futtatásához használ. Lásd: [egyéni tevékenységeket használni egy Azure Data Factory-folyamathoz](data-factory-use-custom-activities.md) fejlesztése egyéni tevékenységeket, és használhatja őket az Azure Data Factory folyamatok alapvető információ megadása.

Egy Azure Data Factory-folyamathoz használható .NET egyéni tevékenységek létrehozásához szüksége a **.NET Class Library** egy osztály, amely megvalósítja az, hogy a projekt **IDotNetActivity** felületet. Ez az interfész csak egy metódusa: **Execute**. Ez a metódus aláírása:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

A metódushoz meg kell ismernie néhány kulcsfontosságú összetevők.

* A metódus négy paramétereket fogadja:

  1. **linkedServices**. Egy enumerálható listája, amely a bemeneti/kimeneti adatforrások összekapcsolt szolgáltatások (például: Azure Blob Storage) az adat-előállító. Ez a példa nincs Azure Storage használt bemeneti és kimeneti típusú csak egy társított szolgáltatást.
  2. **adatkészletek**. A rendszer egy enumerálható állnak. A helyek és a bemeneti és kimeneti adatkészletek által megadott sémák használhatja ezt a paramétert.
  3. **tevékenység**. Ez a paraméter az aktuális entitásnak számítási – ebben az esetben az Azure Batch szolgáltatás.
  4. **naplózó**. A naplózó lehetővé teszi az adatcsatorna "User" bejelentkezési az adott felület hibakeresési megjegyzések írását.
* A metódus visszaadja a szótár részére láncolni egyéni tevékenységek együtt a jövőben használható. Ez a funkció még nem használható, így egy üres szótár visszaadásának metódus.

#### <a name="procedure-create-the-custom-activity"></a>Az eljárás: Az egyéni tevékenység létrehozása
1. A Visual Studio .NET Class Library projekt létrehozása

   1. Indítsa el **Visual Studio 2012**/**2013 vagy 2015**.
   2. Kattintson a **File** (Fájl) menüre, mutasson a **New** (Új) elemre, és kattintson a **Project** (Projekt) lehetőségre.
   3. Bontsa ki a **sablonok**, és válassza ki **Visual C\#**. Ebben a bemutatóban használhat C\#, de bármely .NET nyelvi használhatja az egyéni tevékenység fejlesztéséhez.
   4. Válassza ki **Class Library** a jobb oldali projekttípusok közül.
   5. Adja meg **MyDotNetActivity** a a **neve**.
   6. Válassza ki **C:\\ADF** a a **hely**. A mappa létrehozása **ADF** Ha még nem létezik.
   7. A projekt létrehozásához kattintson az **OK** gombra.
2. Kattintson az **Eszközök** elemre, mutasson a **NuGet Package Manager** (NuGet-csomagkezelő) lehetőségre, majd kattintson a **Package Manager Console** (Csomagkezelő konzol) elemre.
3. Az a **Csomagkezelő konzol**, hajtsa végre a következő parancsot importálandó **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importálás a **Azure Storage** NuGet-csomagot a projekthez. Mivel ez a példa a Blob storage API kell ezt a csomagot.

    ```powershell
    Install-Package Azure.Storage
    ```
5. Adja hozzá a következő **használatával** irányelvek a forrásfájl, a projektben.

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;
    
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Módosítsa a nevét a **névtér** való **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Változtassa meg az osztály nevét **MyDotNetActivity** és a Származtatás a **IDotNetActivity** csatoló alább látható módon.

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Megvalósítása (Hozzáadás) a **Execute** metódusában a **IDotNetActivity** a csatoló a **MyDotNetActivity** osztályhoz, és másolja az alábbi példakód a metódust. Tekintse meg a [metódus végrehajtása](#execute-method) szakasz a metódusban használt logika ismertetése.

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
    
       // declare types for input and output data stores
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // using First method instead of Single since we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
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
           // with the data slice.
           //
           // definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
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
9. A következő segédmódszereket adhat hozzá az osztályhoz. Ezek a módszerek hívják a **Execute** metódust. A legfontosabb a **Calculate** metódus elkülöníti a kódot, amely minden egyes blob telepítéseket.

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
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
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
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
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
    A **GetFolderPath** metódus visszaadja az elérési út a mappába, amely a DataSet adatkészlet mutat, és a **GetFileName** módszer a/fájlját, hogy az adatkészlet nevét adja vissza.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    A **Calculate** metódus kiszámítja a kulcsszó-példányok száma **Microsoft** a bemeneti fájlok (BLOB a mappában található). A keresési kifejezés ("Microsoft") nem változtatható a kódban.

1. Fordítsa le a projektet. Kattintson a **Build** a menüre, majd a **megoldás fordítása**.
2. Indítsa el **Windows Explorer**, és keresse meg **bin\\debug** vagy **bin\\kiadási** mappa build típusától függően.
3. Hozzon létre egy zip fájlt **MyDotNetActivity.zip** , amely tartalmazza a bináris fájlok a  **\\bin\\Debug** mappát. Érdemes lehet a MyDotNetActivity tartalmazza. **pdb** fájlt úgy, hogy be további részletekről, mint a sor számának a forráskódban, hogy a problémát az okozza, ha hiba lép fel.

   ![](./media/data-factory-data-processing-using-batch/image5.png)
4. Töltse fel **MyDotNetActivity.zip** mint a blobtárolót egy blobot: `customactivitycontainer` az Azure blob-tároló, amely a **StorageLinkedService** társított a szolgáltatás a  **ADFTutorialDataFactory** használja. A blob-tároló létrehozása `customactivitycontainer` Ha még nem létezik.

#### <a name="execute-method"></a>Metódus végrehajtása
Ez a témakör további részleteket és megjegyzéseket fűzhet az Execute metódus kódja.

1. A tagok iteráció a bemeneti gyűjteményben találhatók a [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) névtér. A blob gyűjtemény iteráció kell használni a **BlobContinuationToken** osztály. Lényegében kell használnia a do-ciklus az a jogkivonatot a rendszer a hurokból való kilépés közben. További információkért lásd: [használata a .NET-Blob-tároló](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Alapszintű hurkot itt jelenik meg:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    
                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.
    
     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
    } while (continuationToken != null);

    ```
   A dokumentációban a [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) részletes metódust.
2. A használatához, áttekintve blobok készletét logikailag kódját a ne belül kerül-ciklus során. Az a **Execute** metódust, ne-közben hurok nevű metódus a bináris objektumok listáját adja át **Calculate**. A metódus visszaadja egy karakterlánc-változóvá nevű **kimeneti** , amely rendelkezik a szegmensben lévő összes blobjának keresztül többször eredménye.

   A keresési kifejezés előfordulási adja vissza (**Microsoft**) átadott található a **Calculate** metódus.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. Egyszer a **Calculate** metódus végzett munka, azt kell írni új blob. Így a blobok feldolgozása minden készlete esetében egy új blob írhatók az eredményeket. Egy új blob írni először található a kimeneti adatkészletet.

    ```csharp
    // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. A kódot is meghívja egy segédmetódust: **GetFolderPath** lehet lekérni a mappa elérési útját (a tároló nevét).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   A **GetFolderPath** árnyékot a DataSet adatkészlet-objektum egy AzureBlobDataSet, amelynek nevű FolderPath tulajdonság.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. A kód hívások a **GetFileName** metódusának segítéségével lekérheti a fájlnév (blob neve). A kód hasonlít a fenti kód segítségével kérheti le a mappa elérési útját.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. Egy URI-objektum létrehozása a fájl nevét írja. Az URI konstruktor használja a **BlobEndpoint** tulajdonság vissza a tároló neve. A mappa elérési útját és nevét a kimeneti blob URI összeállításához kerülnek.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. A fájl neve van írva, és most lehet írni a kimeneti karakterláncot, a **Calculate** egy új blob módszert:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>A data factory létrehozása
Az a [az egyéni tevékenység létrehozása](#create-the-custom-activity) szakaszban létrehozott egyéni tevékenység és a zip-fájl bináris fájljait és a PDB-fájl feltöltése egy Azure blob-tárolóhoz. Ebben a szakaszban hoz létre egy Azure **adat-előállító** rendelkező egy **csővezeték** , amely használja a **egyéni tevékenység**.

Az egyéni tevékenység bemeneti adatkészletet jelenti. a blobot (fájlok) a bemeneti mappában (`mycontainer\\inputfolder`) a blob Storage tárolóban. A kimeneti adatkészlet a tevékenység jelenti. a kimeneti blobot, amely a kimeneti mappa (`mycontainer\\outputfolder`) a blob Storage tárolóban.

A bemeneti mappák el egy vagy több fájlokat:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Például dobja el egy fájl (file.txt) a következő tartalommal mindegyik mappákat.

```
test custom activity Microsoft test custom activity Microsoft
```

Minden egyes bemeneti mappa felel meg az Azure Data Factory szelet akkor is, ha a mappa 2 vagy több fájlt. A folyamat minden szelet feldolgozása után az egyéni tevékenység az, hogy a szelet bemeneti mappa összes blobjának telepítéseket.

Megjelenik a kimeneti fájlok öt ugyanahhoz a tartalomhoz. Például a kimeneti fájl nem dolgozza fel a 2015-11-16-00 mappában található a fájl tartalma a következő:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Ha több fájl (file.txt, fájl2.ref fájllal, file3.txt) rendelkező ugyanahhoz a tartalomhoz a bemeneti mappába dobja el, tekintse meg a következő tartalmat a kimeneti fájl. Minden mappa (2015-11-16-00, stb.) megfelel-e ez a példa szelet annak ellenére, hogy a mappa több bemeneti fájlokat tartalmaz.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

A kimeneti fájl három sort, egyet a mappában, a szelet (2015-11-16-00) társított minden egyes bemeneti fájl (blob) rendelkezik.

Egy feladat minden egyes tevékenységfuttatási jön létre. Ez a példa nincs csak egy tevékenység folyamatban. A szelet feldolgozása a folyamat, az egyéni tevékenység fut, a szelet feldolgozása az Azure Batch. Nincsenek öt szeletek (minden szelet rendelkezhet több bináris objektumok vagy fájl), mert nincsenek Azure Batch létrehozott öt feladatok. Kötegelt feladatként fut, esetén ténylegesen fut-e egyéni tevékenység.

A következő forgatókönyv további részleteket biztosít.

#### <a name="step-1-create-the-data-factory"></a>1. lépés: Az adat-előállító létrehozása
1. Való bejelentkezés után a [Azure-portálon](https://portal.azure.com/), hajtsa végre a következő lépéseket:

   1. A bal oldali menüben kattintson a **NEW** (ÚJ) elemre.
   2. Kattintson a **adatok + analitika** a a **új** panelen.
   3. Kattintson a **Data Factory** elemre a **Data analytics** (Adatelemzés) panelen.
2. Az a **új adat-előállító** panelen adjon meg **CustomActivityFactory** nevét. Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a hibaüzenetet kapja: **nem érhető el adat-előállító "CustomActivityFactory"**, változtassa meg a data factory nevét (például **yournameCustomActivityFactory**), és próbálja meg újra létrehozni.
3. Kattintson a **ERŐFORRÁSCSOPORT-név**, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot.
4. Győződjön meg arról, hogy a megfelelő előfizetés és a régióban, amelyben szeretné létrehozni az adat-előállítóban használja.
5. Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.
6. Megjelenik a data factory létrehozása a **irányítópult** az Azure portálról.
7. A data factory sikeres létrehozása után megjelenik a data factory oldal, amely megjeleníti a data factory tartalmát.

   ![](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>2. lépés: A társított szolgáltatások létrehozásához
A társított szolgáltatások adattárakat vagy számítási szolgáltatásokat társítanak az Azure data factoryhez. Ebben a lépésben hivatkozásra a **Azure Storage** fiók és **Azure Batch** a data factory fiókot.

#### <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása
1. Kattintson a **Szerző és központi telepítése** csempét a **adat-előállító** paneljén **CustomActivityFactory**. A Data Factory Editor láthatja.
2. Kattintson a **az új adattároló** a parancs megnyitásához, és válassza a **az Azure storage.** A szerkesztőben megjelenik az Azure Storage társított szolgáltatás létrehozására szolgáló JSON-parancsfájl.

   ![](./media/data-factory-data-processing-using-batch/image7.png)

3. Az **account name** kifejezést cserélje az Azure Storage-fiókja nevére, az **account key** kifejezést pedig az Azure Storage-fiók kulcsára. A tárelérési kulcs lekérésével kapcsolatos információk: [Tárelérési kulcsok megtekintése, másolása és újragenerálása](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére.

   ![](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-azure-batch-linked-service"></a>Csatolt Azure Batch szolgáltatás létrehozása
Ebben a lépésben a társított szolgáltatás létrehozása a **Azure Batch** a Data Factory egyéni tevékenység futtatásához használt fiók.

1. Kattintson a **új számítási** a parancs megnyitásához, és válassza a **Azure Batch.** A JSON-parancsfájl egy csatolt Azure Batch szolgáltatás létrehozásához a szerkesztőben kell megjelennie.
2. A JSON-parancsfájl:

   1. Cserélje le **fióknév** az Azure Batch-fiók nevével.
   2. Cserélje le **hozzáférési kulcs** az Azure Batch-fiók hozzáférési kulcsával.
   3. Adja meg a készlet Azonosítóját a **poolName** tulajdonság**.** Ehhez a tulajdonsághoz adja meg vagy készlet nevét vagy tárolókészlet azonosítóját.
   4. Adja meg a kötegelt URI esetében a **batchUri** JSON tulajdonság.

      > [!IMPORTANT]
      > A **URL-cím** a a **Azure Batch-fiók panelen** a következő formátumban: \<accountname\>.\< régió\>. batch.azure.com. Az a **batchUri** tulajdonság a JSON-ban, kell **távolítsa el a "accountname."** az URL-címből. Példa: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![](./media/data-factory-data-processing-using-batch/image9.png)

      Az a **poolName** tulajdonság, azt is megadhatja a tárolókészlet neve helyett a készlet Azonosítóját.

      > [!NOTE]
      > A Data Factory szolgáltatásnak nem támogatja egy igény szerinti lehetőséget az Azure hdinsight azonban nem. Csak a saját Azure Batch-készlet használhatja az Azure data factory.
      >
      >
   5. Adja meg **StorageLinkedService** a a **linkedServiceName** tulajdonság. Az előző lépésben létrehozott szolgáltatásnak. A tárolót használja a rendszer egy átmeneti területre, fájlok és a naplókat.
3. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére.

#### <a name="step-3-create-datasets"></a>3. lépés: Adatkészletek létrehozása
Ebben a lépésben hoz létre a bemeneti és kimeneti adatok adatkészletek.

#### <a name="create-input-dataset"></a>Bemeneti adatkészlet létrehozása
1. Az a **szerkesztő** a Data Factory kattintson **új adatkészlet** gomb az eszköztáron és a kattintson **Azure Blob Storage tárolóban** a legördülő menüből.
2. A jobb oldali JSON cserélje le a következő JSON kódrészletet:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
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

    Ez a forgatókönyv a kezdő időpont későbbi részében hozzon létre egy folyamatot: 2015-11-16T00:00:00Z és záró idő: 2015-11-16T05:00:00Z. Adatok ütemezett **óránkénti**, így 5 bemeneti/kimeneti szeletek (közötti **00**: 00:00 -\> **05**: 00:00).

    A **gyakoriság** és **időköz** az bemeneti adatkészlet állítsa **óra** és **1**, ami azt jelenti, hogy a bemeneti szelet elérhető óránként.

    Az alábbiakban az indítási idejének az egyes szeletek, ami által képviselt **SliceStart** a fenti JSON-részlet rendszer változót.

    | **Szelet** | **Kezdési idő**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**: 00:00 |
    | 2         | 2015-11-16T**01**: 00:00 |
    | 3         | 2015-11-16T**02**: 00:00 |
    | 4         | 2015-11-16T**03**: 00:00 |
    | 5         | 2015-11-16T**04**: 00:00 |

    A **folderPath** számítja ki a szelet kezdési idő az év, hónap, nap és óra része (**SliceStart**). Ezért ez hogyan egy bemeneti mappa szelet van leképezve.

    | **Szelet** | **Kezdési idő**          | **Bemeneti mappa**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**: 00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**: 00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**: 00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**: 00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**: 00:00 | 2015-11-16-**04** |

1. Kattintson a **telepítés** létrehozása és telepítése az eszköztáron a **InputDataset** tábla.

#### <a name="create-output-dataset"></a>Kimeneti adatkészlet létrehozása
Ebben a lépésben létrehoz egy másik dataset a kimeneti adatok AzureBlob típusú.

1. Az a **szerkesztő** a Data Factory kattintson **új adatkészlet** gomb az eszköztáron és a kattintson **Azure Blob Storage tárolóban** a legördülő menüből.
2. A jobb oldali JSON cserélje le a következő JSON kódrészletet:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
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

    Egy kimeneti blob/fájl az egyes bemeneti szeletek jön létre. Ez hogyan kimeneti fájl neve az egyes szeletek. A kimeneti fájlok akkor jönnek létre, egy kimeneti mappában: `mycontainer\\outputfolder`.

    | **Szelet** | **Kezdési idő**          | **Kimeneti fájlja**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**: 00:00 | 2015-11-16 -**00. txt** |
    | 2         | 2015-11-16T**01**: 00:00 | 2015-11-16 -**01. txt** |
    | 3         | 2015-11-16T**02**: 00:00 | 2015-11-16 -**02. txt** |
    | 4         | 2015-11-16T**03**: 00:00 | 2015-11-16 -**03. txt** |
    | 5         | 2015-11-16T**04**: 00:00 | 2015-11-16 -**04. txt** |

    Ne feledje, hogy egy bemeneti mappában lévő összes fájl (például: 2015-11-16-00) részei a szelet, amelynél a kezdő időpont: 2015-11-16-00. A szelet feldolgozása után az egyéni tevékenység minden fájl megvizsgálja, és hozza létre a keresési kifejezés ("Microsoft") előfordulásainak száma a kimeneti fájl egy sorban. Ha a mappa 2015-11-16-00 három fájlok, vannak-e három sort a kimeneti fájl: 2015-11-16-00.txt.

1. Kattintson a **telepítés** létrehozása és telepítése az eszköztáron a **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-custom-activity"></a>4. lépés: Hozzon létre, és futtatjuk a folyamatot az egyéni tevékenység
Ebben a lépésben létrehoz egy folyamatot egy tevékenységet, a korábban létrehozott egyéni tevékenység.

> [!IMPORTANT]
> Ha még nem töltötte fel a **file.txt** a blob-tároló mappákat a felhasználótól, ehhez a folyamat létrehozása előtt. A **isPaused** tulajdonsága hamis az adatcsatorna JSON-NÁ, így a folyamat a rendszer azonnal futtatja a **start** dátuma a múltban van.
>
>

1. Kattintson a Data Factory Editor **új adatcsatorna** a parancssávon. Ha nem látja a parancsot, kattintson a **... Három (pont)**  látja.
2. A jobb oldali JSON cserélje le a következő JSON-parancsfájl:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Vegye figyelembe a következő szempontokat:

   * Csak egy tevékenység van folyamatban, és típusa, amely nem: **DotNetActivity**.
   * **AssemblyName** értéke a dll-fájl neve: **MyDotNetActivity.dll**.
   * **EntryPoint** értéke **MyDotNetActivityNS.MyDotNetActivity**. Alapvetően van \<névtér\>.\< osztálynév\> a kódban.
   * **PackageLinkedService** értéke **StorageLinkedService** a blob-tároló, amely tartalmazza az egyéni tevékenység zip-fájl mutat. Különböző Azure Storage-fiókok használatakor a bemeneti/kimeneti fájlok és az egyéni tevékenység zip-fájlt kell létrehoznia egy másik Azure tárolás társított szolgáltatása. Ez a cikk feltételezi, hogy a azonos Azure Storage-fiókot használja.
   * **PackageFile** értéke **customactivitycontainer/MyDotNetActivity.zip**. A a formátumban: \<containerforthezip\>/\<nameofthezip.zip\>.
   * Az egyéni tevékenység veszi **InputDataset** bemenetként és **OutputDataset** output típusúként.
   * A **linkedServiceName** tulajdonság az egyéni tevékenység mutat, a **AzureBatchLinkedService**, amely közli az Azure Data Factory, amely az egyéni tevékenység Azure Batch futtatásához szükséges.
   * A **egyidejűségi** beállítás fontos. Ha használja az alapértelmezett érték, amely 1, még akkor is, ha 2 vagy több számítási csomópontok az Azure Batch-készlet, a rendszer feldolgozása egymás után. Ezért nem készítésének Azure kötegelt párhuzamos feldolgozási képességének kihasználása. Ha **egyidejűségi** magasabb értékre, mondja ki 2, az azt jelenti, hogy a két szeletek (felel meg az Azure Batch két feladatot) tudja feldolgozni az egy időben, ebben az esetben, mind a virtuális gépek az Azure Batch készlet ki van használva. Ezért állítsa be megfelelően az egyidejű tulajdonság.
   * Alapértelmezés szerint egyetlen virtuális gép (szelet) csak egy feladat végrehajtása. Az oka, hogy a alapértelmezés szerint a **virtuális gépenként feladatok maximális** az Azure Batch-készlet 1 értékre van állítva. Előfeltételek részeként létrehozott készlet e tulajdonsága 2, így a két adat-előállító szeletek futtathat a virtuális gép egyszerre.

    -   **isPaused** tulajdonsága hamis értékre van beállítva, alapértelmezés szerint. A folyamat a rendszer azonnal futtatja ebben a példában a szeletek indítsa el a régebbi, mert. Ez a tulajdonság igaz értékre a feldolgozási sor szüneteltetése, és állítsa vissza újraindítására hamis beállíthatja.

    -   A **start** idő és **end** alkalommal öt órát egymástól, szeletek előállított óránkénti, így öt szeletek hozzák létre a folyamatot.

1. A folyamat üzembe helyezéséhez kattintson a parancssáv **Deploy** (Üzembe helyezés) elemére.

#### <a name="step-5-test-the-pipeline"></a>5. lépés: A folyamat tesztelése
Ebben a lépésben a folyamat a bemeneti mappákba fájlok ejtésével tesztelése. Kezdjük azokkal a folyamat tesztelése egy bemeneti mappa minden egy fájlba.

1. A Data Factory panelen az Azure portálon kattintson **Diagram**.

   ![](./media/data-factory-data-processing-using-batch/image10.png)
2. A diagram nézetben kattintson duplán a bemeneti adatkészlet: **InputDataset**.

   ![](./media/data-factory-data-processing-using-batch/image11.png)
3. Megjelenik a **InputDataset** mind az öt panelről szeletek készen áll. Figyelje meg a **SZELET kezdete** és **SZELET BEFEJEZÉSÉNEK** az egyes szeletek.

   ![](./media/data-factory-data-processing-using-batch/image12.png)
4. Az a **diagramnézet**, ekkor kattintson a **OutputDataset**.
5. Megtekintheti, hogy a az öt kimeneti szeletek kész állapotban vannak, ha azok már előállítani.

   ![](./media/data-factory-data-processing-using-batch/image13.png)
6. Az Azure portál segítségével megtekintheti a **feladatok** társított a **szeletek** , és ellenőrizze, milyen VM minden szelet futott. Lásd: [adat-előállító és kötegelt integrációs](#data-factory-and-batch-integration) című szakaszban talál információt.
7. A kimenő fájlok kell megjelennie a `outputfolder` a `mycontainer` az Azure blob-tároló.

   ![](./media/data-factory-data-processing-using-batch/image15.png)

   Meg kell jelennie egy, az egyes bemeneti szeletek öt kimeneti fájlokat. A kimeneti fájl minden egyes rendelkeznie kell a következő kimeneti hasonló tartalom:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   A következő ábra bemutatja, hogyan képezi le a Data Factory szeletek Azure Batch feladatai számára. Ebben a példában a szelet csak egy futtató rendelkezik.

   ![](./media/data-factory-data-processing-using-batch/image16.png)
8. Most próbáljuk meg egy mappában több fájlokkal. Fájlok létrehozása: **fájl2.ref fájllal**, **file3.txt**, **file4.txt**, és **file5.txt** az ugyanahhoz a tartalomhoz, mint a mappában file.txt: **2015-11-06-01**.
9. A kimeneti mappában **törlése** a kimeneti fájl: **2015-11-16-01.txt**.
10. Most, a a **OutputDataset** panelen kattintson a jobb gombbal a szeletre **SZELET Kezdés időpontja** beállítása **11/16/2015 01:00:00-kor**, és kattintson **futtassa** újrafuttatása/újra-process a szeletet a. A szelet, öt fájlokat tartalmaz egy fájl helyett.

    ![](./media/data-factory-data-processing-using-batch/image17.png)
11. Után futtatja a szelet állapota pedig **készen**, ellenőrizze a tartalmat a kimeneti fájl a szeletre vonatkozó (**2015-11-16-01.txt**) található a `outputfolder` a `mycontainer` a blob Storage tárolóban. Egy sor minden fájlhoz a szelet kell lennie.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Ha, nem törölte a a kimeneti fájl 2015-11-16-01.txt előtt öt bemeneti fájlokkal, látható az előző szelet futni, hanem egy sor és az aktuális szelet futni, hanem öt sorban. Alapértelmezés szerint a rendszer hozzáfűzi a tartalom kimeneti fájl, ha már létezik.
>
>

#### <a name="data-factory-and-batch-integration"></a>Adat-előállító és kötegelt integrációja
A Data Factory szolgáltatásnak hoz létre egy feladatot az Azure Batch nevű: `adf-poolname:job-xxx`.

![Az Azure Data Factory - kötegelt feladatok](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

A feladat egy feladat minden egyes tevékenység futtatásához a szelet jön létre. Ha készen áll a feldolgozásra 10 szeletek, a feladat 10 feladatok jönnek létre. A párhuzamosan futó, ha több számítási csomópontok a készlet több szelet lehet. Ha a maximális tevékenységek maximális száma a számítási csomópont értéke > 1, az azonos számítási futó egynél több szelet lehet.

Ebben a példában nincsenek öt szeletek, így öt feladatok Azure kötegben. Az a **egyidejűségi** beállítása **5** az adatcsatorna JSON-t Azure Data Factory és **virtuális gépenként feladatok maximális** beállítása **2** az Azure Batch-készlet **2** virtuális gépeket, a feladatok futtatása gyors (Ellenőrizze a kezdő és záró időpontjának feladatok).

A portál segítségével fogja megtekinteni a kötegelt és a társított feladatokat a **szeletek** , és ellenőrizze, milyen VM minden szelet futott.

![Az Azure Data Factory - kötegelt munka feladatai](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>A folyamat hibakeresése
A hibakeresés néhány alapvető technikából áll:

1. Ha nincs beállítva a bemeneti szelet **készen**, győződjön meg arról, hogy a bemeneti gyökérmappa-szerkezetében helyességéről, valamint file.txt szerepel a bemeneti mappákat.

   ![](./media/data-factory-data-processing-using-batch/image3.png)
2. Az a **Execute** metódus az egyéni tevékenység, használja a **IActivityLogger** objektum, amely segít a problémák elhárításához adatok naplózására. A naplózott üzenet jelenik meg a felhasználó\_0. naplófájlt.

   Az a **OutputDataset** panelen a szelet megtekintéséhez kattintson a **ADATSZELET** , hogy a szelet paneljét. Látni **tevékenységek** az adott szeletek. Meg kell jelennie egy tevékenység Futtatás újrapróbálása. Ha **futtatása** a parancssávon, akkor kezdhet a azonos szelet futtassa egy másik tevékenység.

   Ha a tevékenység futtatása gombra kattint, megjelenik a **tevékenység futtatása részletei** naplófájlok listáját tartalmazó panel. A naplózott üzeneteket látja a **felhasználói\_0. napló** fájlt. Ha hiba történik, megjelenik az három tevékenység fut oka az újrapróbálkozások maximális számát a feldolgozási sor/tevékenységben JSON 3 értékre van beállítva. Amikor a tevékenység futtatása gombra kattint, láthatja a hiba elhárítása érdekében tekintse át a naplófájlokat.

   ![](./media/data-factory-data-processing-using-batch/image18.png)

   A naplófájlok, kattintson a **felhasználói-0.log**. A jobb oldali panelen a következők használatával eredményeit a **IActivityLogger.Write** metódust.

   ![](./media/data-factory-data-processing-using-batch/image19.png)

   Ellenőrizze a rendszer-0.log rendszer hibaüzeneteket és kivételeket.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Tartalmazza a **PDB** , hogy a hiba részletei információkat, mint a zip-fájlban szereplő fájl **hívási verem** Ha hiba történik.
4. Az egyéni tevékenység zip-fájlban található összes fájl el kell érnie a **felső szintű** egyetlen almappát.

   ![](./media/data-factory-data-processing-using-batch/image20.png)
5. Győződjön meg arról, hogy a **assemblyName** (MyDotNetActivity.dll) **belépési pont** (MyDotNetActivityNS.MyDotNetActivity) **packageFile** (customactivitycontainer/MyDotNetActivity.zip), és **packageLinkedService** (kell mutatnia, amely a zip-fájl tartalmazza az Azure blob-tároló) helyes az értékük értékre van beállítva.
6. Ha kijavított egy hibát, és újra fel szeretné dolgozni a szeletet, kattintson a jobb gombbal a szeletre az **OutputDataset** panelen, és kattintson a **Futtatás** parancsra.

   ![](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Megjelenik egy **tároló** az Azure Blob Storage nevű: `adfjobs`. Ez a tároló nem törli automatikusan a rendszer, de biztonságos törlése után végzett vizsgálat a megoldás. Hasonlóképpen, a Data Factory megoldást hoz létre egy Azure Batch **feladat** nevű: `adf-\<pool ID/name\>:job-0000000001`. Ez a feladat után ha szeretné tesztelni a megoldás törölhető.
   >
   >
7. Az egyéni tevékenység nem használja a **app.config** fájlt a csomagból. Ezért ha a kódot a kapcsolati karakterláncok a konfigurációs fájlból olvassa be, nem működik futásidőben. Az ajánlott eljárás az Azure Batch használata esetén a titkos kulcsainak tárolásához egy **Azure KeyVault**, egyszerű tanúsítvány-alapú szolgáltatás használható a keyvault védelmére, és az Azure Batch-készlet tanúsítvány terjesztése. A .NET egyéni tevékenysége ezután elérheti a titkos kulcsokat a kulcstartóból a futtatáskor. Ez a megoldás általános egy, és minden olyan titkos kulcsot, nem csak a kapcsolati karakterlánc típusú méretezheti.

    Van egy egyszerűbb megoldást (de nem ajánlott): hozhat létre egy **Azure SQL társított szolgáltatásnak** kapcsolatikarakterlánc-beállításokat, hozzon létre egy adatkészlet által használt társított szolgáltatás, valamint az adatkészlet láncolt, egy üres bemeneti adatkészlet a Egyéni .NET tevékenység. A társított szolgáltatás kapcsolati karakterlánc az egyéni tevékenység kódban érhető el, és azt kell működnek az futásidőben.  

#### <a name="extend-the-sample"></a>A minta kiterjesztése
Ez a minta tudhat meg többet az Azure Data Factory és az Azure Batch-szolgáltatások bővítése. Például egy eltérő időtartományt szeletek feldolgozni, hajtsa végre az alábbi lépéseket:

1. Adja hozzá a következő almappákhoz a `inputfolder`: 2015-11-16-05, 2015-11-16-06 201-11-16-07, 2011-11-16-08, 2015-11-16-09 és helyet adjon meg a mappákban lévő fájlok. Módosítsa a folyamat befejezésének `2015-11-16T05:00:00Z` való `2015-11-16T10:00:00Z`. Az a **diagramnézet**, kattintson duplán a **InputDataset**, és győződjön meg arról, hogy a bemeneti szeletek készen áll-e. Kattintson duplán a **OuptutDataset** kimeneti szeletek állapotának megtekintéséhez. Ha üzemkész állapotban vannak, ellenőrizze a kimeneti mappa kimeneti fájlok tárolásához.
2. Növeli vagy csökkenti a **egyidejűségi** beállítás tudni, hogy milyen hatással van a teljesítmény, a megoldás, különösen akkor fordul elő, az Azure Batch feldolgozását. (Lásd a 4. lépés: hozzon létre, és futtassa a folyamat hosszabb a **egyidejűségi** beállítás.)
3. Hozzon létre egy alkalmazáskészlet magasabb/alacsonyabb **virtuális gépenként feladatok maximális**. A létrehozott új készletet használ, frissítse a kapcsolódó Azure Batch szolgáltatás a Data Factory-megoldásban. (Lásd a 4. lépés: hozzon létre, és futtassa a folyamat hosszabb a **virtuális gépenként feladatok maximális** beállítás.)
4. Az Azure Batch-készlet létrehozása **automatikus skálázás** szolgáltatás. A dinamikusan állítható lesz az feldolgozási kapacitása révén az alkalmazás által használt Azure Batch-készlet számítási csomópontjainak automatikus skálázás. 

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
5. A minta a megoldásban a **Execute** metódus meghívja a **Calculate** módszer, amely feldolgozza a egy bemeneti adatszelet egy kimeneti adatszelet létrehozásához. A bemeneti adatok feldolgozásához, és cserélje le a Calculate metódus hívásához a az Execute metódus a metódus hívásakor a saját metódus lehet írni.

### <a name="next-steps-consume-the-data"></a>További lépések: az adatokat
Adatok feldolgozása után felhasználhat az online eszközökkel, például a **Microsoft Power BI**. Az alábbiakban a hivatkozásokat, amelyekkel jobban megértheti a Power bi-ban, és hogy miképpen lehet vele az Azure-ban:

* [A Power BI dataset felfedezés](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Bevezetés a Power BI Desktop használatába](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Adatok frissítése a Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure és a Power BI – alapvető – áttekintés](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referencia
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Bevezetés az Azure Data Factory szolgáltatásnak](data-factory-introduction.md)
  * [Ismerkedés az Azure Data Factory](data-factory-build-your-first-pipeline.md)
  * [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md)
* [Az Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Az Azure Batch alapjai](../../batch/batch-technical-overview.md)
  * [Azure Batch funkcióinak áttekintése](../../batch/batch-api-basics.md)
  * [Létrehozása és kezelése az Azure portál Azure Batch-fiók](../../batch/batch-account-create-portal.md)
  * [Ismerkedés az Azure Batch Library .NET](../../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx

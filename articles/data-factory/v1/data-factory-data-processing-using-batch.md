---
title: "A nagyméretű adatkészletek feldolgozásához adat-előállító és kötegelt |} Microsoft Docs"
description: "Ismerteti, hogyan lehet egy Azure Data Factory-folyamat túl nagy adatmennyiségek feldolgozni a párhuzamos feldolgozása az Azure Batch funkció használatával."
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
ms.openlocfilehash: c78583ed3de357131c834452b2f07cbcc3a1f7ae
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2018
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Folyamat nagy méretű adatkészletek adat-előállító és kötegelt használatával
> [!NOTE]
> Ez a cikk vonatkozik 1 a Azure Data Factory általánosan elérhető. Ha 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, [egyéni tevékenységek 2-es verzióját a Data factoryban](../transform-data-using-dotnet-custom-activity.md).

Ez a cikk ismerteti a minta megoldás, amely helyezi át, és feldolgozza a nagy méretű adatkészletek automatikus és ütemezett módon architektúrát. Emellett biztosítja a megoldás megvalósításához a Data Factory és az Azure Batch egy végpont forgatókönyv.

Ez a cikk nem hosszabb, mint egy tipikus cikk, mert a forgatókönyv egy teljes mintát megoldás tartalmaz. Ha most ismerkedik a kötegelt és a Data Factory megismerheti ezeket a szolgáltatásokat, és hogyan működnek együtt. Ha tudja, valamit a szolgáltatásokkal kapcsolatos, és vannak designing/újratervezni a megoldás, összpontosíthat a [architektúra szakasz](#architecture-of-sample-solution) a cikk. Ha az egy prototípus vagy egy megoldást, érdemes, részletes utasításait kipróbálásához a [forgatókönyv](#implementation-of-sample-solution). A Microsoft hívhat meg a tartalmat, és használatának kapcsolatos megjegyzéseit.

Első lépésként nézzük hogyan adat-előállító és a Batch szolgáltatás segítségével folyamat nagy adatkészletek a felhőben.     

## <a name="why-azure-batch"></a>Miért érdemes az Azure Batch?
 Használhatja a kötegelt nagyméretű párhuzamos és nagy teljesítményű számítástechnikai (HPC) alkalmazások hatékonyan és méretezhetően fussanak a felhőben. A platform szolgáltatás, amely ütemezi a számítási igényű munkát futtatásához a virtuális gépek (VM) felügyelt gyűjteménye. Az automatikusan át tudja méretezni számítási erőforrásokat, a feladatok igényeinek.

A Batch szolgáltatással Azure számítási erőforrásokat határoz meg az alkalmazások párhuzamos és méretezhető futtatásához. Igény szerinti futtatása vagy ütemezett feladatokat. Nem kell manuálisan létrehozása, konfigurálása és kezelése a HPC-fürt, az egyes virtuális gépek, virtuális hálózatok, vagy egy összetett feladat és a feladatütemezés infrastruktúra.

 Ha nem ismeri a kötegelt, az alábbi cikkek megismerheti a megoldás, a cikkben leírt architektúra/végrehajtására:   

* [Kötegelt alapjai](../../batch/batch-technical-overview.md)
* [A Batch funkcióinak áttekintése](../../batch/batch-api-basics.md)

Másik lehetőségként kötegelt kapcsolatos további információkért lásd: [kötegelt képzési terv](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>Miért érdemes az Azure Data Factory?
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása. Adat-előállító hozhat létre, amely tárolt adatok mozgatása a helyszíni és felhőalapú adattároló központosított adattárolóhoz felügyelt adatok folyamatok. Példa: az Azure Blob Storage tárolóban. Adat-előállító segítségével folyamat vagy átalakítási adatok szolgáltatásokat, például Azure HDInsight és az Azure Machine Learning segítségével. Adatok folyamatok (például óránként, naponta, és heti) ütemezett módon futtatásra is ütemezheti. Figyelheti és kezelheti a folyamatok egy pillanat alatt azonosíthatja a problémákat, és hajtsa végre a műveletet.

  Ha nem ismeri a Data Factory, a következő cikkekben megismerheti a megoldás, a cikkben leírt architektúra/végrehajtására:  

* [Adat-előállító bemutatása](data-factory-introduction.md)
* [Felépítheti első folyamatát adatok](data-factory-build-your-first-pipeline.md)   

Ha szükséges, adat-előállító kapcsolatos további információkért lásd: [Data Factory képzési terv](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>Adat-előállító és kötegelt együtt
Adat-előállító beépített tevékenységeket magában foglalja. Például a Másolás tevékenységgel másolására/áthelyezésére adatok származó adatok céltár forrás adattároló. A Hive tevékenységgel feldolgozni az adatokat az Azure-on Hadoop-fürtök (HDInsight) használatával. Támogatott átalakítása tevékenységek listáját lásd: [adatok átalakítása tevékenységek](data-factory-data-transformation-activities.md).

Egyéni .NET tevékenységeket áthelyezése, illetve feldolgozni az adatokat a saját logikával is létrehozhatja. Ezek a tevékenységek futtathatja egy HDInsight-fürt vagy a virtuális gépek kötegelt készletét. Kötegelt használatakor konfigurálhatja az automatikus skálázás készletben (hozzáadása vagy eltávolítása a virtuális gépek, a munkaterhelés alapján), adja meg a képlet alapján.     

## <a name="architecture-of-a-sample-solution"></a>A minta megoldás architektúrája
  Ebben a cikkben leírt architektúra a legegyszerűbb megoldás szolgál. Egyúttal összetett forgatókönyvek esetében, például a pénzügyi szolgáltatásokat, kép feldolgozási és megjelenítési és genomikus elemzés modellezési kockázat kapcsolódik.

A diagram azt ábrázolja, hogyan koordinálja a Data Factory adatmozgatást és a feldolgozása. Azt is bemutatja, hogyan kötegelt dolgozza fel az adatok párhuzamos módon. Letölthető és kinyomtatható a az egyszerűbb használat (11 x 17 hüvelyk vagy A3 méret). A diagram elérésének, hogy kinyomtathassa, lásd: [HPC és az orchestration kötegelt és a Data Factory használatával](http://go.microsoft.com/fwlink/?LinkId=717686).

[![A nagyméretű adatok feldolgozása diagramja](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

Az alábbi lista ismerteti a folyamat alapvető lépéseit. A megoldás tartalmaz a kódot és magyarázataik a végpont megoldás kiépítését.

* **Konfigurálja a Batch számítási csomópontok (VM) készletét.** Megadhatja, hogy a csomópontok száma és az egyes csomópontok méretét.

* **Hozzon létre egy adat-előállító példányt** , amelyek megfelelnek a blob-tároló, a Batch számítási szolgáltatás, bemeneti adatokat és egy munkafolyamat/folyamat olyan tevékenységet, amely áthelyezése és az adatok átalakítása entitások van konfigurálva.

* **Hozzon létre egy egyéni .NET tevékenység a Data Factory-folyamathoz.** A tevékenység a felhasználói kód a Batch-készlet futó.

* **Nagy mennyiségű bemeneti adatok tárolására az Azure Storage blobként.** Adatok osztóval időszeletekre logikai (általában idő).

* **Adat-előállító másolja át a feldolgozott adatok párhuzamosan** a másodlagos helyre.

* **Adat-előállító kötegelt lefoglalta a készlet használatával az egyéni tevékenység lefut.** Adat-előállító egyidejűleg futtatható tevékenységek. Minden tevékenység adatok szelet dolgozza fel. Az eredmények tárolási vannak tárolva.

* **Adat-előállító egy harmadik helyre helyezi át a végső eredmények** vagy terjesztési keresztül egy alkalmazást, vagy más eszközökkel további feldolgozásra.

## <a name="implementation-of-the-sample-solution"></a>A minta-megoldás megvalósítása
A minta megoldás, szándékosan egyszerű. Úgy van kialakítva, bemutatják a Data Factory és kötegelt használható együtt a folyamat adatkészletek. A megoldás a keresési kifejezés "Microsoft" idősor vannak rendszerezve bemeneti fájlok előfordulását megszámlálása. Majd exportálja a kimeneti fájlok száma.

**Idő:** megismerheti az Azure Data Factory és kötegelt alapjait, és végrehajtotta a következő előfeltételek teljesülését, ha ez a megoldás egy-két órát is igényel.

### <a name="prerequisites"></a>Előfeltételek
#### <a name="azure-subscription"></a>Azure-előfizetés
Ha nem rendelkezik Azure-előfizetéssel, gyorsan létrehozhat egy ingyenes próbafiókot. További információkért lásd: [ingyenes próbaverzió](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure Storage-fiók
A storage-fiók ebben az oktatóanyagban az adatok tárolásához használja. Ha a storage-fiók nem rendelkezik, tekintse meg a [hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md#create-a-storage-account). A megoldást használja a blob Storage tárolóban.

#### <a name="azure-batch-account"></a>Azure Batch-fiók
A Batch-fiók létrehozása a [Azure-portálon](http://portal.azure.com/). További információkért lásd: [létrehozása és kezelése a Batch-fiók](../../batch/batch-account-create-portal.md). Vegye figyelembe a kötegelt nevét és a fiók kulcsot. Is használhatja a [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) parancsmagot, hogy a Batch-fiók létrehozása. Ez a parancsmag használatával, lásd: [Ismerkedés a kötegelt PowerShell-parancsmagok](../../batch/batch-powershell-cmdlets-get-started.md).

A minta megoldás adatfeldolgozásra történő kötegelt (közvetve keresztül a data factory-folyamathoz) használ a számítási csomópontok (a virtuális gépek felügyelt gyűjteménye) készlete egy párhuzamos módon.

#### <a name="azure-batch-pool-of-virtual-machines"></a>A virtuális gépek az Azure Batch-készlet
Hozzon létre egy kötegelt készlet legalább két számítási csomópontok.

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **Tallózás** a bal oldali menüben, és válasszon **Batch-fiókok**.

2. Válassza ki a Batch-fiók megnyitása a **Batch-fiók** panelen.

3. Válassza ki a **készletek** csempére.

4. Az a **készletek** panelen válassza a **Hozzáadás** az eszköztáron a készlet hozzáadása gombra.

   a. Adja meg a készlet Azonosítóját (**alkalmazáskészlet-azonosító**). Megjegyzés: a készlet Azonosítóját. Meg kell a data factory megoldás létrehozásakor.

   b. Adja meg **Windows Server 2012 R2** a a **operációsrendszer-családot** beállítást.

   c. Válassza ki a **csomóponti tarifacsomagot**.

   d. Adja meg **2** értéke a **cél dedikált** beállítást.

   e. Adja meg **2** értéke a **csomópontonkénti tevékenységek maximális** beállítást.

   f. Válassza ki **OK** a készlet létrehozásához.

#### <a name="azure-storage-explorer"></a>Azure Storage Explorer
Használja [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) vagy [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (a ClumsyLeaf szoftver), és módosítsa az adatokat tároló projektjeikbe. Is vizsgálja meg és módosítsa az adatok a felhőben üzemeltetett alkalmazások rögzít.

1. Hozzon létre egy nevű tárolót **mycontainer** privát hozzáférést (nincs névtelen hozzáférés).

2. Ha CloudXplorer használja, hozzon létre mappákban és almappáiban az alábbi szerkezettel:

   ![Mappa-és almappák](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder`és `outputfolder` a legfelső szintű mappák `mycontainer`. A `inputfolder` mappa almappákat dátum-időbélyegeket (éééé-hh-nn-ÓÓ) rendelkezik.

   Ha Tártallózó, használja a következő lépésben, a következő nevű feltöltött: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt`, és így tovább. Ez a lépés automatikusan a mappákat hoz létre.

3. Hozzon létre egy szövegfájlt **file.txt** a számítógépre, amelyen a kulcsszó tartalmú **Microsoft**. Példa: "egyéni tevékenység Microsoft teszt egyéni tevékenység Microsoft teszt."

4. A fájl feltöltése a blob Storage tárolóban a következő bemeneti mappák:

   ![Bemeneti mappák](./media/data-factory-data-processing-using-batch/image4.png)

   A Tártallózó használatakor töltse fel a **file.txt** fájl **mycontainer**. Válassza ki **másolási** másolatot készít a blob az eszköztáron. Az a **másolási Blob** párbeszédpanelen módosítsa a **cél blob neve** való `inputfolder/2015-11-16-00/file.txt`. Ismételje meg ezt a lépést létrehozása `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt`, és így tovább. Ez a művelet automatikusan létrehozza a mappákat.

5. Hozzon létre egy másik tároló `customactivitycontainer`. Az egyéni tevékenység zip-fájl feltöltése a tárolóban.

#### <a name="visual-studio"></a>Visual Studio
Telepítse a Visual Studio 2012 vagy újabb a egyéni kötegelt tevékenység használható a data factory megoldás létrehozásához.

### <a name="high-level-steps-to-create-the-solution"></a>Magas szintű lépései a megoldás létrehozása
1. Hozzon létre egy egyéni tevékenység, amely az adatok feldolgozása logikáját tartalmazza.

2. Használja az egyéni tevékenység adat-előállító létrehozása.

### <a name="create-the-custom-activity"></a>Az egyéni tevékenység létrehozása
A data factory egyéni tevékenység Ez a minta-megoldás kulcsfontosságú. A minta megoldás kötegelt az egyéni tevékenység futtatásához használ. Egyéni tevékenységek fejlesztése és használja azokat az adatokat előállító adatcsatornák kapcsolatos információkért lásd: [egyéni tevékenységeket felhasználni a data factory-folyamathoz](data-factory-use-custom-activities.md).

A data factory-folyamathoz használható .NET egyéni tevékenységek létrehozásához, amely megvalósítja a IDotNetActivity felületet osztállyal hoz létre egy .NET hordozhatóosztálytár-projektjének. Ez az interfész csak egy metódusa: hajtható végre. Ez a metódus aláírása:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

A metódushoz meg kell ismernie néhány kulcsfontosságú összetevők:

* A metódus négy paramétereket fogadja:

  * **linkedServices**. Ez a paraméter egy-egy bemeneti/kimeneti adatforrások (például blob-tároló) az adat-előállítóban hivatkozó csatolt szolgáltatások enumerálható lista. Ez a példa nincs Azure Storage használt bemeneti és kimeneti típus csak egy kapcsolódó szolgáltatás.
  * **adatkészletek**. Ez a paraméter egy-egy enumerálható lista állnak. A helyek és a bemeneti és kimeneti adatkészletek által megadott sémák használhatja ezt a paramétert.
  * **tevékenység**. Ez a paraméter az aktuális entitásnak számítási. Ebben az esetben a Batch szolgáltatás is.
  * **logger**. A naplózó segítségével írható hibakeresési megjegyzéseket, hogy a felület a "User" napló a következő feldolgozási sor.
* A metódus visszaadja a szótár részére láncolni egyéni tevékenységek együtt a jövőben használható. Ez a funkció még nincs implementálva kerül, ezért csak egy üres szótár metódusa.

#### <a name="procedure-create-the-custom-activity"></a>Az eljárás: Az egyéni tevékenység létrehozása
1. A .NET hordozhatóosztálytár-projektjének létrehozása a Visual Studióban.

   a. Indítsa el a Visual Studio 2012/2013 vagy 2015-öt.

   b. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.

   c. Bontsa ki a **sablonok**, és válassza ki **Visual C\#**. Ebben a bemutatóban használhat C\#, de bármely .NET nyelvi használhatja az egyéni tevékenység fejlesztéséhez.

   d. Válassza ki **Class Library** a jobb oldali projekttípusok közül.

   e. Adja meg **MyDotNetActivity** a a **neve**.

   f. Válassza ki **C:\\ADF** a a **hely**. A mappa létrehozása **ADF** Ha még nem létezik.

   g. Válassza ki **OK** a projekt létrehozásához.

2. Válassza ki **eszközök** > **NuGet-Csomagkezelő** > **Csomagkezelő konzol**.

3. A Package Manager Console hajtsa végre a következő parancsot Microsoft.Azure.Management.DataFactories importálása:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importálás a **Azure Storage** NuGet-csomagot a projektben. Mivel ez a példa a Blob Storage API-t kell ezt a csomagot:

    ```powershell
    Install-Package Azure.Storage
    ```
5. Adja hozzá a következő irányelvek segítségével a forrásfájl, a projekt:

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
6. Módosítsa a névtér nevét **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Változtassa meg az osztály nevét **MyDotNetActivity**, és a Származtatás a **IDotNetActivity** csatoló látható módon:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. (Hozzáadás) megvalósítása a **Execute** metódusában a **IDotNetActivity** a csatoló a **MyDotNetActivity** osztály. Másolja az alábbi példakód a metódust. A módszer azon logika egy ismertetése a [metódus végrehajtása](#execute-method) szakasz.

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {
    
       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.
    
       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
       // Initialize the continuation token before using it in the do-while loop.
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
    
           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);
    
       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
9. A következő segédmódszereket adhat hozzá az osztályhoz. Ezek a módszerek hívják a **Execute** metódust. Legfontosabb, a **Calculate** metódus elkülöníti a kódot, amely minden egyes blob telepítéseket.

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
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
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
    A GetFolderPath metódus visszaadja az elérési út a mappába, amely a DataSet adatkészlet mutat, és a GetFileName metódus a/fájlját, hogy az adatkészlet nevét adja vissza.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    A Calculate metódus a következő kulcsszó: "Microsoft" a bemeneti fájlok (a mappában található a bináris objektumok) a példányok száma számítja ki. A keresési kifejezés "Microsoft" nem változtatható a kódban.

10. Fordítsa le a projektet. Válassza ki **Build** a menüben, majd válassza ki a **megoldás fordítása**.

11. Indítsa el a Windows Intézőt, és válassza a **bin\\debug** vagy **bin\\kiadási** mappát. A mappa build típusú függ.

12. Hozzon létre egy zip fájlt **MyDotNetActivity.zip** , amely tartalmazza a bináris fájlok a  **\\bin\\Debug** mappát. Érdemes a MyDotNetActivity tartalmazza. **pdb** fájlt úgy, hogy be további részletekről, mint a sor száma a forráskódban, hogy a problémát az okozza, ha hiba lép fel.

   ![A bin\Debug Mappalista](./media/data-factory-data-processing-using-batch/image5.png)

13. Töltse fel **MyDotNetActivity.zip** mint a blobtárolót egy blobot `customactivitycontainer` a blob Storage tárolóban, hogy a StorageLinkedService társítva ADFTutorialDataFactory használja a szolgáltatást. A blob-tároló létrehozása `customactivitycontainer` Ha még nem létezik.

#### <a name="execute-method"></a>Metódus végrehajtása
Ez a szakasz ismerteti az Execute metódus kódja további részleteit.

1. A tagok iteráció a bemeneti gyűjteményben találhatók a [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) névtér. Az iterációt a blob-gyűjtemény, módosítania kell használni a **BlobContinuationToken** osztály. Lényegében kell használnia a do-ciklus az a jogkivonatot a rendszer a hurokból való kilépés közben. További információkért lásd: [használja a Blob storage a .NET-](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Alapszintű hurkot itt jelenik meg:

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
   További információ a dokumentációban a a [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) metódust.

2. A használatához, áttekintve blobok készletét logikailag kódját a ne belül kerül-ciklus során. Az a **Execute** metódust, ne-közben hurok nevű metódus a bináris objektumok listáját adja át **Calculate**. A metódus visszaadja egy karakterlánc-változóvá nevű **kimeneti** , amely rendelkezik a szegmensben lévő összes blobjának keresztül többször eredménye.

   A keresési kifejezés található a "Microsoft" átadott előfordulási adja vissza a **Calculate** metódust.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. Miután a **Calculate** metódus befejeződött, azt kell írni új blob. Az eredmények blobok feldolgozása minden készlete esetében egy új blob írhatók. Egy új blob írni először található a kimeneti adatkészletet.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. A kódot is meghívja segédmetódus **GetFolderPath** lehet lekérni a mappa elérési útját (a tároló nevét).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   A GetFolderPath metódus árnyékot a DataSet adatkészlet-objektum egy AzureBlobDataSet, amelynek nevű FolderPath tulajdonság.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. A kód hívások a **GetFileName** metódusának segítéségével lekérheti a fájlnév (blob neve). A kód hasonlít a mappa elérési útjának használt előző kódot.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. Egy URI-objektum létrehozása a fájl nevét írja. Az URI konstruktor használja a **BlobEndpoint** tulajdonság vissza a tároló neve. A mappa elérési útját és nevét a kimeneti blob URI összeállításához kerülnek.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. A fájl neve írása, után írhat a kimeneti karakterláncot, a **Calculate** egy új blob módszert:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>A data factory létrehozása
Az a [az egyéni tevékenység létrehozása](#create-the-custom-activity) szakaszban létrehozott egyéni tevékenység és a zip-fájl bináris fájljait és a PDB-fájl feltöltése egy blob-tárolóba. Ebben a szakaszban hoz létre egy adat-előállító egy folyamatot, amely az egyéni tevékenység használja.

Az egyéni tevékenység bemeneti adatkészletet jelenti. a blobot (fájlok) a bemeneti mappában (`mycontainer\\inputfolder`) a blob Storage tárolóban. A kimeneti adatkészlet a tevékenység jelenti. a kimeneti blobot, amely a kimeneti mappa (`mycontainer\\outputfolder`) a blob Storage tárolóban.

A bemeneti mappákba dobja el egy vagy több fájlt:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Például dobja el egy fájl (file.txt) a következő tartalommal mindegyik mappák:

```
test custom activity Microsoft test custom activity Microsoft
```

Minden egyes bemeneti mappa felel meg a szelet adat-előállító akkor is, ha a mappa két vagy több fájlt. A folyamat minden szelet feldolgozása után az egyéni tevékenység az, hogy a szelet bemeneti mappa összes blobjának telepítéseket.

Megjelenik a kimeneti fájlok öt ugyanahhoz a tartalomhoz. Például a kimeneti fájl nem dolgozza fel a 2015-11-16-00 mappában található a fájl tartalma a következő:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Ha több fájl (file.txt, fájl2.ref fájllal, file3.txt) rendelkező ugyanahhoz a tartalomhoz a bemeneti mappába dobja el, lásd: a következő tartalmat a kimeneti fájl. Minden mappa (2015-11-16-00, stb.) megfelel-e ez a példa szelet annak ellenére, hogy a mappa több bemeneti fájlokat tartalmaz.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

A kimeneti fájl három sort, egyet a mappában, a szelet (2015-11-16-00) társított minden egyes bemeneti fájl (blob) rendelkezik.

Egy feladat minden egyes tevékenységfuttatási jön létre. Ez a példa nincs csak egy tevékenység folyamatban. A szelet feldolgozása a folyamat, az egyéni tevékenység fut a szelet feldolgozása kötegelt. Mivel öt szeletek (minden szelet rendelkezhet több bináris objektumok vagy fájl), öt feladatok kötegelt jönnek létre. Kötegelt feladatként fut, esetén az egyéni tevékenység, hogy fut-e.

A következő forgatókönyv további részleteket biztosít.

#### <a name="step-1-create-the-data-factory"></a>1. lépés: Az adat-előállító létrehozása
1. Miután bejelentkezik a [Azure-portálon](https://portal.azure.com/), tegye a következőket:

   a. Válassza ki **új** a bal oldali menüben.

   b. Válassza ki **adatok + analitika** a a **új** panelen.

   c. Válassza ki **adat-előállító** a a **adatelemzés** panelen.

2. Az a **új adat-előállító** panelen adjon meg **CustomActivityFactory** nevét. Az adat-előállító nevének globálisan egyedinek kell lennie. Ha hibaüzenetet kap, a "adat-előállító CustomActivityFactory nem érhető el", módosítsa az adat-előállító nevét. Például yournameCustomActivityFactory használja, és hozza létre újra az adat-előállítóban.

3. Válassza ki **ERŐFORRÁSCSOPORT-név**, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot.

4. Győződjön meg arról, hogy az egyes előfizetésekhez és hol szeretné létrehozni az adat-előállítóban régió helyesek.

5. Válassza ki **létrehozása** a a **új adat-előállító** panelen.

6. Az adat-előállítóban a portál irányítópultján jön létre.

7. Az adat-előállítóban sikeres létrehozása után megjelenik a **adat-előállító** lap, amely jelzi, hogy a data factory tartalmát.

   ![Data factory lap](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>2. lépés: A társított szolgáltatások létrehozásához
Társított szolgáltatások adattárolókhoz hivatkozásra, vagy egy adat-előállító szolgáltatások számítási. Ebben a lépésben kapcsolhat a tárfiók és a Batch-fiók a data factory.

#### <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása
1. Válassza ki a **Szerző és központi telepítése** csempét a **adat-előállító** paneljén **CustomActivityFactory**. A Data Factory Editor jelenik meg.

2. Válassza ki **az új adattároló** a parancssávon válassza **az Azure storage.** A JSON-parancsfájl hozhat létre, tárolási funkciókat biztosító társított szolgáltatás a szerkesztőben jelenik meg.

   ![Új adattároló](./media/data-factory-data-processing-using-batch/image7.png)

3. Cserélje le **fióknév** a tárfiók nevével. Cserélje le **fiókkulcs** a tárfiók hozzáférési kulccsal. A tárelérési kulcs beszerzéséről további tudnivalókért lásd: [nézet, a másolás és a hívóbetűk újragenerálása tárolási](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. Válassza ki **telepítés** a parancssávon, a társított szolgáltatás telepítéséhez.

   ![Üzembe helyezés](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Csatolt Azure Batch szolgáltatás létrehozása
Ebben a lépésben a Batch-fiók, amely a data factory egyéni tevékenységek futtatásához használt társított szolgáltatás létrehozása.

1. Válassza ki **új számítási** a parancssávon válassza **Azure Batch.** A JSON-parancsfájl segítségével hozzon létre egy kötegelt kapcsolódószolgáltatás-szerkesztőben jelenik meg.

2. A JSON-parancsfájl:

   a. Cserélje le **fióknév** a Batch-fiók nevével.

   b. Cserélje le **hozzáférési kulcs** a Batch-fiók hozzáférési kulcsával.

   c. Adja meg a készlet Azonosítóját a **poolName** tulajdonság. Ehhez a tulajdonsághoz megadhatja a készlet nevét vagy a készlet azonosítóját.

   d. Adja meg a kötegelt URI esetében a **batchUri** JSON tulajdonság.

      > [!IMPORTANT]
      > Az URL-CÍMÉT a **Batch-fiók** panelen a következő formátumban kell megadni: \<accountname\>.\< régió\>. batch.azure.com. Az a **batchUri** tulajdonság a JSON-parancsfájl, el kell távolítania a88 "accountname." ** a URL-címe Például: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Batch-fiók panelen](./media/data-factory-data-processing-using-batch/image9.png)

      Az a **poolName** tulajdonság, a készlet neve helyett a készlet Azonosítóját is megadhatja.

      > [!NOTE]
      > Ugyanúgy, mint a HDInsight a Data Factory szolgáltatásnak egy igény szerinti beállítás nem támogatja a kötegelt. Használhatja a saját Batch-készlet egy adat-előállítóban.
      >
      >
   
   e. Adja meg **StorageLinkedService** a a **linkedServiceName** tulajdonság. Az előző lépésben létrehozott szolgáltatásnak. A tárolót használja a rendszer egy átmeneti területre, fájlok és a naplókat.

3. Válassza ki **telepítés** a parancssávon, a társított szolgáltatás telepítéséhez.

#### <a name="step-3-create-datasets"></a>3. lépés: Adatkészletek létrehozása
Ebben a lépésben hoz létre a bemeneti és kimeneti adatok adatkészletek.

#### <a name="create-the-input-dataset"></a>A bemeneti adatkészlet létrehozása
1. A Data Factory Editor, válassza ki a **új adatkészlet** gomb az eszköztáron. Válassza ki **Azure Blob Storage tárolóban** a legördülő listából.

2. A JSON-parancsfájl, a jobb oldali cserélje le a következő JSON kódrészletet:

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

    Létrehoz egy folyamat később Ez a forgatókönyv, amelynél a kezdő időpont 2015-11-16T00:00:00Z és a befejezési idő 2015-11-16T05:00:00Z. Az ütemezett eredményezett adatokat óránként, így öt bemeneti/kimeneti szeletek (közötti **00**: 00:00 -\> **05**: 00:00).

    A **gyakoriság** és **időköz** bemeneti adatkészlet van beállítva **óra** és **1**, ami azt jelenti, hogy a bemeneti szelet elérhető óránként.

    Minden szelet kezdési idejét által képviselt a **SliceStart** az előző JSON-részlet rendszer változót. Az alábbiakban a minden szelet kezdési idejét.

    | **Slice** | **Kezdési idő**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    A **folderPath** számítja ki a szelet kezdési idő az év, hónap, nap és óra része (**SliceStart**). Ez hogyan egy bemeneti mappa szelet van leképezve.

    | **Slice** | **Kezdési idő**          | **Bemeneti mappa**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

3. Válassza ki **telepítés** létrehozása és telepítése az eszköztáron a **InputDataset** tábla.

#### <a name="create-the-output-dataset"></a>A kimeneti adatkészlet létrehozása
Ebben a lépésben létrehoz egy másik dataset a kimeneti adatok AzureBlob típusú.

1. A Data Factory Editor, válassza ki a **új adatkészlet** gomb az eszköztáron. Válassza ki **Azure Blob Storage tárolóban** a legördülő listából.

2. A JSON-parancsfájl, a jobb oldali cserélje le a következő JSON kódrészletet:

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

    Egy kimeneti blob/fájl az egyes bemeneti szeletek jön létre. Ez hogyan kimeneti fájl neve az egyes szeletek. A kimeneti fájlok akkor jönnek létre, egy kimeneti mappában `mycontainer\\outputfolder`.

    | **Slice** | **Kezdési idő**          | **Kimeneti fájlja**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Ne feledje, hogy a mappában lévő összes fájl egy bemeneti (például a 2015-11-16-00) amelynél a kezdő időpont 2015-11-16-00 szelet részét. A szelet feldolgozása után az egyéni tevékenység minden egyes fájl megvizsgálja-e, és hozza létre a kimeneti fájl előfordulási a keresési kifejezés "Microsoft". egy vonal Ha a mappa 2015-11-16-00 három fájlok, nincsenek három sort a kimeneti fájl 2015-11-16-00.txt.

3. Válassza ki **telepítés** létrehozása és telepítése az eszköztáron a **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>4. lépés: Hozzon létre, és futtatjuk a folyamatot az egyéni tevékenység
Ebben a lépésben létrehoz egy folyamatot egy tevékenységet, a korábban létrehozott egyéni tevékenység.

> [!IMPORTANT]
> Ha még nem töltötte fel **file.txt** a blob-tároló mappákat a felhasználótól, ehhez a folyamat létrehozása előtt. A **isPaused** tulajdonság értéke hamis az adatcsatorna JSON, így a folyamat azonnal futtatja, mert a **start** dátuma a múltban van.
>
>

1. Válassza a Data Factory Editor **új adatcsatorna** a parancssávon. Ha nem látja a parancs, válassza a három pont szimbólum megjelenítéséhez.

2. A JSON-parancsfájl, a jobb oldali cserélje le a következő JSON kódrészletet:

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

   * Csak egy tevékenység van folyamatban, és a típusú **DotNetActivity**.
   * **AssemblyName** értéke a dll-fájl neve **MyDotNetActivity.dll**.
   * **EntryPoint** értéke **MyDotNetActivityNS.MyDotNetActivity**. Alapvetően van \<névtér\>.\< osztálynév\> a kódban.
   * **PackageLinkedService** értéke **StorageLinkedService**, amely mutat, a blob-tároló, amely az egyéni tevékenység zip-fájl tartalmazza. Ha eltérő tárfiókokból használja bemeneti/kimeneti fájlok és az egyéni tevékenység zip-fájl, kell létrehoznia egy másik tárolás társított szolgáltatása. Ez a cikk feltételezi, hogy ugyanazt a tárfiókot használja.
   * **PackageFile** értéke **customactivitycontainer/MyDotNetActivity.zip**. A formátumú \<containerforthezip\>/\<nameofthezip.zip\>.
   * Az egyéni tevékenység veszi **InputDataset** bemenetként és **OutputDataset** output típusúként.
   * A **linkedServiceName** mutat, az egyéni tevékenység tulajdonság **AzureBatchLinkedService**, amely közli, hogy az egyéni tevékenység kell futtatni a kötegelt adat-Előállítóban.
   * A **egyidejűségi** beállítás fontos. Ha használja az alapértelmezett érték, amely 1, még akkor is, ha kettő vagy több csomópontja a Batch-készlet számítási, a rendszer feldolgozása egymás után. Ezért nem tart a párhuzamos feldolgozása kötegelt képességének kihasználása. Ha **egyidejűségi** magasabb értékre, mondja ki 2, az azt jelenti, hogy a két szeletek (felel meg a kötegben két feladatot) tudja feldolgozni az egy időben. Ebben az esetben a Batch-készlet mindkét a virtuális gépek ki van használva. Állítsa be megfelelően az egyidejű tulajdonság.
   * Alapértelmezés szerint egyetlen virtuális gép (szelet) csak egy feladat végrehajtása. Alapértelmezés szerint **virtuális gépenként feladatok maximális** a Batch-készlet 1 értékre van állítva. Az Előfeltételek részeként létrehozott egy alkalmazáskészlet Ez a tulajdonság értéke 2. Ezért két adatszeletek gyári futtathatja a virtuális gép egyszerre.
    - A **isPaused** tulajdonsága hamis értékre van beállítva, alapértelmezés szerint. A folyamat a rendszer azonnal futtatja ebben a példában a szeletek indítsa el a régebbi, mert. Állíthatja ezt a tulajdonságot **igaz** felfüggeszti a feldolgozási sorban lévő és állítsa vissza azt **hamis** újraindítására.
    -   A **start** és **end** alkalommal egymástól öt órát. A szeletek hourly, előállítása, öt szeletek hozzák létre a folyamat.

3. Válassza ki **telepítés** üzembe a folyamatot a parancssávon.

#### <a name="step-5-test-the-pipeline"></a>5. lépés: A folyamat tesztelése
Ebben a lépésben a folyamat a bemeneti mappákba fájlok ejtésével tesztelése. Indítsa el a feldolgozási sor tesztelték bemeneti mappáinak egy fájl.

1. Az a **adat-előállító** panel az Azure portálon, válassza az **Diagram**.

   ![Ábra](./media/data-factory-data-processing-using-batch/image10.png)

2. Az a **Diagram** megtekintéséhez kattintson duplán a bemeneti adatkészlet **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

3. A **InputDataset** panel jelenik meg, amelyen az összes öt szelet készen áll. Figyelje meg a **SZELET kezdete** és **SZELET BEFEJEZÉSÉNEK** az egyes szeletek.

   ![Adja meg szelet kezdési és befejezési időpontja](./media/data-factory-data-processing-using-batch/image12.png)

4. Az a **Diagram** nézetben jelölje ki **OutputDataset**.

5. Az öt kimeneti szeletek megjelennek a **készen** állapot, ha állították.

   ![Kimeneti szelet kezdési és befejezési időpontja](./media/data-factory-data-processing-using-batch/image13.png)

6. A szeletek társított feladatok megtekintése a portál használatával, és milyen virtuális gép futott minden szelet megtekintheti. További információkért lásd: a [adat-előállító és kötegelt integrációs](#data-factory-and-batch-integration) szakasz.

7. A kimeneti fájlok alatt szerepelhet `mycontainer` a `outputfolder` a blob Storage tárolóban.

   ![A kimeneti fájlok tárolási](./media/data-factory-data-processing-using-batch/image15.png)

   Öt kimeneti fájlok találhatók, minden szelet bemeneti. Minden kimeneti fájl tartalma a következő kimeneti hasonlít:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   A következő ábra bemutatja, hogyan a data factory szeletek kötegelt feladatok hozzárendelését. Ebben a példában a szelet csak egy futtató rendelkezik.

   ![Szelet leképezést diagramja](./media/data-factory-data-processing-using-batch/image16.png)

8. Próbálja meg most több, a mappákban található fájlokat. A fájlok létrehozása **fájl2.ref fájllal**, **file3.txt**, **file4.txt**, és **file5.txt** az ugyanahhoz a tartalomhoz, mint a mappában file.txt**2015-11-06-01**.

9. A megadott kimeneti mappa törlése a kimeneti fájl **2015-11-16-01.txt**.

10. Az a **OutputDataset** panelen kattintson a jobb gombbal a szeletre **SZELET kezdete** beállítása **11/16/2015 01:00:00-kor**. Válassza ki **futtatása** újrafuttatása/megismételheti a szelet számára. A szelet öt fájlok most már rendelkezik egy fájl helyett.

    ![Futtassa a következőt:](./media/data-factory-data-processing-using-batch/image17.png)

11. A szelet fut, és állapotú-e után **készen**, ellenőrizze a tartalmat a kimeneti fájl a szeletre vonatkozó (**2015-11-16-01.txt**). A kimeneti fájl megjelenik a `mycontainer` a `outputfolder` a blob Storage tárolóban. Egy sor minden fájlhoz a szelet kell lennie.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Ha nem törli a kimeneti fájl 2015-11-16-01.txt öt bemeneti fájlokkal tett kísérlet előtt, látni az előző szelet futni, hanem egy sor és az aktuális szelet futni, hanem öt sorban. Alapértelmezés szerint a tartalmat a rendszer hozzáfűzi a kimeneti fájlba, ha már létezik.
>
>

#### <a name="data-factory-and-batch-integration"></a>Adat-előállító és kötegelt integrációja
A Data Factory szolgáltatásnak hoz létre egy feladat kötegelt nevű `adf-poolname:job-xxx`.

![Kötegelt feladatok](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

A feladat egy feladat minden egyes tevékenység futtatásához a szelet jön létre. Ha 10 szeletek készen áll a feldolgozásra, a feladat 10 feladatok jönnek létre. A párhuzamosan futó, ha több számítási csomópontok a készlet több szelet lehet. Ha egyes számítási csomópontjain feladatok maximális száma nagyobb, mint egy, az azonos számítási egynél több szelet is futtathatók.

Ebben a példában nincsenek öt szeletek, ezért jelenleg öt feladatok kötegben. A **egyidejűségi** beállítása **5** az adatcsatorna JSON adat-előállító és **virtuális gépenként feladatok maximális** beállítása **2** a aBatch-készlet**2** virtuális gépeket, a feladatok futtatásához gyors. (Ellenőrizze a feladatok kezdési és befejezési idejének.)

A portál segítségével fogja megtekinteni a kötegelt és a feladatokat a szeletek társított, és megtekintheti, milyen minden szelet futtatott virtuális gép.

![Kötegelt munka feladatai](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>A folyamat hibakeresése
Néhány alapvető technikák hibakeresés áll.

1. Ha a bemeneti szelet nem beállítva **készen**, győződjön meg arról, hogy a bemeneti gyökérmappa-szerkezetében helyességéről, valamint, hogy file.txt szerepel a bemeneti mappákat.

   ![Bemeneti mappaszerkezet](./media/data-factory-data-processing-using-batch/image3.png)

2. Az a **Execute** metódus az egyéni tevékenység, használja a **IActivityLogger** objektum, amely segít a problémák elhárításához adatok naplózására. A naplózott üzenet jelenik meg a felhasználó\_0. naplófájlt.

   A a **OutputDataset** panelen jelölje ki a szeletet megtekintéséhez a **adatszelet** , hogy a szelet paneljét. A **tevékenység fut**, futtassa a szeletre vonatkozó egy tevékenység. Ha **futtatása** a parancssávon, akkor kezdhet a azonos szelet futtassa egy másik tevékenység.

   Ha a tevékenység futtatása választja, megjelenik a **tevékenység fut részletek** naplófájlok listáját tartalmazó panel. A felhasználó a naplózott üzeneteket látja\_0. naplófájlt. Ha hiba történik, megjelenik az három tevékenység fut oka az újrapróbálkozások maximális számát a feldolgozási sor/tevékenységben JSON 3 értékre van beállítva. A tevékenység futtatása kiválasztásakor tekintse meg a hiba elhárítása érdekében tekintse át a naplófájlokat.

   ![OutputDataset és az adatok szelet paneleken](./media/data-factory-data-processing-using-batch/image18.png)

   A naplófájlok, jelölje ki **felhasználói-0.log**. A jobb oldali panelen, használatával eredményeit a **IActivityLogger.Write** metódus jelennek meg.

   ![Tevékenységfuttatási részleteit megjelenítő panelen](./media/data-factory-data-processing-using-batch/image19.png)

   Ellenőrizze a rendszer-0.log rendszer hibaüzeneteket és kivételeket.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Tartalmazza a **PDB** a zip-fájlban szereplő fájlt, hogy a hiba részletei információkat, például a hívási verem hiba esetén.

4. Az egyéni tevékenység zip-fájlban szereplő összes fájlt egyetlen almappát a legfelső szinten kell lennie.

   ![Egyéni tevékenységek zip-fájl listája](./media/data-factory-data-processing-using-batch/image20.png)

5. Győződjön meg arról, hogy **assemblyName** (MyDotNetActivity.dll) **entryPoint** (MyDotNetActivityNS.MyDotNetActivity) **packageFile** (customactivitycontainer / MyDotNetActivity.zip), és **packageLinkedService** (kell mutatnia a blob-tároló, amely a zip-fájl tartalmazza) helyes értékre van beállítva.

6. Ha egy hiba, és újból feldolgozza a szelet szeretné rögzíteni kattintson a jobb gombbal a szeletre a **OutputDataset** panelhez, és válassza **futtatása**.

   ![Futtatás lehetőséggel OutputDataset panel](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Egy tároló megtalálható-e a blob-tároló nevű `adfjobs`. Ez a tároló nem automatikusan törlődik, de nyugodtan törölheti a megoldás tesztelés befejezése után. Hasonlóképpen, a data factory megoldás hoz létre egy kötegelt nevű `adf-\<pool ID/name\>:job-0000000001`. Ez a feladat után ha szeretné tesztelni a megoldás törölhető.
   >
   >
7. Az egyéni tevékenység nem használja a **app.config** fájlt a csomagból. Ezért a kódot a kapcsolati karakterláncok a konfigurációs fájlból olvassa be, ha még nem működik futásidőben. Az ajánlott eljárás szerint kötegelt használatakor a titkos kulcsok tárolására az Azure Key Vault. Egyszerű tanúsítvány-alapú szolgáltatás segítségével megvédheti a kulcstároló, és a Batch-készlet a tanúsítvány terjesztése. A .NET egyéni tevékenység titkok érhetik el a key vault futásidőben. Ez a megoldás általános méretezhető, bármilyen típusú titkos kulcsot, nem csak egy kapcsolati karakterláncot.

    Az egyszerűbb megkerülő megoldás, de nincs az ajánlott eljárás. Létrehozhat egy SQL-adatbázis kapcsolódó szolgáltatás kapcsolattal rendelkező kapcsolatikarakterlánc-beállításokat. Ezután hozzon létre egy adatkészlet által használt társított szolgáltatás, és a dataset láncolt az egyéni .NET tevékenység típusú bemeneti adatkészletként. A társított szolgáltatás kapcsolati karakterlánc az egyéni tevékenység kódban érheti el. Akkor érdemes jól működnek futásidőben.  

#### <a name="extend-the-sample"></a>A minta kiterjesztése
Ez a minta tudhat meg többet a Data Factory és kötegelt szolgáltatások bővítése. Például egy eltérő időtartományt szeletek feldolgozásához, a következő lépéseket:

1. Adja hozzá a következő almappákhoz `inputfolder`: 2015-11-16-05, 2015-11-16-06 201-11-16-07 2011-11-16-08 és 2015-11-16-09. A bemeneti fájlok ezeken a mappákon helyezze el. Módosítsa a folyamat befejezésének `2015-11-16T05:00:00Z` való `2015-11-16T10:00:00Z`. Az a **Diagram** megtekintéséhez kattintson duplán a **InputDataset** , és győződjön meg arról, hogy a bemeneti szeletek készen áll-e. Kattintson duplán a **OutputDataset** a kimeneti szeletek állapotának megtekintéséhez. Ha az a **készen** állapot, tekintse meg a kimeneti mappa kimeneti fájlok tárolásához.

2. Növeli vagy csökkenti a **egyidejűségi** beállítás tudni, hogy milyen hatással van a teljesítmény, a megoldás, különösen akkor fordul elő, a köteg feldolgozása. További információ a **egyidejűségi** beállítása, lásd: "4. lépés: hozzon létre és futtatjuk a folyamatot az egyéni tevékenység."

3. Hozzon létre egy alkalmazáskészlet magasabb/alacsonyabb **virtuális gépenként feladatok maximális**. A létrehozott új készletet használ, frissítse a kapcsolódó Batch-szolgáltatás a data factory megoldásban. További információk a **virtuális gépenként feladatok maximális** beállítás módosításához lásd "4. lépés: létrehozása és futtatjuk a folyamatot az egyéni tevékenység."

4. A Batch-készlet létrehozása a **automatikus skálázás** szolgáltatás. A Batch-készlet számítási csomópontjainak automatikus skálázás feldolgozási kapacitása révén az alkalmazás által használt dinamikus igazítását. 

    A minta képlet itt éri el a következő viselkedés. A készlet először hozza létre, amikor egy virtuális gép kezdődik. A $PendingTasks metrika futó feladatok számát határozza meg, és aktív (aszinkron) szerint. A képlet átlagos száma függőben lévő feladatok megkeresi az elmúlt 180 másodperc alatt, és ennek megfelelően állítja be TargetDedicated. Biztosítja, hogy TargetDedicated soha nem túllép 25 virtuális gépeket. Új feladatok be, mivel a készlet automatikusan nő. A feladat befejeződött virtuális gépek szabad egyenként válik, és az automatikus skálázás zsugorítja a virtuális gépek. StartingNumberOfVMs és maxNumberofVMs az igényeinek megfelelően módosíthatja.
 
    Automatikus skálázás képlet:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   További információkért lásd: [automatikus méretezési számítási csomópontok a Batch-készlet](../../batch/batch-automatic-scaling.md).

   Ha az alapértelmezett értéket használja, a készlet [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), a Batch szolgáltatás programnak a virtuális gép előkészítése az egyéni tevékenység futtatása előtt 15-30 percet is igénybe vehet. A készlet egy másik autoScaleEvaluationInterval használja, ha a Batch szolgáltatás autoScaleEvaluationInterval plus 10 percet is igénybe vehet.

5. A minta a megoldásban a **Execute** metódus meghívja a **Calculate** módszer, amely feldolgozza a egy bemeneti adatszelet egy kimeneti adatszelet létrehozásához. A bemeneti adatok feldolgozásához, és cserélje le a saját metódus lehet írni a **Calculate** kiszolgálómetódus-hívás a **Execute** metódus a metódus hívása.

### <a name="next-steps-consume-the-data"></a>További lépések: az adatokat
Adatok feldolgozása után online eszközöket, például a Power BI felhasználhat. Az alábbiakban a hivatkozásokat, amelyekkel jobban megértheti a Power bi-ban, és hogy miképpen lehet vele az Azure-ban:

* [A Power BI dataset felfedezés](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Első lépések a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Adatok frissítése a Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure és a Power BI: alapszintű áttekintése](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referencia
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [A Data Factory szolgáltatásnak bemutatása](data-factory-introduction.md)
  * [Az adat-előállító első lépései](data-factory-build-your-first-pipeline.md)
  * [Egyéni tevékenységeket felhasználni a Data Factory-folyamat](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Kötegelt alapjai](../../batch/batch-technical-overview.md)
  * [Batch funkcióinak áttekintése](../../batch/batch-api-basics.md)
  * [Létrehozása és kezelése a Batch-fiók az Azure-portálon](../../batch/batch-account-create-portal.md)
  * [Ismerkedés a kötegelt ügyféloldali kódtára a .NET-hez](../../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx

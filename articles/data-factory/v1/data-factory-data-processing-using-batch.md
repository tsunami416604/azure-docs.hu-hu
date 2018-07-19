---
title: Nagyméretű adatkészletek feldolgozása a Data Factory és a Batch használatával |} A Microsoft Docs
description: Ismerteti az Azure Data Factory-folyamatot hatalmas adatmennyiségek feldolgozása a párhuzamos feldolgozási képesség az Azure Batch használatával.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: b097a85ee97fb815106803ab95f3e4f6edde4896
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136672"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Nagyméretű adatkészletek folyamatot a Data Factory és a Batch használatával
> [!NOTE]
> Ez a cikk az Azure Data Factory általánosan elérhető 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás régebbi verzióját használja, lásd: [egyéni tevékenységek a Data Factory](../transform-data-using-dotnet-custom-activity.md).

Ez a cikk ismerteti az architektúra a minta megoldás, amely helyezi át, és feldolgozza a nagyméretű adatkészletek automatikus és ütemezett módon. A megoldás megvalósítása a Data Factory és az Azure Batch használatával egy végpontok közötti forgatókönyv is tartalmazza.

Ez a cikk hosszabb, mint egy tipikus cikket, mert tartalmaz egy teljes mintamegoldást bemutató. Ha most ismerkedik a Batch és Data Factory áttekintése, ezek a szolgáltatások talál további információt, és hogyan működnek együtt. Ha ismeri a szolgáltatásokkal kapcsolatos hiba, és vannak tervezése/mikroszolgáltatásokra egy megoldást, akkor előtérbe kerülhet a [architektúra szakasz](#architecture-of-sample-solution) a cikk. Fejleszt egy prototípusként vagy egy megoldást, előfordulhat, hogy szeretné-e, próbálja ki a részletes utasításokat a [forgatókönyv](#implementation-of-sample-solution). Felkérjük ezt a tartalmat, és miként használják a megjegyzéseit.

Először nézzük, hogyan Data Factory és a Batch szolgáltatás segíthet folyamat nagy méretű adatkészleteket a felhőben.     

## <a name="why-azure-batch"></a>Miért érdemes az Azure Batch?
 A Batch használatával hatékonyan futtathat nagy méretű párhuzamos és nagy teljesítményű feldolgozási (HPC) alkalmazásokat a felhőben. Olyan platformszolgáltatás, amely úgy ütemezi a nagy számítási igényű munkák futtatását (VM) virtuális gépek felügyelt gyűjteményében. Automatikusan képes méretezni a számítási erőforrásokat a feladatok igényeinek kielégítése érdekében.

A Batch szolgáltatással Azure számítási erőforrásokat határoz meg az alkalmazások párhuzamos és méretezhető futtatásához. Futtathat igény szerinti vagy ütemezett feladatokat. Nem kell manuálisan létrehozása, konfigurálása és kezelése a HPC-fürt, az egyes virtuális gépek, virtuális hálózatok, vagy egy összetett feladat és -Tevékenységütemezési infrastruktúrát.

 Ha nem ismeri a Batch, a következő cikkek segítséget nyújt megismerheti a megoldás az ebben a cikkben leírt architektúra/végrehajtása:   

* [A Batch alapjai](../../batch/batch-technical-overview.md)
* [A Batch funkcióinak áttekintése](../../batch/batch-api-basics.md)

Szükség esetén a Batch kapcsolatos további információkért lásd: [tanulási útvonalterv a Batchhez](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>Miért érdemes az Azure Data Factoryt választani?
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása. A Data Factory segítségével felügyelt adatfolyamatokat állíthat össze, amelyek az adatok áthelyezése a helyszíni és felhőbeli adattárolókból egy központi adattárban való létrehozásához. Ilyen például, az Azure Blob storage. A Data Factory használatával feldolgozhatók és átalakíthatók adatok például az Azure HDInsight és az Azure Machine Learning services használatával. Adatfolyamatok ütemezett módon (például óránként, naponta, és hetente) futtatását is ütemezheti. Akkor is folyamatok figyelése és felügyelete a ránézésre azonosíthatja a problémákat, és hajtsa végre műveletet.

  Ha nem ismeri a Data Factory, a következő cikkeket megismerheti a megoldás az ebben a cikkben leírt architektúra/végrehajtása:  

* [Data Factory bemutatása](data-factory-introduction.md)
* [Az első adatfolyamat kiépítése](data-factory-build-your-first-pipeline.md)   

Szükség esetén a Data Factory kapcsolatos további információkért lásd: [a Data Factory képzési terv](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>A Data Factory és a Batch együtt
A Data Factory beépített tevékenységeket magában foglalja. Ha például a másolási tevékenység van elvégezni adatok másolása vagy áthelyezése egy forrásadattárból egy céladattárba. A Hive-tevékenység segítségével az Azure-on Hadoop-fürtök (HDInsight) használatával adatokat feldolgozni. Támogatott Adatátalakítási tevékenységek listáját lásd: [adat-átalakítási tevékenységeket](data-factory-data-transformation-activities.md).

Egyéni .NET-tevékenységek áthelyezése vagy saját logika az adatok feldolgozására is létrehozhat. Ezek a tevékenységek futtathatja egy HDInsight-fürtön, vagy a Batch-készlet, virtuális gépek. Ha Batch használata esetén konfigurálhatja a készletet az automatikus méretezés (hozzáadása vagy eltávolítása a virtuális gépek a számítási feladatok alapján), adjon meg egy képlet alapján.     

## <a name="architecture-of-a-sample-solution"></a>Egy mintául szolgáló megoldás architektúrája
  Ebben a cikkben leírt architektúra olyan egy egyszerű megoldást. Emellett fontos összetett forgatókönyvek, például a pénzügyi szolgáltatások, képfeldolgozás és megjelenítési és részfeladatának elemzési kockázatelemzés.

A diagram azt ábrázolja, hogyan adat-előállító adatáthelyezésre és adatfeldolgozásra vezényli. Azt is bemutatja, hogyan Batch dolgozza fel az adatok párhuzamos módon. Töltse le, és nyomtassa ki a diagram a könnyű hivatkozás (11 x 17 hüvelyk vagy A3 méret). Lásd a diagramot úgy, hogy kinyomtathassa eléréséhez [HPC és adatkoordinálás Batch és Data Factory használatával](http://go.microsoft.com/fwlink/?LinkId=717686).

[![Nagyméretű adatfeldolgozás diagramja](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

A következő lista ismerteti a folyamat alapvető lépéseit. A megoldás magában foglalja a kód és magyarázataik a teljes körű megoldás létrehozásához.

* **Konfigurálhatja a Batch számítási csomópontokból (virtuális gépek) készletét.** Megadhatja, hogy a csomópontok számát és az egyes csomópontok méretét.

* **Hozzon létre egy Data Factory-példányt** megfelelően van konfigurálva, az entitások, amelyek a blob storage-ba, a Batch számítási szolgáltatás, bemeneti és kimeneti adatok és munkafolyamat vagy folyamat olyan tevékenységet, amely az adatok áthelyezéséhez és átalakításához.

* **Hozzon létre egy egyéni .NET-tevékenységet a Data Factory-folyamatot.** A tevékenységet, a felhasználói kód, amely a Batch-készlet futtat.

* **A bemeneti adatok nagy mennyiségű Store, az Azure Storage-blobokat.** Adatok (általában ideje szerint) logikai szelet van osztva.

* **A Data Factory másolja át a feldolgozott adatok párhuzamosan** a másodlagos helyre.

* **A Data Factory az egyéni tevékenység a készlet lefoglalta a Batch használatával futtatja.** A Data Factory egyidejűleg futtatható tevékenységek. Minden tevékenység dolgozza fel az adatok egy szeletet. Az eredményeket a storage szolgáltatásban tárolódnak.

* **A Data Factory egy harmadik helyre helyezi át a végső eredmények** vagy terjesztés keresztül egy alkalmazást, vagy más eszközökkel további feldolgozás céljából.

## <a name="implementation-of-the-sample-solution"></a>A minta megoldás megvalósítása
A mintául szolgáló megoldás, szándékosan egyszerű. Feladata, hogy bemutatják, hogyan használható a Data Factory és Batch folyamat adatkészletekhez együtt. A megoldás megszámlálja előfordulását a bemeneti fájlokat, amelyek időbeli adatsorok vannak rendszerezve a "Microsoft" keresőkifejezést. Majd megjeleníti a kimeneti fájlok száma.

**Idő:** Ha ismeri az Azure Data Factory és a Batch alapjait, és befejeződött a következő előfeltételek vonatkoznak, ez a megoldás egy vagy két óra alatt.

### <a name="prerequisites"></a>Előfeltételek
#### <a name="azure-subscription"></a>Azure-előfizetés
Ha nem rendelkezik Azure-előfizetéssel, gyorsan létrehozhat egy ingyenes próbafiókot. További információkért lásd: [az ingyenes próbaidőszak](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure Storage-fiók
Storage-fiók segítségével adatokat tárolni ebben az oktatóanyagban. Ha egy storage-fiók nem rendelkezik, tekintse meg [hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md#create-a-storage-account). A mintául szolgáló megoldás használja a blob storage-bA.

#### <a name="azure-batch-account"></a>Az Azure Batch-fiók
A Batch-fiók létrehozása a [az Azure portal](http://portal.azure.com/). További információkért lásd: [létrehozása és kezelése a Batch-fiók](../../batch/batch-account-create-portal.md). Vegye figyelembe a Batch-fiók tárfióknév és fiókkulcs. Használhatja még a [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) parancsmaggal hozzon létre egy Batch-fiókot. Ez a parancsmag használatával, lásd: [Batch PowerShell-parancsmagok használatának első lépései](../../batch/batch-powershell-cmdlets-get-started.md).

A minta megoldás dolgozza fel az adatokat egy készlet számítási csomópontok (virtuális gépek felügyelt gyűjteményében) párhuzamos módon Batch (közvetetten egy data factory-folyamatot) keresztül használja.

#### <a name="azure-batch-pool-of-virtual-machines"></a>A virtuális gépek az Azure Batch-készlet
Hozzon létre legalább két számítási csomópontok Batch-készlet.

1. Az a [az Azure portal](https://portal.azure.com)válassza **Tallózás** a bal oldali menüben, és válassza a **Batch-fiókok**.

2. Válassza ki a Batch-fiók megnyitása a **Batch-fiók** panelen.

3. Válassza ki a **készletek** csempére.

4. Az a **készletek** panelen válassza ki a **Hozzáadás** gombra az eszköztáron a készlet hozzáadása.

   a. Adja meg a készlet Azonosítóját (**készlet azonosítója**). Megjegyzés: a készlet azonosítója. Meg kell a data factory-megoldás létrehozásakor.

   b. Adja meg **Windows Server 2012 R2** számára a **operációs rendszer Termékcsaládját** beállítás.

   c. Válassza ki a **csomóponti tarifacsomagot**.

   d. Adja meg **2** értékeként a **cél dedikált** beállítás.

   e. Adja meg **2** értékeként a **csomópontonkénti tevékenységek maximális** beállítás.

   f. Válassza ki **OK** hoz létre a készletet.

#### <a name="azure-storage-explorer"></a>Azure Storage Explorer
Használhat [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) vagy [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (ClumsyLeaf szoftverből) vizsgálatára, és az adatok a Storage-projektek az alter. Is vizsgálja meg és módosíthatja az adatokat a felhőben üzemeltetett alkalmazások a naplókban.

1. Hozzon létre egy tárolót **mycontainer** privát hozzáférést (nincs névtelen hozzáférés).

2. Ha CloudXplorer használ, hozzon létre mappákban és almappáiban az alábbi struktúra használatával:

   ![Mappa és az almappák struktúra](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` és `outputfolder` a legfelső szintű mappák `mycontainer`. A `inputfolder` mappa almappákat dátum-idő stampek (éééé-HH-NN-HH) rendelkezik.

   Ha a Storage Explorer, a következő lépésben, a következő nevekkel fájlok feltöltése: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt`, és így tovább. Ez a lépés automatikusan létrehozza a mappákat.

3. Hozzon létre egy szövegfájlt **file.txt** tartalommal, amely rendelkezik a kulcsszót a gépen **Microsoft**. Ilyen például, "egyéni tevékenység Microsoft teszt egyéni tevékenység Microsoft teszt."

4. A fájl feltöltése a blob storage-ban a következő bemeneti mappák:

   ![Bemeneti mappa](./media/data-factory-data-processing-using-batch/image4.png)

   Ha a Storage Explorer, töltse fel a **file.txt** fájlt **mycontainer**. Válassza ki **másolási** BLOB másolatot készítsen az eszköztáron. Az a **a Blob másolásához** párbeszédpanelen módosítsa a **cél blobnév** való `inputfolder/2015-11-16-00/file.txt`. Ismételje meg ezt a lépést létrehozása `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt`, és így tovább. Ez a művelet automatikusan létrehoz a mappákat.

5. Hozzon létre egy másik tárolót `customactivitycontainer`. Töltse fel az egyéni tevékenység zip-fájlt a tárolóba.

#### <a name="visual-studio"></a>Visual Studio
Telepítse a Visual Studio 2012 vagy újabb a egyéni kötegelt tevékenység használható a data factory-megoldás létrehozásához.

### <a name="high-level-steps-to-create-the-solution"></a>A megoldás létrehozása a magas szintű lépései
1. Hozzon létre egy egyéni tevékenységet, amely az adatok feldolgozási logikáját tartalmazza.

2. Hozzon létre egy adat-előállítót, amely az egyéni tevékenység használja.

### <a name="create-the-custom-activity"></a>Az egyéni tevékenység létrehozása
A data factory egyéni tevékenység a minta megoldás legfontosabb céljait segítik. A Mintamegoldás Batch használja az egyéni tevékenység futtatásához. Fejleszthetők egyéni tevékenységek, és használja őket az adat-előállító folyamatok kapcsolatos információkért lásd: [egyéni tevékenységek használata egy data factory-folyamatot](data-factory-use-custom-activities.md).

.NET egyéni tevékenység használható egy data factory-folyamatot létrehozni, hozzon létre egy .NET-osztálytár projektet egy olyan osztállyal, amely megvalósítja az IDotNetActivity felület végrehajtási. Ez az interfész már csak egy ezek közül: hajtható végre. Íme a podpis metody:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

A módszer van néhány kulcsfontosságú összetevők, ismernie kell:

* A metódus négy paramétereket fogadja:

  * **linkedServices**. Ez a paraméter egy társított szolgáltatást, amely a bemeneti és kimeneti adatokat forrásokból (például blob storage) összekapcsolása a data factory enumerálható listáját. Ebben a példában csak egy társított szolgáltatást a bemeneti és kimeneti is használt Azure Storage típusú van.
  * **az adatkészletek**. A paraméter-adatkészletek enumerálható listáját. Ez a paraméter használatával a helyek és a bemeneti és kimeneti adatkészleteket által definiált sémák.
  * **tevékenység**. Ez a paraméter az aktuális számítási entitás jelöli. Ebben az esetben egy Batch-szolgáltatás.
  * **naplózó**. A naplózó használhatja, hogy a surface hibakeresési megjegyzéseket írhat, a folyamat a "User" napló.
* A metódus adja vissza egy szótár, amely összekapcsolja az egyéni tevékenységek együtt a jövőben is használható. Ez a funkció még nincs megvalósítva, csak egy üres szótár visszaadásához a metódus.

#### <a name="procedure-create-the-custom-activity"></a>Az eljárás: Az egyéni tevékenység létrehozása
1. Hozzon létre egy .NET-osztálytár projektet a Visual Studióban.

   a. Indítsa el a Visual Studio 2012/2013/2015.

   b. Válassza a **File** (Fájl) > **New** (Új) > **Project** (Projekt) lehetőséget.

   c. Bontsa ki a **sablonok**, és válassza ki **Visual C\#**. Ebben az útmutatóban a C használata\#, de használhat bármilyen .NET nyelven fejleszthet az egyéni tevékenység.

   d. Válassza ki **osztálytár** projekttípusok jobb listájából.

   e. Adja meg **MyDotNetActivity** számára a **neve**.

   f. Válassza ki **C:\\ADF** számára a **hely**. Hozza létre a mappát **ADF** Ha még nem létezik.

   g. A projekt létrehozásához válassza az **OK** lehetőséget.

2. Válassza ki **eszközök** > **NuGet-Csomagkezelő** > **Package Manager Console**.

3. Hajtsa végre a Package Manager Console Microsoft.Azure.Management.DataFactories importálása a következő parancsot:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importálás a **Azure Storage** NuGet-csomagot a projektbe. Mivel ebben a példában a Blob Storage API-t használja ezt a csomagot kell:

    ```powershell
    Install-Package Azure.Storage
    ```
5. Adja hozzá az alábbi irányelvek használatával a projektben a forrásfájl:

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
7. Módosítsa a kívánt osztály nevét **MyDotNetActivity**, és a Származtatás a **IDotNetActivity** csatoló látható módon:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. (Hozzáadás) megvalósítása a **Execute** módszere a **IDotNetActivity** a csatoló a **MyDotNetActivity** osztály. Másolja az alábbi mintakód a metódus. Ennél a módszernél a logika ismertetése, tekintse meg a [metódus végrehajtása](#execute-method) szakaszban.

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
9. Adja hozzá a következő segédmetódusokat az osztályhoz. Ezek a metódusok által kerül meghívásra a **Execute** metódust. Legfontosabb, a **Calculate** metódus a kódot, amely végighalad a minden egyes blob különíti el.

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
    A GetFolderPath metódus az az elérési utat a mappához, amely az adatkészlet ad vissza, és a GetFileName metódust a blob/fájl, amely az adatkészlet nevét adja vissza.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    A Calculate metódus a bemeneti fájlt (a mappában lévő blobok) a "Microsoft" kulcsszó példányainak számát számítja ki. A keresési kifejezés "Microsoft" nem változtatható a kódban.

10. A projekt fordításához használandó. Válassza ki **összeállítása** a menüben, és válassza ki a **megoldás fordítása**.

11. Indítsa el a Windows Intézőt, és nyissa meg a **bin\\debug** vagy **bin\\kiadási** mappát. A mappa build típusától függ.

12. Hozzon létre egy zip-fájlt **MyDotNetActivity.zip** , amely tartalmazza az összes bináris fájl a  **\\bin\\Debug** mappát. Érdemes a MyDotNetActivity tartalmazza. **pdb** fájlt annak érdekében, hogy további részleteket, például a sor száma, hogy a problémát az okozza, ha hiba lép fel a forráskódban.

   ![A bin\Debug mappák listája](./media/data-factory-data-processing-using-batch/image5.png)

13. Töltse fel **MyDotNetActivity.zip** blobként a blob-tároló `customactivitycontainer` az, hogy a StorageLinkedService társított szolgáltatás ADFTutorialDataFactory használja a blob Storage. A blob-tároló létrehozása `customactivitycontainer` Ha még nem létezik.

#### <a name="execute-method"></a>Metódus végrehajtása
Ez a szakasz további információt nyújt az Execute metódus a kódot.

1. A tagok iterálás a bemeneti gyűjteményben található a [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) névtér. Az iterációt a blob-gyűjtemény, módosítania kell használni a **BlobContinuationToken** osztály. Lényegében kell használnia a do-a jogkivonattal a hurokból mechanizmusként ciklus során. További információkért lásd: [használja a Blob storage a .NET használatával](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Itt láthatók egy alapszintű hurok:

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
   További információkért lásd a dokumentációban a [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) metódust.

2. A kód használatához blobok rekordkészletből logikailag kerül belül a do-ciklus során. Az a **Execute** metódus, a do-közben hurok nevű metódus adja át a blobok listáját **Calculate**. A metódus egy karakterlánc-változóhoz nevű ad vissza **kimeneti** , amely a szegmensben lévő összes BLOB keresztül kellene iterálni eredménye.

   A keresési kifejezést a blob "Microsoft" átadott előfordulását számát adja meg azt a **Calculate** metódust.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. Miután a **Calculate** metódus befejeződött, azt kell megírni, hogy egy új blob. Minden csoport a blobok feldolgozása egy új blob dají zapsat az eredményeket. Egy új blob írni, keresse meg a kimeneti adatkészlet.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. A kód is meghívja a segédmetódus **GetFolderPath** beolvasni a mappa elérési útját (a tároló nevét).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   A GetFolderPath metódus kerül az adatkészlet-objektum, melyet az AzureBlobDataSet, amely FolderPath nevű tulajdonsággal rendelkezik.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. A kód meghívja a **GetFileName** metódusának segítéségével lekérheti a fájl nevét (a blob neve). A kód hasonlít az előző kód használt mappa elérési útját.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. Egy URI-objektum létrehozása a fájl nevét írja. Az URI-konstruktor használja a **BlobEndpoint** tulajdonságát a tároló nevének visszaadása. A mappa elérési útját és nevét a kimeneti blob URI-t hozhat létre kerülnek.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. Után íródik a fájl nevét, a kimeneti karakterláncot, írhat a **Calculate** új blob metódust:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Az adat-előállító létrehozása
Az a [az egyéni tevékenység létrehozása](#create-the-custom-activity) szakaszban létrehozott egyéni tevékenységek és a bináris fájlokat a zip-fájlt, és a PDB-fájl feltöltése a blob-tárolóba. Ebben a szakaszban egy adat-előállítót egy olyan folyamattal, amely használja az egyéni tevékenység létrehozása.

Az egyéni tevékenység a bemeneti adatkészlet jelöli a blobokat (fájlok) a bemeneti mappában (`mycontainer\\inputfolder`) a blob storage-ban. A kimeneti adatkészlet a tevékenység kimeneti mappában a kimeneti blobok jelöli (`mycontainer\\outputfolder`) a blob storage-ban.

A bemeneti mappákba, dobja el egy vagy több fájlt:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Például betett egy fájlt (file.txt) a következő tartalommal egyes mappák:

```
test custom activity Microsoft test custom activity Microsoft
```

Minden egyes bemeneti mappa felel meg az adat-előállító szeletet akkor is, ha a mappa két vagy több fájlt. A folyamat minden egyes szeletet dolgoz fel, ha az egyéni tevékenység végighalad a szelet a bemeneti mappában lévő összes BLOB.

Láthatja, hogy az azonos tartalmú öt kimeneti fájlokat. Például a kimeneti fájlt a 2015-11-16-00 mappában található fájl feldolgozását, a következő tartalommal:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Ha a bemeneti mappa több fájlt (file.txt, fájl2.ref fájllal, file3.txt) az azonos tartalmú betett, láthatja a kimeneti fájl az alábbi tartalommal. Minden mappa (2015-11-16-00 stb.) ebben a példában egy szelet felel meg, annak ellenére, hogy a mappa több bemeneti fájllal rendelkezik.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

A kimeneti fájl három sort, egy az egyes bemeneti fájlra (blobra) abban a mappában, a szelet (2015-11-16-00) társított rendelkezik.

Egy feladat jön létre minden egyes tevékenység futtatásához. Ebben a példában csak egyetlen tevékenység van folyamatban. A folyamat egy szeletet dolgoz fel, ha az egyéni tevékenység kötegelt dolgozza fel a szeletet futtat. Nincsenek öt szeletek (minden szelet rendelkezhet több blobok vagy fájlt), mert öt feladatok jönnek létre a Batch szolgáltatásban. Batch-feladat fut, esetén az egyéni tevékenység, hogy fut-e.

A következő forgatókönyv további részleteket biztosít.

#### <a name="step-1-create-the-data-factory"></a>1. lépés: Az adat-előállító létrehozása
1. A bejelentkezés után a [az Azure portal](https://portal.azure.com/), az alábbi lépéseket:

   a. Válassza ki **új** a bal oldali menüben.

   b. Válassza ki **adatok + analitika** a a **új** panelen.

   c. Válassza ki **adat-előállító** a a **Data analytics** panelen.

2. Az a **új adat-előállító** panelen adja meg **CustomActivityFactory** neve. Az adat-előállító nevének globálisan egyedinek kell lennie. Ha a "Data factory CustomActivityFactory név nem érhető el" hibaüzenetet kapja módosítsa az adat-előállító nevét. Például yournameCustomActivityFactory használja, és hozza létre újra az adat-előállítóban.

3. Válassza ki **ERŐFORRÁSCSOPORT-név**, és válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot.

4. Győződjön meg arról, hogy az előfizetésben és régióban, ahol szeretné létrehozni az adat-előállító helyesek.

5. Válassza ki **létrehozás** a a **új adat-előállító** panelen.

6. Az adat-előállító létrehozása a portál irányítópultján.

7. A data factory sikeres létrehozása után láthatja a **adat-előállító** oldal, amely megjeleníti az adat-előállító tartalmát.

   ![Adat-előállító lap](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>2. lépés: A társított szolgáltatások létrehozása
Társított szolgáltatások adattárakat vagy számítási szolgáltatások adat-előállító. Ebben a lépésben társítani fogja a storage-fiók és a Batch-fiókot az adat-előállítóhoz.

#### <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása
1. Válassza ki a **Szerző és üzembe helyezése** csempét a **adat-előállító** paneljén **CustomActivityFactory**. A Data Factory Editor jelenik meg.

2. Válassza ki **új adattároló** a parancssávon válassza **az Azure storage.** A JSON-parancsfájl használatával hozzon létre egy Storage társított szolgáltatás a szerkesztőben megjelenik.

   ![Új adattároló](./media/data-factory-data-processing-using-batch/image7.png)

3. Cserélje le az **account name** kifejezést a tárfiókja nevére. Az **account key** kifejezést cserélje le a tárfiók hozzáférési kulcsára. A tárelérési kulcs lekérésével kezelésével kapcsolatos információkért lásd: [megtekintése, másolása és újragenerálása storage hozzáférési kulcsok](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Üzembe helyezés** elemére.

   ![Üzembe helyezés](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Azure Batch-beli társított szolgáltatás létrehozása
Ebben a lépésben a Batch-fiók, amely a data factory egyéni tevékenység futtatásához használt társított szolgáltatás létrehozása.

1. Válassza ki **új számítási** a parancssávon válassza **Azure Batch.** A JSON-parancsfájl használatával hozzon létre egy Batch-társított szolgáltatást a szerkesztőben megjelenik.

2. A JSON-parancsfájl:

   a. Cserélje le **fióknév** a Batch-fiók nevére.

   b. Cserélje le **hívóbetű** a Batch-fiók hozzáférési kulcsára.

   c. Adja meg a készlet az Azonosítót a **poolName** tulajdonság. Ehhez a tulajdonsághoz megadhatja a készlet nevét, vagy a készlet azonosítójával.

   d. Adja meg a batch URI az az **batchUri** JSON-tulajdonságot.

      > [!IMPORTANT]
      > Az URL-címet a **Batch-fiók** panelen a következő formátumban van: \<accountname\>.\< régió\>. batch.azure.com. Az a **batchUri** tulajdonság a JSON-parancsfájl, el kell távolítania a88 "accountname." ** az URL-CÍMÉT. Például: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Batch-fiók panel](./media/data-factory-data-processing-using-batch/image9.png)

      Az a **poolName** tulajdonság, a készlet neve helyett a készlet Azonosítóját is megadható.

      > [!NOTE]
      > A Data Factory szolgáltatás nem támogatja az igény szerinti beállítás a Batch, mint az HDInsight. Adat-előállító is használhatja a saját Batch-készlet.
      >
      >
   
   e. Adja meg **StorageLinkedService** számára a **linkedServiceName** tulajdonság. Az előző lépésben hozott létre ezt a társított szolgáltatást. Ez a tároló egy átmeneti területre, fájlok és a naplók szolgál.

3. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Üzembe helyezés** elemére.

#### <a name="step-3-create-datasets"></a>3. lépés: Adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre, bemeneti és kimeneti adatokat képviselik.

#### <a name="create-the-input-dataset"></a>A bemeneti adatkészlet létrehozása
1. A Data Factory Editorban válassza ki a **új adatkészlet** gombra az eszköztáron. Válassza ki **Azure Blob storage** a legördülő listából.

2. Cserélje le a JSON-parancsfájl, a jobb oldali panelen a következő JSON-kódrészletre:

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

    Létrehoz egy folyamatot a kezdési időpont 2015 az útmutató későbbi részeiben-11-16T00:00:00Z és a befejezési ideje a 2015-11-16T05:00:00Z. Van ütemezve adatok előállításához óránként, ezért a öt bemeneti/kimeneti szeletek (közötti **00**: 00:00 -\> **05**: 00:00).

    A **gyakorisága** és **időköz** a bemeneti adatkészlethez vannak beállítva **óra** és **1**, ami azt jelenti, hogy a bemeneti szelet érhető el Óránként.

    Minden szelet kezdési ideje képviseli a **SliceStart** rendszerváltozó az előző JSON-kódrészletben. Az alábbiakban az egyes szeletekhez a kezdési idejének.

    | **Slice** | **Kezdési idő**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    A **folderPath** által az év, hónap, nap és óra részét a szelet kezdő időpontja (**SliceStart**). Itt látható, hogyan van leképezve egy bemeneti mappa egy szeletet.

    | **Slice** | **Kezdési idő**          | **Bemeneti mappa**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

3. Válassza ki **telepítés** létrehozása és üzembe helyezése az eszköztáron a **InputDataset** tábla.

#### <a name="create-the-output-dataset"></a>A kimeneti adatkészlet létrehozása
Ebben a lépésben hozzon létre egy másik adatkészlet típusa AzureBlob, amely a kimeneti adatokat jelöli.

1. A Data Factory Editorban válassza ki a **új adatkészlet** gombra az eszköztáron. Válassza ki **Azure Blob storage** a legördülő listából.

2. Cserélje le a JSON-parancsfájl, a jobb oldali panelen a következő JSON-kódrészletre:

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

    Kimeneti blob/fájl jön létre az egyes bemeneti szeletek. Itt látható, hogy az egyes szeletekhez egy kimeneti fájl neve. A kimeneti fájlok jönnek létre egy kimeneti mappában, `mycontainer\\outputfolder`.

    | **Slice** | **Kezdési idő**          | **Kimeneti fájl**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Ne feledje, hogy a mappában található összes fájl egy bemeneti (például a 2015-11-16-00) a kezdési időponttal 2015-11-16-00 szelet részei. A szeletek feldolgozásakor a rendszer az egyéni tevékenység keresztül minden fájlt megvizsgálja-e, és létrehozza a kimeneti fájl az előfordulási a keresési kifejezés "Microsoft". egy vonal A 2015-11-16-00 mappa három fájl is tartalmaz, ha nincsenek három sort a kimeneti fájl 2015-11-16-00.txt.

3. Válassza ki **telepítés** létrehozása és üzembe helyezése az eszköztáron a **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>4. lépés: Hozzon létre, és futtatjuk a folyamatot az egyéni tevékenységek
Ebben a lépésben létrehoz egy folyamatot egy tevékenységgel, a korábban létrehozott egyéni tevékenységet.

> [!IMPORTANT]
> Ha még nem töltött **file.txt** bemeneti blob-tárolóban található, ehhez a folyamat létrehozása előtt. A **isPaused** tulajdonság értéke HAMIS, a folyamat JSON-t, így a folyamat azonnali fut, mert a **start** dátuma a múltban van.
>
>

1. A Data Factory Editorban válassza **új adatcsatorna** a parancssávon. Ha nem látja a parancs, válassza ki a három pontra szimbólum, a megjelenítéséhez.

2. Cserélje le a JSON-parancsfájl, a jobb oldali panelen a következő JSON-kódrészletre:

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

   * A folyamat csak egyetlen tevékenység van, és a típusú **DotNetActivity**.
   * **AssemblyName** értéke az a dll-fájl neve **MyDotNetActivity.dll**.
   * **EntryPoint** értékre van állítva **MyDotNetActivityNS.MyDotNetActivity**. Gyakorlatilag \<névtér\>.\< osztálynév\> a kódban.
   * **PackageLinkedService** értékre van állítva **StorageLinkedService**, amely a blob Storage, az egyéni tevékenység zip-fájlt tartalmazó mutat. Ha a bemeneti és kimeneti fájlok és az egyéni tevékenység zip-fájlt használjon különböző tárfiókokat, akkor egy másik Storage társított szolgáltatás létrehozása. Ez a cikk feltételezi, hogy ugyanazt a tárfiókot használja.
   * **PackageFile** értékre van állítva **customactivitycontainer/MyDotNetActivity.zip**. A következő formátumban van \<containerforthezip\>/\<nameofthezip.zip\>.
   * Az egyéni tevékenységek **InputDataset** bemenetként, és **OutputDataset** kimenetként.
   * A **linkedServiceName** tulajdonság az egyéni tevékenység **AzureBatchLinkedService**, amely tájékoztatja, hogy a Data Factory, az egyéni tevékenység Batch futtatásához szükséges.
   * A **egyidejűségi** beállítás fontos. Ha használja az alapértelmezett érték, amely 1, még akkor is, ha kettő vagy több készlet számítási csomópontjain a Batch, a szeletek feldolgozása után. Ezért azt nem kihasználhatja a párhuzamos feldolgozási képesség a Batch. Ha **egyidejűségi** értéke, például: 2, az azt jelenti, hogy a két szeleteket (felel meg a két feladatot a Batch szolgáltatásban) egy időben feldolgozható. Ebben az esetben is a virtuális gépek a Batch-készlet használ. Állítsa be megfelelően az egyidejűségi tulajdonság.
   * Csak egy feladat (szelet) végrehajtása a virtuális gép bármikor alapértelmezés szerint. Alapértelmezés szerint **virtuális gépenkénti feladatok maximális** a Batch-készlet 1 értékre van állítva. Az Előfeltételek részeként létrehozott készlet Ez a tulajdonság értéke 2. Ezért két data factory szeletek is futtathatja a virtuális gép egyszerre.
    - A **isPaused** tulajdonsága hamis értékre van beállítva, alapértelmezés szerint. A folyamat azonnal fut ebben a példában, mivel a szeletek indítsa el a múltban. És ez a tulajdonság megadható **igaz** szüneteltetni a folyamat és a készlet, biztonsági **hamis** újraindításához.
    -   A **start** és **záró** értendő öt órára egymástól. A szeletek előállítása óránként, így öt szelet előállítása a folyamat.

3. A folyamat üzembe helyezéséhez kattintson a parancssor **Üzembe helyezés** elemére.

#### <a name="step-5-test-the-pipeline"></a>5. lépés: A folyamat tesztelése
Ebben a lépésben a folyamat által fájlokat húzhat a bemeneti mappákban teszteléséhez. Indítsa el az egyes bemeneti mappa egy fájlt a folyamat tesztelése.

1. Az a **adat-előállító** az Azure Portalon, válassza a panel **Diagram**.

   ![Ábra](./media/data-factory-data-processing-using-batch/image10.png)

2. Az a **Diagram** kattintson duplán a bemeneti adatkészlet **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

3. A **InputDataset** panel jelenik meg az összes öt szelet készen áll. Figyelje meg a **SZELET KEZDŐ időpont** és **SZELET BEFEJEZÉSI időpontja** az egyes szeletekhez.

   ![A bemeneti szelet kezdési és befejezési időpontja](./media/data-factory-data-processing-using-batch/image12.png)

4. Az a **Diagram** nézetben válassza **OutputDataset**.

5. Az öt kimeneti szeletek jelennek meg a **készen** állapotba, ha azok keletkezett.

   ![A kimeneti szelet kezdési és befejezési időpontja](./media/data-factory-data-processing-using-batch/image13.png)

6. A szeletek tartozó feladatok megtekintéséhez a portál használatával, és megtekintheti, milyen virtuális gép minden szelet futtatunk. További információkért lásd: a [Data Factory és a Batch-integráció](#data-factory-and-batch-integration) szakaszban.

7. A kimeneti fájlok meg fog jelenni `mycontainer` a `outputfolder` a blob storage-ban.

   ![A kimeneti fájlok tárolására](./media/data-factory-data-processing-using-batch/image15.png)

   Öt kimeneti fájlok találhatók, egyet az egyes bemeneti szelet. A kimeneti fájlok mindegyike rendelkezik a tartalom a következő kimenet hasonlít:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   A következő ábra szemlélteti, hogyan képezze le a data factory szeletek feladatok Batch szolgáltatásban. Ebben a példában egy szelet van, csak egy példányban fusson.

   ![Szelet leképezés diagramja](./media/data-factory-data-processing-using-batch/image16.png)

8. Próbálja meg most már több fájl egy mappában. Hozza létre a fájlokat **fájl2.ref fájllal**, **file3.txt**, **file4.txt**, és **file5.txt** az ugyanazon a tartalmon, ahogy az a mappa file.txt**2015-11-06-01**.

9. A kimeneti mappa, törölje a kimeneti fájl **2015-11-16-01.txt**.

10. Az a **OutputDataset** panelen kattintson a jobb gombbal a szeletre **SZELET KEZDŐ időpont** beállítása **11/16/2015 01:00:00-kor**. Válassza ki **futtatása** az ismétlés/megismételheti a szeletet. A szelet öt fájl most már rendelkezik egy fájl helyett.

    ![Futtassa a következőt:](./media/data-factory-data-processing-using-batch/image17.png)

11. Ha a szelet fut, és annak állapotát **készen**, ellenőrizze a tartalmat a kimeneti fájl a szelet (**2015-11-16-01.txt**). A kimeneti fájl megjelenik a `mycontainer` a `outputfolder` a blob storage-ban. Egy sor minden fájlhoz a szelet kell lennie.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Ha a kimeneti fájl 2015-11-16-01.txt öt bemeneti fájlokkal tett kísérlet előtt nem törli, láthatja az előző szelet futtatásból származó egy vonal- és az aktuális szelet futtatható öt sorban. Alapértelmezés szerint a tartalmat a rendszer hozzáfűzi a kimeneti fájl Ha már létezik.
>
>

#### <a name="data-factory-and-batch-integration"></a>Data Factory és a Batch-integráció
A Data Factory szolgáltatás nevű hoz létre egy feladatot a Batch szolgáltatásban `adf-poolname:job-xxx`.

![Batch-feladatok](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

A feladat feladat jön létre minden egyes tevékenység futtatásához egy szeletet. 10 szelet készen áll a dolgozhatók, ha a feladat 10 feladatok jönnek létre. A párhuzamosan futó, ha több számítási csomópont a készletben egynél több szelet rendelkezhet. Ha a számítási csomópontok tevékenységek maximális számát értéke nagyobb, mint egy, az azonos számítási egynél több szelet is futtathatja.

Ebben a példában nincsenek öt szeletek, így öt feladatokat a Batch szolgáltatásban. A **egyidejűségi** beállítása **5** a folyamat JSON-t az adat-előállító és **virtuális gépenkénti feladatok maximális** beállítása **2** aBatch-készletben**2** virtuális gépeket, a tevékenységek futtatásakor gyors. (Ellenőrizze a feladatok kezdési és befejezési idejének.)

A Batch-feladat és a szeletek társított feladatainak megtekintése a portál használatával, és megtekintheti, milyen minden szelet futtatott virtuális gép.

![Batch-feladatok](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Hibakeresés a folyamaton
Hibakeresés néhány alapvető technikából áll.

1. Ha a bemeneti szelet nem **készen**, győződjön meg arról, hogy a bemeneti mappa struktúrája megfelelő-e, és file.txt, hogy létezik-e a bemeneti mappákban.

   ![Bemeneti mappa struktúrája](./media/data-factory-data-processing-using-batch/image3.png)

2. Az a **Execute** metódus az egyéni tevékenység, használja a **IActivityLogger** objektum naplózza az adatokat, amelyek segítenek elhárítani a problémákat. A felhasználónak megjelennek a naplózott üzeneteket\_0. naplófájlt.

   A a **OutputDataset** panelen válassza ki a szelet megtekintéséhez a **adatszelet** adott szeletre vonatkozó panelen. A **tevékenységfuttatások**, a szelet egy tevékenységfuttatás látható. Ha **futtatása** a parancssorban futtassa a azonos szelet egy másik tevékenység megkezdése.

   Ha a tevékenység-végrehajtásonként választja, megjelenik a **az Activity run details** naplófájlok listáját tartalmazó panelen. Láthatja, hogy a felhasználó a naplózott üzeneteket\_0. naplófájlt. Ha hiba történik, akkor három tevékenységfuttatások megtekintéséhez, mert az újrapróbálkozások számának értéke 3, a folyamat/tevékenység a JSON. Amikor kiválasztja a tevékenységet futtató, tekintse meg a naplófájlokat a hiba elhárítása tekinthető meg.

   ![Outputdataset néven –, és a Data slice paneljein](./media/data-factory-data-processing-using-batch/image18.png)

   A naplófájlok a listában, válassza ki a **user-0.log**. A jobb oldali panelen, az eredményeket a a **IActivityLogger.Write** metódus jelennek meg.

   ![Tevékenységfuttatás részletei panel](./media/data-factory-data-processing-using-batch/image19.png)

   Ellenőrizze a rendszer-0.log bármilyen rendszer hibaüzenetek és a kivételek.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Tartalmazza a **PDB** fájlt a zip-fájlt, hogy a hiba részletes adatait adatokat, például a hívási verem hiba esetén.

4. Az egyéni tevékenység zip-fájljában lévő összes fájlt a legfelső szinten kell lennie az almappák nélkül.

   ![Egyéni tevékenység zip-fájlok listája](./media/data-factory-data-processing-using-batch/image20.png)

5. Ügyeljen arra, hogy **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer / MyDotNetActivity.zip), és **packageLinkedService** (a blob Storage, amely tartalmazza a zip-fájlt kell mutatnia) megfelelő értékekre vannak beállítva.

6. Ha kijavított egy hibát, és a szelet újra fel szeretné, kattintson a jobb gombbal a szeletre az **OutputDataset** panelhez, és válassza **futtatása**.

   ![Futtassa a beállítás OutputDataset panel](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > A blob Storage-nevű tároló lehet `adfjobs`. Ez a tároló nem törlődnek automatikusan, de biztonságosan törölheti a megoldás tesztelés befejezése után. Hasonlóképpen, a data factory-megoldás hoz létre egy Batch-feladat nevű `adf-\<pool ID/name\>:job-0000000001`. A megoldás igény szerint tesztelését követően törölheti a feladat.
   >
   >
7. Az egyéni tevékenység nem használja a **app.config** fájlt a csomagból. Ezért ha a kód kapcsolati karakterláncokat olvas a konfigurációs fájlban, nem működik futásidőben. Batch használata esetén az ajánlott eljárás, hogy az Azure Key Vaultban titkos kulcsok tárolásához. Egyszerű tanúsítvány-alapú szolgáltatás segítségével a key vault védelmet, és terjessze a tanúsítványt a Batch-készlet. A .NET egyéni tevékenység elérheti a titkos kulcsokat a kulcstartóból futásidőben. Ez általános megoldás méretezheti bármilyen típusú titkos kulcsot, nem csak egy kapcsolati karakterláncot.

    Egy egyszerűbb megkerülő megoldás, de nem ajánlott. Létrehozhat egy SQL database-beli társított szolgáltatás kapcsolattal kapcsolatikarakterlánc-beállításokat. Ezután hozzon létre egy adatkészletet, amely a társított szolgáltatást használja, és az adatkészlet hozzákapcsolva a .NET egyéni tevékenység helyőrző bemeneti adatkészletként. A társított szolgáltatás kapcsolati karakterlánc az egyéni tevékenység kódban érheti el. Akkor működnek jól futásidőben.  

#### <a name="extend-the-sample"></a>Terjessze ki a mintát
Bővítheti ezt a mintát, ha többet szeretne megtudni a Data Factory és a Batch-funkciók. Ha például egy másik időtartományban szeletei feldolgozásához, a következő lépéseket:

1. Adja hozzá a következő almappákhoz a `inputfolder`: 2015-11-16-05, 2015-11-16-06 201-11-16-07, 2011-11-16-08 és 2015. 11-16-09-. A bemeneti fájlokat helyezze mappákat. Módosítsa a folyamat a befejezési `2015-11-16T05:00:00Z` való `2015-11-16T10:00:00Z`. Az a **Diagram** kattintson duplán **InputDataset** , és győződjön meg arról, hogy készen áll-e a bemeneti szeletek. Kattintson duplán a **OutputDataset** a kimeneti szeleteket állapotának megtekintéséhez. Ha a **készen** állapotban van, ellenőrizze a kimeneti mappát a kimeneti fájlokat.

2. Növelheti vagy csökkentheti a **egyidejűségi** beállítás megérteni, hogyan érinti a megoldását, különösen a kötegelt végzett feldolgozás teljesítményét. További információ a **egyidejűségi** beállítását, lásd: "4. lépés: hozzon létre, és futtatjuk a folyamatot az egyéni tevékenységek."

3. Készlet létrehozása magasabb/alacsonyabb **virtuális gépenkénti feladatok maximális**. Használni létrehozott új készletet, frissítse a Batch társított szolgáltatást a data factory-megoldás. További információ a **virtuális gépenkénti feladatok maximális** beállítását, lásd: "4. lépés: hozzon létre, és futtatjuk a folyamatot az egyéni tevékenységek."

4. A Batch-készlet létrehozása a **automatikus skálázási** funkció. Egy Batch-készletben lévő számítási csomópontok automatikus méretezése a feldolgozási teljesítményt az alkalmazása által használt dinamikus igazítását. 

    A mintául szolgáló képlet itt éri el a következő viselkedés. Először hozza létre a készletet, ha egy virtuális gép kezdődik. A $PendingTasks metrika futó feladatok száma határozza meg, és állapotok aktív (sorban áll). A képlet átlagos száma függőben lévő feladatokat megkeresi az elmúlt 180 másodperc alatt, és ennek megfelelően beállítja a TargetDedicated. Biztosítja, hogy TargetDedicated soha nem túllép 25 virtuális gépeket. Új feladatok elküldése, ahogy a készlet automatikusan nő. A feladat befejeződött, mint virtuális gépek ingyenes egyenként legyen, és az automatikus skálázás zsugorítja ezeken a virtuális gépeken. Igény szerinti startingNumberOfVMs és maxNumberofVMs módosíthatja.
 
    Automatikus skálázási képletet:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   További információkért lásd: [automatikusan méretezni a számítási csomópontok Batch-készlet](../../batch/batch-automatic-scaling.md).

   Ha a készletet használja az alapértelmezett [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), a Batch szolgáltatás a virtuális gép előkészítése az egyéni tevékenység futtatása előtt 15 – 30 percet is igénybe vehet. Ha a készlet egy másik autoScaleEvaluationInterval használ, a Batch szolgáltatás autoScaleEvaluationInterval és 10 percet is igénybe vehet.

5. A minta megoldásban a **Execute** metódus meghívja a **Calculate** metódushoz, amely egy bemeneti adatszelet előállít egy kimeneti adatszeletet dolgozza fel. Írhat saját módszer dolgozza fel a bemeneti adatokat, és cserélje le a **Calculate** metódust hívja meg a **Execute** metódust a metódus hívásával.

### <a name="next-steps-consume-the-data"></a>Következő lépések: az adatok felhasználása
Miután az adatok feldolgozására, online eszközökkel, mint a Power BI használhatja fel. Az alábbiakban a hivatkozások segítenek megérteni a Power bi-ban, és hogyan használható az Azure-ban:

* [Ismerje meg egy adatkészletet a Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Ismerkedés a Power BI Desktopban](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Adatfrissítés a Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Az Azure és a Power bi-ban: alapszintű áttekintése](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referencia
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [A Data Factory szolgáltatás bemutatása](data-factory-introduction.md)
  * [A Data Factory – első lépések](data-factory-build-your-first-pipeline.md)
  * [Egyéni tevékenységek használata Data Factory-folyamatok](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [A Batch alapjai](../../batch/batch-technical-overview.md)
  * [A Batch funkcióinak áttekintése](../../batch/batch-api-basics.md)
  * [Hozzon létre és felügyelhet Batch-fiókot az Azure Portalon](../../batch/batch-account-create-portal.md)
  * [Ismerkedés a Batch ügyfél .NET-hez készült](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx

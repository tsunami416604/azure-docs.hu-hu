---
title: Nagyméretű adatkészletek feldolgozása a Data Factory és a Batch használatával
description: Bemutatja, hogyan dolgozhat fel hatalmas mennyiségű adatot egy Azure Data Factory-folyamatban az Azure Batch párhuzamos feldolgozási képességének használatával.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: afc7a7406831568304c2ebd8d9a6c72b497e04e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972878"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Nagyméretű adatkészletek feldolgozása a Data Factory és a Batch használatával
> [!NOTE]
> Ez a cikk az Azure Data Factory általánosan elérhető 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az Egyéni tevékenységek a Data Factory szolgáltatásban című [témakört.](../transform-data-using-dotnet-custom-activity.md)

Ez a cikk egy mintamegoldás architektúráját ismerteti, amely automatikus anam-es és ütemezett módon mozgatja és dolgozza fel a nagyméretű adatkészleteket. Emellett egy teljes körű forgatókönyvet is biztosít a megoldás megvalósításához a Data Factory és az Azure Batch használatával.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk hosszabb, mint egy tipikus cikk, mert egy teljes mintamegoldás forgatókönyvét tartalmazza. Ha most ismerkedik a Batch és a Data Factory szolgáltatással, megismerheti ezeket a szolgáltatásokat és azok együttműködésének módját. Ha tud valamit a szolgáltatásokról, és egy megoldást tervez/tervez, a cikk architektúrára összpontosíthat. Ha prototípust vagy megoldást fejleszt, érdemes kipróbálnia a forgatókönyv lépésenkénti útmutatóját. Meghívjuk a tartalommal és a tartalom használatával kapcsolatos megjegyzéseit.

Először nézzük meg, hogyan segíthetnek a Data Factory és a Batch szolgáltatások nagy adatkészletek feldolgozásában a felhőben.     


## <a name="why-azure-batch"></a>Miért az Azure Batch?
 A Batch segítségével hatékonyan futtathat nagyméretű párhuzamos és nagy teljesítményű számítástechnikai (HPC) alkalmazásokat a felhőben. Ez egy platformszolgáltatás, amely ütemezi a számítási igényű munkát a virtuális gépek (VM-ek felügyelt gyűjteményén) való futtatáshoz. Automatikusan skálázhatja a számítási erőforrásokat a feladatok igényeinek megfelelően.

A Batch szolgáltatással Azure számítási erőforrásokat határoz meg az alkalmazások párhuzamos és méretezhető futtatásához. Igény szerinti vagy ütemezett feladatokat futtathat. Nem kell manuálisan létrehoznia, konfigurálnia és kezelnie egy HPC-fürtöt, az egyes virtuális gépeket, a virtuális hálózatokat vagy egy összetett feladat- és feladatütemezési infrastruktúrát.

 Ha nem ismeri a Batch-et, az alábbi cikkek segítenek megérteni a jelen cikkben ismertetett megoldás architektúráját/megvalósítását:   

* [A köteg alapjai](../../batch/batch-technical-overview.md)
* [A Batch funkcióinak áttekintése](../../batch/batch-api-basics.md)

Ha további információra van szükség a Batch szolgáltatásról, olvassa el [a Batch dokumentációját.](https://docs.microsoft.com/azure/batch/)

## <a name="why-azure-data-factory"></a>Miért az Azure Data Factory?
A Data Factory egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása. A Data Factory segítségével felügyelt adatfolyamatokat hozhat létre, amelyek adatokat helyeznek át a helyszíni és a felhőbeli adattárakból egy központi adattárba. Egy példa az Azure Blob storage. A Data Factory segítségével feldolgozhatja/átalakíthatja az adatokat olyan szolgáltatások használatával, mint az Azure HDInsight és az Azure Machine Learning. Az adatfolyamatokütemezett (például óránkénti, napi és heti) futtatását is ütemezheti. A folyamatokat egy pillantással figyelheti és kezelheti a problémák azonosításához és a műveletek hez.

  Ha nem ismeri a Data Factory-t, a következő cikkek segítenek megérteni a jelen cikkben ismertetett megoldás architektúráját/megvalósítását:  

* [A Data Factory bemutatása](data-factory-introduction.md)
* [Az első adatfolyamat létrehozása](data-factory-build-your-first-pipeline.md)   

A Data Factory szolgáltatásról további információ: [A Data Factory dokumentációja.](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory)

## <a name="data-factory-and-batch-together"></a>A Data Factory és a Batch együtt
A Data Factory beépített tevékenységeket is tartalmaz. A Másolás tevékenység például arra szolgál, hogy adatokat másoljon/helyezzen át egy forrásadattárból egy céladattárba. A Hive-tevékenység az azure-beli Hadoop-fürtök (HDInsight) használatával történő adatok feldolgozására szolgál. A támogatott átalakítási tevékenységek listáját az Adatátalakítási tevékenységek című [témakörben tetszetős.](data-factory-data-transformation-activities.md)

Egyéni .NET-tevékenységeket is létrehozhat az adatok saját logikával való áthelyezéséhez vagy feldolgozásához. Ezeket a tevékenységeket hdinsight-fürtön vagy virtuális gépek batch készletén futtathatja. A Batch használatakor konfigurálhatja a készlet automatikus skálázás (virtuális gépek hozzáadása vagy eltávolítása a munkaterhelés alapján) a megadott képlet alapján.     

## <a name="architecture-of-a-sample-solution"></a>Mintamegoldás architektúrája
  A cikkben ismertetett architektúra egyszerű megoldást kínál. Összetett forgatókönyvek, például a pénzügyi szolgáltatások általi kockázatmodellezés, a képfeldolgozás és -renderelés, valamint a genomikai elemzés szempontjából is fontos.

Az ábra bemutatja, hogyan koordinálja a Data Factory az adatok mozgását és feldolgozását. Azt is bemutatja, hogy a Batch hogyan dolgozza fel az adatokat párhuzamosan. Töltse le és nyomtassa ki a diagramot az egyszerű hivatkozás (11 x 17 hüvelyk vagy A3 méret). A diagram nyomtatásához tekintse meg a [HPC és az adatvezénylést a Batch és a Data Factory használatával című témakörben.](https://go.microsoft.com/fwlink/?LinkId=717686)

[![Nagyméretű adatfeldolgozási diagram](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

Az alábbi lista a folyamat alapvető lépéseit tartalmazza. A megoldás tartalmazza a kódot és a magyarázatokat a végpontok között megoldás létrehozásához.

* **Batch konfigurálása számítási csomópontok (VM-ek) készlettel.** Megadhatja a csomópontok számát és az egyes csomópontok méretét.

* **Hozzon létre egy Data Factory-példányt,** amely blobstorage-t, a Batch számítási szolgáltatást, bemeneti/kimeneti adatokat és az adatokat áthelyező és átalakító tevékenységeket tartalmazó tevékenységeket képviselő entitásokkal van konfigurálva.

* **Hozzon létre egyéni .NET-tevékenységet a Data Factory folyamatban.** A tevékenység a kötegkészleten futó felhasználói kód.

* **Nagy mennyiségű bemeneti adatot tárolhet blobként az Azure Storage-ban.** Az adatok logikai szeletekre vannak osztva (általában idő szerint).

* A Data Factory a másodlagos **hellyel párhuzamosan feldolgozott adatokat másolja.**

* **A Data Factory az egyéni tevékenységet a Batch által lefoglalt készlet használatával futtatja.** A Data Factory egyidejűleg futtathat tevékenységeket. Minden tevékenység feldolgoz egy adatszeletet. Az eredményeket a tárolóban tárolják.

* **A Data Factory áthelyezi a végeredményt egy harmadik helyre,** akár egy alkalmazáson keresztül történő terjesztésre, akár más eszközökkel történő további feldolgozásra.

## <a name="implementation-of-the-sample-solution"></a>A mintaoldat megvalósítása
A mintaoldat szándékosan egyszerű. Úgy tervezték, hogy megmutassa, hogyan használhatja együtt a Data Factory és a Batch együttes adatkészletek feldolgozását. A megoldás megszámolja a "Microsoft" keresőkifejezés előfordulásainak számát az idősorozatba rendezett bemeneti fájlokban. Ezután kimeneti a szám a kimeneti fájlokat.

**Idő:** Ha ismeri az Azure, a Data Factory és a Batch alapjait, és elvégezte az alábbi előfeltételeket, ez a megoldás egy-két órát vesz igénybe.

### <a name="prerequisites"></a>Előfeltételek
#### <a name="azure-subscription"></a>Azure-előfizetés
Ha nem rendelkezik Azure-előfizetéssel, gyorsan létrehozhat egy ingyenes próbafiókot. További információ: [Free trial](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure Storage-fiók
Az oktatóanyagban tárolt adatok tárolásához tárfiókot használhat. Ha nem rendelkezik tárfiókkal, olvassa el a Tárfiók létrehozása című [témakört.](../../storage/common/storage-account-create.md) A mintamegoldás blob storage-t használ.

#### <a name="azure-batch-account"></a>Azure Batch-fiók
Batch-fiók létrehozása az [Azure Portal használatával.](https://portal.azure.com/) További információt a [Batch-fiók létrehozása és kezelése című témakörben](../../batch/batch-account-create-portal.md)talál. Jegyezze fel a Batch-fiók nevét és a fiókkulcsot. A [New-AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) parancsmag használatával kötegelt fiókot is létrehozhat. A parancsmag használatáról a Batch [PowerShell-parancsmagok használatának első lépései.](../../batch/batch-powershell-cmdlets-get-started.md)

A mintamegoldás batch (közvetve egy adatfeldolgozó folyamaton keresztül) az adatok feldolgozása párhuzamos módon egy számítási csomópontok (a virtuális gépek felügyelt gyűjteménye) folyamata.

#### <a name="azure-batch-pool-of-virtual-machines"></a>Virtuális gépek Azure Batch-készlete
Hozzon létre egy batch készlet legalább két számítási csomópontot.

1. Az [Azure Portalon](https://portal.azure.com)válassza a bal oldali menü **Tallózás parancsát,** majd válassza a **Kötegelt fiókok lehetőséget.**

1. Válassza ki a Batch-fiókot a **Batch-fiók** panel megnyitásához.

1. Válassza a **Készletek csempét.**

1. Készlet hozzáadásához kattintson a **Poolok** panelen az eszköztár **Hozzáadás** gombjára.

   a. Adja meg a készlet (**készletazonosító) azonosítóját.** Jegyezze fel a készlet azonosítóját. Szüksége van rá, amikor létrehozza az adat-előállító megoldás.

   b. Adja meg a **Windows Server 2012 R2 értéket** az operációs rendszer család **beállításához.**

   c. Válasszon **csomóponti tarifacsomagot.**

   d. Írja be a **2** értéket a **Dedikált cél** beállítás értékeként.

   e. Adja meg a **2** értéket a **Maximális feladatok csomópontonkéntbeállításértékként.**

   f. A készlet létrehozásához válassza az **OK gombot.**

#### <a name="azure-storage-explorer"></a>Azure Storage Explorer
Az [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) vagy [a CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (a ClumsyLeaf szoftverből) segítségével ellenőrizheti és módosíthatja a Storage-projektekben lévő adatokat. Azt is ellenőrizheti, és módosíthatja az adatokat a naplókaa felhőben üzemeltetett alkalmazások.

1. Hozzon létre egy mycontainer nevű **tárolót** privát hozzáféréssel (nincs névtelen hozzáférés).

1. CloudXplorer használata esetén hozzon létre mappákat és almappákat a következő struktúrával:

   ![Mappa- és almappa-struktúra](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder`és `outputfolder` a legfelső szintű `mycontainer`mappák a ban. A `inputfolder` mappa almappái dátum-idő bélyegzőkkel (YYYY-MM-DD-HH).

   A Storage Explorer használata esetén a következő lépésben a `inputfolder/2015-11-16-00/file.txt`következő `inputfolder/2015-11-16-01/file.txt`nevekkel tölthet fel fájlokat: , , és így tovább. Ez a lépés automatikusan létrehozza a mappákat.

1. Hozzon létre egy szöveges **file file.txt** fájlt a számítógépen olyan tartalommal, amely a **Microsoft**kulcsszót tartalmaz. Egy példa a "teszt egyéni tevékenység Microsoft teszt egyéni tevékenység Microsoft."

1. Töltse fel a fájlt a blob storage következő beviteli mappáiba:

   ![Beviteli mappák](./media/data-factory-data-processing-using-batch/image4.png)

   Ha a Storage Explorert használja, töltse fel a **file.txt** fájlt a **mycontainer fájlba.** Válassza a **Másolás** lehetőséget az eszköztáron a blob másolatának létrehozásához. A **Blob másolása** párbeszédpanelen módosítsa a `inputfolder/2015-11-16-00/file.txt`célblob nevét a (Célblob **neve) névre.** Ismételje meg ezt `inputfolder/2015-11-16-01/file.txt` `inputfolder/2015-11-16-02/file.txt`a `inputfolder/2015-11-16-03/file.txt` `inputfolder/2015-11-16-04/file.txt`lépést a létrehozásához , , , , és így tovább. Ez a művelet automatikusan létrehozza a mappákat.

1. Hozzon létre `customactivitycontainer`egy másik tárolót . Töltse fel az egyéni tevékenység zip-fájlt ebbe a tárolóba.

#### <a name="visual-studio"></a>Visual Studio
Telepítse a Visual Studio 2012-es vagy újabb telepítését az adat-előállító megoldásban használandó egyéni batch tevékenység létrehozásához.

### <a name="high-level-steps-to-create-the-solution"></a>Magas szintű lépések a megoldás létrehozásához
1. Hozzon létre egy egyéni tevékenységet, amely tartalmazza az adatfeldolgozási logikát.

1. Hozzon létre egy adat-előállító, amely az egyéni tevékenységet használja.

### <a name="create-the-custom-activity"></a>Egyéni tevékenység létrehozása
A data factory egyéni tevékenység a szíve ennek a mintamegoldásnak. A mintamegoldás batch-et használ az egyéni tevékenység futtatásához. Az egyéni tevékenységek fejlesztéséről és az adatüzemi folyamatokban való használatáról az [Egyéni tevékenységek használata adatfeldolgozó folyamatokban című témakörben](data-factory-use-custom-activities.md)talál további információt.

Az adat-előállító folyamatokban használható .NET egyéni tevékenység létrehozásához hozzon létre egy .NET osztálykönyvtár-projektet egy olyan osztállyal, amely megvalósítja az IDotNetActivity felületet. Ennek a kapcsolatnak csak egy metódusa van: Végrehajtás. Itt van az aláírása a módszer:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

A módszer néhány kulcsfontosságú összetevőből áll, amelyeket meg kell értenie:

* A módszer négy paramétert vesz igénybe:

  * **linkedServices**. Ez a paraméter a bemeneti/kimeneti adatforrásokat (például blob storage) az adat-előállítóhoz kapcsolt szolgáltatások számbavételi listája. Ebben a példában csak egy azure storage típusú összekapcsolt szolgáltatás van, amelyet bemeneti és kimeneti célokra is használnak.
  * **adatkészletek .** Ez a paraméter az adatkészletek felsorolása. Ezzel a paraméterrel lejuthat a bemeneti és kimeneti adatkészletek által meghatározott helyek és sémák leése.
  * **tevékenység.** Ez a paraméter az aktuális számítási entitást jelöli. Ebben az esetben batch szolgáltatás.
  * **logger**. A naplózóval hibakeresési megjegyzéseket írhat, amelyek a folyamat "Felhasználó" naplójaként jelentkeznek.
* A metódus egy szótárat ad vissza, amely az egyéni tevékenységek et a jövőben együtt láncolhatja. Ez a funkció még nincs megvalósítva, így csak egy üres szótárt ad vissza a metódusból.

#### <a name="procedure-create-the-custom-activity"></a>Eljárás: Egyéni tevékenység létrehozása
1. Hozzon létre egy .NET osztálykönyvtár-projektet a Visual Studióban.

   a. Indítsa el a Visual Studio 2012/2013/2015 alkalmazást.

   b. Válassza **az** > **Új** > **fájl projekt lehetőséget.**

   c. Bontsa ki **a Sablonok csomópontot,** és válassza a **Visual\#C**lehetőséget. Ebben a forgatókönyvben a\#C , de bármilyen .NET nyelvet használhat az egyéni tevékenység fejlesztéséhez.

   d. Válassza az **Osztálytár** elemet a jobb oldali projekttípusok listájából.

   e. Írja be a **MyDotNetActivity nevet** a **nevéhez.**

   f. Válassza a **C:\\ADF** lehetőséget a **helyhez.** Hozza létre az **ADF** mappát, ha az nem létezik.

   g. A projekt létrehozásához válassza az **OK** lehetőséget.

1. Válassza **az Eszközök** > **NuGet Csomagkezelő csomagkezelő** > **konzolt.**

1. A Csomagkezelő konzolon hajtsa végre a következő parancsot a Microsoft.Azure.Management.DataFactories importálásához:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importálja az **Azure Storage** NuGet csomagot a projektbe. Szüksége van erre a csomagra, mert a Blob Storage API-t használja ebben a példában:

    ```powershell
    Install-Package Az.Storage
    ```
1. Adja hozzá a következő irányelveket a projekt forrásfájljához:

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
1. Módosítsa a névtér nevét **MyDotNetActivityNS névre.**

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Módosítsa az osztály nevét **MyDotNetActivity**névre, és az **IDotNetActivity** felületről az ábrán látható módon:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Valósítsa meg (adja hozzá) az **IDotNetActivity** felület **Execute** metódusát a **MyDotNetActivity** osztályba. Másolja a következő mintakódot a metódusba. Az ebben a módszerben használt logika magyarázatát lásd a [Metódus végrehajtása című](#execute-method) szakaszban.

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
1. Adja hozzá a következő segítő módszereket az osztályhoz. Ezeket a metódusokat az **Execute** metódus hívja meg. A legfontosabb, hogy a **Calculate** metódus elkülöníti az egyes blobok on-t iterációkódot.

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
    A GetFolderPath metódus visszaadja annak a mappának az elérési útját, amelyre az adatkészlet mutat, és a GetFileName metódus visszaadja annak a blobnak/fájlnak a nevét, amelyre az adatkészlet mutat.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    A Számítás módszer kiszámítja a "Microsoft" kulcsszó példányainak számát a bemeneti fájlokban (blobok a mappában). A "Microsoft" keresőkifejezés a kódban kódolva van.

1. Fordítsa össze a projektet. Válassza a menü **Build parancsát,** majd a **Megoldás összeállítása**lehetőséget.

1. Indítsa el a Windows Intézőt, és lépjen a **tárolóhiba-hibakereső\\** vagy **a raktárhely-kiadási\\** mappába. A mappaválasztás a létrehozás típusától függ.

1. Hozzon létre egy Zip fájlt **MyDotNetActivity.zip,** amely tartalmazza az összes bináris bin ** \\\\Debug** mappában. Előfordulhat, hogy a MyDotNetActivity-t is be szeretné vonni. **pdb** fájlt, így további részleteket kaphat, például a forráskódban lévő sorszámot, amely a problémát a hiba bekövetkeztekor okozta.

   ![A bin\Debug mappalista](./media/data-factory-data-processing-using-batch/image5.png)

1. Töltsön fel **MyDotNetActivity.zip** blobként `customactivitycontainer` a blob tárolóba, amelyet a StorageLinkedService csatolt szolgáltatás a ADFTutorialDataFactory használ. Hozza létre `customactivitycontainer` a blob tárolót, ha még nem létezik.

#### <a name="execute-method"></a>Metódus végrehajtása
Ez a szakasz további részleteket tartalmaz a kód a Végrehajtás metódusban.

1. A bemeneti gyűjteményen keresztül iteráláshoz a tagok a [Microsoft.WindowsAzure.Storage.Blob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob) névtérben találhatók. A blob gyűjtemény ismétléséhez a **BlobContinuationToken** osztályt kell használnia. Lényegében egy do-while ciklust kell használnia a tokenlel, mint a hurok ból való kilépés mechanizmusát. További információt a [Blob storage használata a .NET-ből című témakörben talál.](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) Az alaphurok itt látható:

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
   További információt a [ListBlobsSegmented](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobssegmented) metódus dokumentációjában talál.

1. A blobok készletén való munka kódja logikailag a do-while cikluson belül reked. A **Végrehajtás** metódusban a teendő ciklus a blobok listáját egy Calculate nevű metódusnak **adja át.** A metódus egy **kimenet** nevű karakterlánc-változót ad vissza, amely a szegmens összes blobján keresztül iterált.

   A "Microsoft" keresőkifejezés előfordulásainak számát adja vissza a **Számítási** metódusnak átadott blobban.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Miután a **Calculate** metódus befejeződött, azt egy új blobba kell írni. Minden feldolgozott blobkészlethez egy új blob írható az eredményekkel. Új blobba íráshoz először keresse meg a kimeneti adatkészletet.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. A kód a **GetFolderPath** segítő metódust is meghívja a mappa elérési útjának (a tárolótároló nevének) beolvasásához.

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   A GetFolderPath metódus a DataSet objektumot egy FolderPath nevű tulajdonsággal ellátott AzureBlobDataSet objektumra küldi.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. A kód meghívja a **GetFileName** metódust a fájlnév (blobnév) beolvasásához. A kód hasonló a mappa elérési útjának leéséhez használt előző kódhoz.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. A fájl neve URI-objektum létrehozásával íródott. Az URI konstruktor a **BlobEndpoint** tulajdonság ot használja a tároló nevének visszaadására. A mappa elérési útja és a fájlnév hozzáadódnak a kimeneti blob URI létrehozásához.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. A fájl nevének megírása után a kimeneti karakterláncot a **Calculate** metódusból egy új blobba írhatja:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Az adat-előállító létrehozása
Az [egyéni tevékenység létrehozása](#create-the-custom-activity) szakaszban létrehozott egy egyéni tevékenységet, és feltöltötte a zip fájlt bináris fájllal és a PDB-fájlt egy blobtárolóba. Ebben a szakaszban hozzon létre egy adat-előállító egy folyamat, amely az egyéni tevékenységet használja.

Az egyéni tevékenység bemeneti adatkészlete a blobok (fájlok)`mycontainer\\inputfolder`a bemeneti mappában ( ) a blob storage- ban. A tevékenység kimeneti adatkészlete a kimeneti mappában`mycontainer\\outputfolder`( ) lévő kimeneti blobok kimeneti blobtárolóban lévő kimeneti blobokat jelöli.

Egy vagy több fájl áthelyezése a beviteli mappákba:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Például egy fájlt (file.txt) a következő tartalommal együtt az egyes mappákba húzzon:

```
test custom activity Microsoft test custom activity Microsoft
```

Minden beviteli mappa az adat-előállító szeletének felel meg, még akkor is, ha a mappa két vagy több fájlt tartalmaz. Amikor a folyamat minden szeletet feldolgoz, az egyéni tevékenység végighalad a szelet beviteli mappájában lévő összes blobon.

Öt, azonos tartalmú kimeneti fájl jelenik meg. A 2015-11-16-00 mappában lévő fájl feldolgozásából származó kimeneti fájl például a következő tartalommal rendelkezik:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Ha több fájlt (file.txt, file2.txt, file3.txt) dob be a bemeneti mappába, a kimeneti fájlban a következő tartalom jelenik meg. Minden mappa (2015-11-16-00, stb.) megfelel a minta egy szeletének, még akkor is, ha a mappában több bemeneti fájl található.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

A kimeneti fájl most már három sor, egy-egy bemeneti fájl (blob) a mappához társított a szelet (2015-11-16-00).

Minden tevékenységfuttatáshoz létrejön egy feladat. Ebben a mintában csak egy tevékenység van a folyamatban. Amikor egy szeletet a folyamat feldolgoz, az egyéni tevékenység a Batch-en fut a szelet feldolgozásához. Mivel öt szelet van (minden szelet több blobot vagy fájlt is tartalmazhat), öt feladat jön létre a Batch-ben. Amikor egy feladat a Batch, ez az egyéni tevékenység fut.

A következő forgatókönyv további részleteket tartalmaz.

#### <a name="step-1-create-the-data-factory"></a>1. lépés: Az adatgyár létrehozása
1. Miután bejelentkezett az [Azure Portalra,](https://portal.azure.com/)tegye a következő lépéseket:

   a. Válassza az **ÚJ** lehetőséget a bal oldali menüben.

   b. Válassza a **Data + Analytics** lehetőséget az **Új** panelen.

   c. Válassza a **Data Factory** lehetőséget az **Adatelemzés** panelen.

1. Az **Új adatgyár** panelen adja meg a **CustomActivityFactory** nevet. Az adat-előállító nevének globálisan egyedinek kell lennie. Ha a "Data factory name CustomActivityFactory is not available" hibaüzenet jelenik meg, módosítsa az adatgyár nevét. Használja például a yournameCustomActivityFactory nevet, és hozza létre újra az adatgyárat.

1. Válassza **az ERŐFORRÁSCSOPORT NEVE**lehetőséget, és jelöljön ki egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot.

1. Ellenőrizze, hogy helyes-e az előfizetés és a régió, ahol az adat-előállító létrehozását létre kívánja hozni.

1. Válassza a **Létrehozás** lehetőséget az **Új adatgyár** panelen.

1. Az adatgyár a portál irányítópultján jön létre.

1. Az adat-előállító sikeres létrehozása után megjelenik a **Data factory** oldal, amely megmutatja az adat-előállító tartalmát.

   ![Adatgyári lap](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>2. lépés: Csatolt szolgáltatások létrehozása
A csatolt szolgáltatások adattárakat vagy számítási szolgáltatásokat kapcsolnak össze egy adat-előállítóval. Ebben a lépésben összekapcsolja a tárfiókot és a Batch-fiókot az adat-előállítóval.

#### <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása
1. Válassza ki a **Szerző és üzembe helyezése** csempe a Data **factory** panel **CustomActivityFactory.** Megjelenik a Data Factory Editor.

1. Válassza az **Új adattár lehetőséget** a parancssávon, és válassza az **Azure storage lehetőséget.** Megjelenik a storage-alapú szolgáltatás szerkesztőben történő létrehozásához használt JSON-parancsfájl.

   ![Új adattár](./media/data-factory-data-processing-using-batch/image7.png)

1. Cserélje le az **account name** kifejezést a tárfiókja nevére. Az **account key** kifejezést cserélje le a tárfiók hozzáférési kulcsára. A tárfiók hozzáférési kulcsának [kezelése( Tárfiók-hozzáférési kulcsok kezelése)](../../storage/common/storage-account-keys-manage.md)témakörből megtudhatja, hogy miként szerezheti be a tárfiók hozzáférési kulcsait.

1. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Üzembe helyezés** elemére.

   ![Üzembe helyezés](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Azure Batch-alapú szolgáltatás létrehozása
Ebben a lépésben hozzon létre egy csatolt szolgáltatást a Batch-fiókhoz, amely az adat-előállító egyéni tevékenység futtatásához használatos.

1. Válassza **az Új számítás lehetőséget** a parancssávon, és válassza az Azure Batch **lehetőséget.** Megjelenik a Kötegelt kapcsolattal ellátott szolgáltatás szerkesztőben való létrehozásához használt JSON-parancsfájl.

1. A JSON-parancsfájlban:

   a. Cserélje le a **fiók nevét** a Batch-fiók nevére.

   b. Cserélje le a **hozzáférési kulcsot** a Batch-fiók hozzáférési kulcsára.

   c. Adja meg a **készletnév** tulajdonság készletének azonosítóját. Ehhez a tulajdonsághoz megadhatja a készlet nevét vagy a készletazonosítót.

   d. Adja meg a **batchUri** JSON tulajdonság kötegURI-uri-ját.

      > [!IMPORTANT]
      > A Batch **Account** panel URL-címe a \<következő\>formátumban található: fióknév . \<régió\>.batch.azure.com. A **BatchUri** tulajdonság a JSON-parancsfájlban el kell távolítania a88"fióknév." ** az URL-címről. Például: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Kötegszámla panel](./media/data-factory-data-processing-using-batch/image9.png)

      A **készletnév** tulajdonsághoz a készlet neve helyett a készlet azonosítóját is megadhatja.

      > [!NOTE]
      > A Data Factory szolgáltatás nem támogatja az igény szerinti beállítást a Batch, mint a HDInsight. Csak a saját batch készlet et használhat egy adat-előállítóban.
      >
      >

   e. Adja meg a **StorageLinkedService szolgáltatást** a **linkedServiceName** tulajdonsághoz. Ezt a csatolt szolgáltatást az előző lépésben hozta létre. Ez a tároló a fájlok és naplók átmeneti területeként használatos.

1. A társított szolgáltatás üzembe helyezéséhez kattintson a parancssáv **Üzembe helyezés** elemére.

#### <a name="step-3-create-datasets"></a>3. lépés: Adatkészletek létrehozása
Ebben a lépésben a bemeneti és kimeneti adatokat jelölő adatkészleteket hoz létre.

#### <a name="create-the-input-dataset"></a>A bemeneti adatkészlet létrehozása
1. A Data Factory Editor ban válassza az **Eszköztár Új adatkészlet** gombját. Válassza ki az **Azure Blob storage** a legördülő listából.

1. Cserélje le a json-parancsfájlt a jobb oldali ablaktáblára a következő JSON-kódrészletre:

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

    A forgatókönyv későbbi részében hozzon létre egy folyamatot a 2015-11-16T00:00:00Z kezdési idővel és a 2015-11-16T05:00:00Z befejezési idővel. A tervek szerint óránként hoz létre adatokat, így öt bemeneti/kimeneti szelet van\> (00:00:00 és **05:00:00**között). **00**

    A bemeneti adatkészlet **gyakorisága** és **időköze** **Óra** és **1,** ami azt jelenti, hogy a bemeneti szelet óránként érhető el.

    Az egyes szeletek kezdési idejét az előző JSON-kódrészlet **SliceStart** rendszerváltozója jelöli. Itt vannak az egyes szeletek kezdési időpontjai.

    | **Szelet** | **Kezdési idő**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**00:00:00 |
    | 2         | 2015-11-16T**01**01:00:00 |
    | 3         | 2015-11-16T**02**02:00:00 |
    | 4         | 2015-11-16T**03**03:00:00 |
    | 5         | 2015-11-16T**04**04:00:00 |

    A **folderPath** kiszámítása a szelet kezdési időpontjának év, hónap, nap és óra része (**SliceStart**) használatával történik. Így van leképezve egy beviteli mappára egy szeletre.

    | **Szelet** | **Kezdési idő**          | **Beviteli mappa**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**00:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**01:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**02:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**03:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**04:00:00 | 2015-11-16-**04** |

1. Válassza a **Telepítés lehetőséget** az eszköztáron a **Beviteliadatkészlet-tábla** létrehozásához és központi telepítéséhez.

#### <a name="create-the-output-dataset"></a>A kimeneti adatkészlet létrehozása
Ebben a lépésben hozzon létre egy másik adatkészletet a azureblob típusú, hogy képviselje a kimeneti adatokat.

1. A Data Factory Editor ban válassza az **Eszköztár Új adatkészlet** gombját. Válassza ki az **Azure Blob storage** a legördülő listából.

1. Cserélje le a json-parancsfájlt a jobb oldali ablaktáblára a következő JSON-kódrészletre:

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

    Minden bemeneti szelethez létrejön egy kimeneti blob/fájl. Itt van, hogyan kell egy kimeneti fájlt elnevezni az egyes szeletekhez. Az összes kimeneti fájl egy kimeneti mappában jön létre, `mycontainer\\outputfolder`.

    | **Szelet** | **Kezdési idő**          | **Kimeneti fájl**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**00:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**01:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**02:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**03:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**04:00:00 | 2015-11-16-**04.txt** |

    Ne feledje, hogy a bemeneti mappában lévő összes fájl (például 2015-11-16-00) egy szelet része a 2015-11-16-00 kezdési időpontdal. A szelet feldolgozásakor az egyéni tevékenység átvizsgálja az egyes fájlokat, és létrehoz egy sort a kimeneti fájlban a "Microsoft" keresőkifejezés előfordulásainak számával. Ha három fájl van a mappában 2015-11-16-00, három sor van a 2015-11-16-00.txt kimeneti fájlban.

1. Válassza a **Telepítés lehetőséget** az eszköztáron a **OutputDataset**létrehozásához és központi telepítéséhez.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>4. lépés: A folyamat létrehozása és futtatása egyéni tevékenységgel
Ebben a lépésben hozzon létre egy folyamatot egy tevékenységgel, a korábban létrehozott egyéni tevékenységgel.

> [!IMPORTANT]
> Ha még nem töltötte fel **a file.txt** fájlt a blobtárolóban lévő mappákba, tegye meg a folyamat létrehozása előtt. Az **isPaused** tulajdonság értéke hamis a JSON folyamatban, így a folyamat azonnal fut, mert a **kezdési** dátum a múltban van.
>
>

1. A Data Factory Editor ban válassza az **Új folyamat lehetőséget** a parancssávon. Ha nem látja a parancsot, jelölje ki a három pont szimbólumot a megjelenítéséhez.

1. Cserélje le a json-parancsfájlt a jobb oldali ablaktáblára a következő JSON-kódrészletre:

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

   * Csak egy tevékenység van folyamatban, és ez a típus **DotNetActivity**.
   * **Az AssemblyName** a **DLL MyDotNetActivity.dll**nevére van beállítva.
   * **Az EntryPoint** beállítása **MyDotNetActivityNS.MyDotNetActivity**. Ez alapvetően \<névtér\>. \<osztálynevét\> a kódban.
   * **PackageLinkedService** van beállítva **StorageLinkedService**, amely rámutat arra, hogy a blob storage, amely tartalmazza az egyéni tevékenység zip fájlt. Ha különböző tárfiókokat használ a bemeneti/kimeneti fájlokhoz és az egyéni tevékenységzip-fájlhoz, létre kell hoznia egy másik Storage csatolt szolgáltatást. Ez a cikk feltételezi, hogy ugyanazt a tárfiókot használja.
   * **A PackageFile** beállítása **customactivitycontainer/MyDotNetActivity.zip**. Ez a \<formátum containerforthezip\>/\<nameofthezip.zip\>.
   * Az egyéni tevékenység az **InputDataset-et** bemenetként, **a OutputDataset-et** pedig kimenetként veszi fel.
   * Az egyéni tevékenység **linkedServiceName** tulajdonsága az **AzureBatchLinkedService-re**mutat, amely közli a Data Factoryval, hogy az egyéni tevékenységnek kötegen kell futnia.
   * Az **egyidejűség** beállítása fontos. Ha az alapértelmezett értéket használja, amely 1, akkor is, ha két vagy több számítási csomópont van a Batch készletben, a szeletek feldolgozása egymás után történik. Ezért nem használja ki a Batch párhuzamos feldolgozási képességét. Ha az egyidejűséget magasabb értékre **állítja** be, például 2, az azt jelenti, hogy két szelet (két feladatnak felel meg a kötegben) egyszerre dolgozható fel. Ebben az esetben a batch készletben lévő mindkét virtuális gép a rendszer thasználja. Állítsa be megfelelően az egyidejűségi tulajdonságot.
   * Alapértelmezés szerint a virtuális gépeken csak egy feladat (szelet) kerül végrehajtásra. Alapértelmezés szerint a **virtuális gépenkénti feladatok maximális beállítása** 1 egy kötegkészlet esetén. Az előfeltételek részeként létrehozott egy készletet, amelynek tulajdonsága 2-re van állítva. Ezért két adat-előállító szeletek futtathatók egy virtuális gép egy időben.
     - Az **isPaused** tulajdonság alapértelmezés szerint hamis értékre van állítva. A folyamat azonnal fut ebben a példában, mert a szeletek a múltban kezdődnek. Ezt a tulajdonságot **true** értékre állíthatja a folyamat szüneteltetéséhez, és **hamis** ra állíthatja vissza az újraindításhoz.
     -   A **kezdési** és **befejezési** idők öt óra különbséggel vannak. A szeletek óránként készülnek, így a csővezeték öt szeletet állít elő.

1. A folyamat üzembe helyezéséhez kattintson a parancssor **Üzembe helyezés** elemére.

#### <a name="step-5-test-the-pipeline"></a>5. lépés: A csővezeték tesztelése
Ebben a lépésben a folyamatot úgy tesztelheti, hogy fájlokat dob a bemeneti mappákba. Kezdje azzal, hogy minden bemeneti mappához egy fájllal teszteli a folyamatot.

1. Az Azure Portal **Adatgyárpanelen** válassza a **Diagram lehetőséget.**

   ![Ábra](./media/data-factory-data-processing-using-batch/image10.png)

1. A **Diagram** nézetben kattintson duplán a **Bemeneti dataset**bemeneti adatkészletre.

   ![InputDataset (Beviteli adatok beállítása)](./media/data-factory-data-processing-using-batch/image11.png)

1. Az **InputDataset** panel mind az öt szelet készen áll. Figyelje meg a **SZELET KEZDÉSI ÉS** **SZELETBEFEJEZÉSI IDEJÉT** AZ egyes szeleteknél.

   ![Beviteli szelet kezdő- és befejezési időpontjai](./media/data-factory-data-processing-using-batch/image12.png)

1. A **Diagram** nézetben válassza a **Kimenetiadatkészlet lehetőséget.**

1. Az öt kimeneti szelet **kész** állapotban jelenik meg, ha azokat előállították.

   ![Kimeneti szelet kezdő és záró időpontjai](./media/data-factory-data-processing-using-batch/image13.png)

1. A portál segítségével megtekintheti a szeletekhez társított feladatokat, és megtekintheti, hogy az egyes szeletek melyik virtuális gépen futottak. További információt a [Data Factory és](#data-factory-and-batch-integration) a Batch integráció című szakaszban talál.

1. A kimeneti `mycontainer` fájlok `outputfolder` a blob storage alatt jelennek meg.

   ![Kimeneti fájlok a tárolóban](./media/data-factory-data-processing-using-batch/image15.png)

   Öt kimeneti fájl van felsorolva, minden bemeneti szelethez egy. Minden kimeneti fájl tartalma hasonló a következő kimenethez:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Az alábbi ábra azt mutatja be, hogy az adatfeldolgozó szeletek hogyan felelnek meg a Kötegben lévő feladatoknak. Ebben a példában egy szeletnek csak egy futtatása van.

   ![Szeletleképezési diagram](./media/data-factory-data-processing-using-batch/image16.png)

1. Most próbálja meg több fájlt egy mappában. Hozza létre a **2015-11-06-01**mappában található **file2.txt**, **file3.txt**, **file4.txt**és **file5.txt** fájlt a 2015-11-06-01 mappában található file.txt fájllal.

1. A kimeneti mappában törölje a **2015-11-11-16-01.txt**kimeneti fájlt.

1. A **KimenetIAdatok készlet** panelen kattintson a jobb gombbal a **szelet kezdési időpontja** beállítással, amely **2015.16.11-re van állítva 01:00:00 AM.** A szelet újrafuttatásához/újrafeldolgozásához válassza a **Futtatás** lehetőséget. A szelet most már öt fájlt tartalmaz egy fájl helyett.

    ![Futtassa a következőt:](./media/data-factory-data-processing-using-batch/image17.png)

1. Miután a szelet fut, és állapota **Ready**, ellenőrizze a tartalmát a kimeneti fájl ban ezt a szeletet (**2015-11-16-01.txt**). A kimeneti `mycontainer` fájl `outputfolder` alatt jelenik meg a blob storage. A szelet minden fájljának kell lennie egy sornak.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Ha nem törölte a 2015-11-16-01.txt kimeneti fájlt, mielőtt öt bemeneti fájllal próbálkozott volna, akkor az előző szelet futtatása és az aktuális szelet öt sora jelenik meg. Alapértelmezés szerint a tartalom hozzáfűzi a kimeneti fájlt, ha már létezik.
>
>

#### <a name="data-factory-and-batch-integration"></a>Adatgyár és kötegintegráció
A Data Factory szolgáltatás létrehoz egy `adf-poolname:job-xxx`feladatot a Batch-ben a névvel.

![Kötegelt feladatok](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

A feladatban egy feladat jön létre a szelet minden egyes tevékenységfuttatásához. Ha 10 szelet készen áll a feldolgozásra, 10 feladat jön létre a feladatban. Egynél több szelet fut párhuzamosan, ha több számítási csomópont van a készletben. Ha a számítási csomópontonkénti feladatok maximális száma egynél nagyobb, egynél több szelet futhat ugyanazon a számítási számítógépen.

Ebben a példában öt szelet van, így öt feladat van a Kötegben. Ha az **egyidejűség** **5-re** van állítva a JSON-folyamatban az adat-előállítóban, és a **Maximális feladatok virtuális gépenként** 2-re **2** van állítva a Batch készletben **2** virtuális géppel, a feladatok gyorsan futnak. (Ellenőrizze a tevékenységek kezdési és befejezési időpontjait.)

A portál segítségével megtekintheti a kötegelt feladatot és a szeletekhez társított feladatokat, és megtekintheti, hogy az egyes szeletek melyik virtuális gépen futottak.

![Kötegelt feldolgozási feladatok](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Hibakeresés a folyamaton
A hibakeresés néhány alapvető technikából áll.

1. Ha a bemeneti szelet nincs **Kész**beállításra, ellenőrizze, hogy a bemeneti mappa szerkezete helyes-e, és hogy a file.txt létezik-e a bemeneti mappákban.

   ![Beviteli mappa szerkezete](./media/data-factory-data-processing-using-batch/image3.png)

1. Az egyéni tevékenység **végrehajtása** metódusában használja az **IActivityLogger** objektumot a problémák elhárítását segítő információk naplózásához. A naplózott üzenetek megjelennek a felhasználó\_0.log fájljában.

   A **KimenetIData-készlet** panelen jelölje ki a szeletet az adott szelet **Adat szeletpaneljének** megtekintéséhez. A **Tevékenység futtatása**csoportban egy tevékenység fut a szelethez. Ha a parancssávon a **Futtatás** lehetőséget választja, akkor egy másik tevékenységfuttatást is indíthat ugyanarra a szeletre.

   Amikor kiválasztja a tevékenység futtatásához, megjelenik a **Tevékenység futtatási részletek** panel a naplófájlok listáját. A naplózott üzenetek\_a felhasználó 0.log fájljában jelennek meg. Hiba esetén három tevékenység fut, mert az újrapróbálkozások száma 3-ra van állítva a folyamat/tevékenység JSON.When a error occurs, you see three activity runs because the retry count is set to 3 in the pipeline/activity JSON. Amikor kiválasztja a tevékenység futtatását, megjelennek azok a naplófájlok, amelyeket áttekinthet a hiba elhárításához.

   ![OutputDataset és Data szeletpengék](./media/data-factory-data-processing-using-batch/image18.png)

   A naplófájlok listájában válassza a **user-0.log**lehetőséget. A jobb oldali panelen az **IActivityLogger.Write** metódus használatának eredményei jelennek meg.

   ![Tevékenységfuttatás részletei panel](./media/data-factory-data-processing-using-batch/image19.png)

   Ellenőrizze a system-0.log rendszerben a rendszerhibaüzeneteket és kivételeket.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. A **PDB-fájl** felvétele a zip-fájlba, hogy a hiba részletei olyan információkkal rendelkezhessenek, mint például a hívásverem, ha hiba történik.

1. Az egyéni tevékenység zip-fájljában lévő összes fájlnak a legfelső szinten kell lennie almappák nélkül.

   ![Egyéni tevékenység zip fájl lista](./media/data-factory-data-processing-using-batch/image20.png)

1. Győződjön meg arról, hogy **az assemblyName** (MyDotNetActivity.dll), **a entryPoint** (MyDotNetActivity.MyDotNetActivity), **a packageFile** (customactivitycontainer/MyDotNetActivity.zip) és a **packageLinkedService** (a zip-fájlt tartalmazó blobtárolóra mutat) a megfelelő értékekre van beállítva.

1. Ha hibát javított, és újra fel szeretné dolgozni a szeletet, kattintson a jobb gombbal a szeletre a **KimenetIDataset** panelen, és válassza a **Futtatás parancsot.**

   ![OutputDataset panel Futtatás a beállítás](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Egy tároló a blob `adfjobs`storage nevű. Ez a tároló nem törlődik automatikusan, de biztonságosan törölheti, miután befejezte a megoldás tesztelését. Hasonlóképpen az adatfeldolgozó megoldás létrehoz `adf-\<pool ID/name\>:job-0000000001`egy batch feladat nevű. A megoldás tesztelése után törölheti ezt a feladatot.
   >
   >
1. Az egyéni tevékenység nem használja a csomagból származó **app.config** fájlt. Ezért ha a kód beolvassa a konfigurációs fájlból származó kapcsolati karakterláncokat, az nem működik futásidőben. A Batch használata során ajánlott az Azure Key Vaultban található titkos kulcsok megtartása. Ezután egy tanúsítványalapú egyszerű szolgáltatás segítségével védje a key vault és a tanúsítvány terjesztése a batch készlet. A .NET egyéni tevékenység futásidőben hozzáférhet a kulcstartó ból származó titkos kulcsokhoz. Ez az általános megoldás bármilyen típusú titkos titokra méretezhető, nem csak egy kapcsolati karakterláncra.

    Van egy könnyebb megoldás, de ez nem ajánlott eljárás. Létrehozhat egy SQL-adatbázishoz csatolt szolgáltatást kapcsolati karakterlánc-beállításokkal. Ezután létrehozhat egy adatkészletet, amely a csatolt szolgáltatást használja, és az adatkészletet dummy bemeneti adatkészletként az egyéni .NET tevékenységhez láncolja. Ezután elérheti a csatolt szolgáltatás kapcsolati karakterláncát az egyéni tevékenységkódban. Ez kellet volna dolgozik finom -on futásidő.  

#### <a name="extend-the-sample"></a>A minta kiterjesztése
Ezt a mintát kiterjesztheti, ha többet szeretne megtudni a Data Factory és a Batch szolgáltatásairól. Ha például a szeleteket egy másik időtartományban szeretné feldolgozni, tegye a következő lépéseket:

1. A következő almappákat `inputfolder`adja hozzá: 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 és 2015-11-16-09. Helyezzen bemeneti fájlokat ezekbe a mappákba. Módosítsa a folyamat befejezési `2015-11-16T05:00:00Z` `2015-11-16T10:00:00Z`idejét a-ra. A **Diagram** nézetben kattintson duplán a **Beviteliadatok készletelemre,** és ellenőrizze, hogy a bemeneti szeletek készen állnak-e. Kattintson duplán **a Kimenetiadatokkészletre** a kimeneti szeletek állapotának megtekintéséhez. Ha **kész** állapotban vannak, ellenőrizze a kimeneti fájlok kimeneti mappájában.

1. Növelje vagy csökkentse az **egyidejűségi** beállítást annak megértéséhez, hogy ez hogyan befolyásolja a megoldás teljesítményét, különösen a Batch-en előforduló feldolgozást. Az **egyidejűségi** beállításról további információt a "4.

1. Hozzon létre egy készletet, amely nek nagyobb/alacsonyabb **Maximális feladatok virtuális gépenként.** A létrehozott új készlet használatához frissítse a Batch csatolt szolgáltatást az adat-előállító megoldásban. A **Virtuális gépenkénti feladatok maximális értékéről** a "4.

1. Hozzon létre egy kötegkészletet az **automatikus skálázási** funkcióval. A kötegkészletben lévő számítási csomópontok automatikus méretezése az alkalmazás által használt feldolgozási teljesítmény dinamikus beállítása.

    A mintaképlet itt a következő viselkedést éri el. Amikor a készlet eredetileg létre, egy virtuális gép kezdődik. A $PendingTasks metrika határozza meg a feladatok számát a futó és aktív (várólistára helyezett) állapotokban. A képlet megkeresi a függőben lévő feladatok átlagos számát az elmúlt 180 másodpercben, és ennek megfelelően állítja be a TargetDedicated függvényt. Ez biztosítja, hogy a TargetDedicated soha nem lépi túl a 25 virtuális gépet. Az új feladatok elküldésekor a készlet automatikusan növekszik. A feladatok befejeződésével a virtuális gépek egyenként válnak ingyenessé, és az automatikus skálázás csökkenti a virtuális gépeket. A startingNumberOfVMs és a maxNumberofVMs az igényekhez igazítható.

    Automatikus skálázási képlet:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   További információt a [Kötegkészlet számítási csomópontjainak automatikus méretezése](../../batch/batch-automatic-scaling.md)című témakörben talál.

   Ha a készlet az alapértelmezett [autoScaleScaleEvaluationInterval értéket](https://msdn.microsoft.com/library/azure/dn820173.aspx)használja, a Batch szolgáltatás 15–30 percet is igénybe vehet a virtuális gép előkészítése az egyéni tevékenység futtatása előtt. Ha a készlet egy másik autoScaleScaleEvaluationInterval, a Batch szolgáltatás lehet, hogy autoScaleEvaluationInterval plusz 10 perc.

1. A mintamegoldásban az **Execute** metódus **elindítja** a Calculate metódust, amely egy bemeneti adatszeletet dolgoz fel egy kimeneti adatszelet létrehozásához. Saját metódust írhat a bemeneti adatok feldolgozásához, és **lecserélheti** a Számítási metódus hívását az **Execute** metódusban a metódus hívására.

### <a name="next-steps-consume-the-data"></a>Következő lépések: Használja fel az adatokat
Az adatok feldolgozása után felhasználhatja azokat olyan online eszközökkel, mint a Power BI. Az alábbiakban hivatkozásokat talál a Power BI és az Azure-beli használat megértéséhez:

* [Adatkészlet ek feltárása a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Ismerkedés a Power BI Desktopprogrammal](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Adatok frissítése a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure és Power BI: Alapszintű áttekintés](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referencia
* [Azure-adatgyár](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Bevezetés a Data Factory szolgáltatásba](data-factory-introduction.md)
  * [Első lépések a Data Factory-val](data-factory-build-your-first-pipeline.md)
  * [Egyéni tevékenységek használata adatfeldolgozó folyamatban](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [A köteg alapjai](../../batch/batch-technical-overview.md)
  * [A Batch szolgáltatásainak áttekintése](../../batch/batch-api-basics.md)
  * [Batch-fiók létrehozása és kezelése az Azure Portalon](../../batch/batch-account-create-portal.md)
  * [Első lépések a .NET Batch ügyféltárával](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx

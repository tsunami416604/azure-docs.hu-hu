---
title: A Microsoft Azure Storage adatátviteli könyvtár adatátvitel |} A Microsoft Docs
description: Az adatátviteli kódtára segítségével áthelyezi vagy másolhat blob és a fájl tartalmát a. Adatok másolása az Azure Storage a helyi fájlokból vagy adatmásolás belül vagy tárfiókok között. Egyszerűen migrálhatja az adatokat az Azure Storage.
services: storage
author: seguler
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: seguler
ms.component: common
ms.openlocfilehash: 809010727f0caf2e340bb62fff24e5ffff52f88a
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464680"
---
# <a name="transfer-data-with-the-microsoft-azure-storage-data-movement-library"></a>Adatok áthelyezése az a Microsoft Azure Storage adatátviteli könyvtár

## <a name="overview"></a>Áttekintés
A Microsoft Azure Storage adatátviteli könyvtár egy platformfüggetlen, nyílt forráskódú kódtár, amely a nagy teljesítményű letöltésére, valamint az Azure Storage-Blobok és fájlok másolása szolgál. Ebben a könyvtárban az alapvető adatátviteli keretrendszeren biztosító [AzCopy](../storage-use-azcopy.md). Az adatátviteli könyvtár, amelyek nem érhetők el a hagyományos kényelmes módszert biztosít [.NET Azure Storage ügyféloldali kódtár](../blobs/storage-dotnet-how-to-use-blobs.md). Ez magában foglalja a párhuzamos műveletek számának beállítása, átviteli nyomon követni, ott folytathatja a megszakított átvitel, és még sok más lehetőség.

Ebben a könyvtárban is használja a .NET Core, ami azt jelenti, amikor a .NET-alkalmazások létrehozásához Windows, Linux és MacOS rendszeren használhatja. .NET Core keretrendszerrel kapcsolatos további tudnivalókért tekintse meg a [.NET Core dokumentációja](https://dotnet.github.io/). Ez a tár is használható olyan hagyományos alkalmazások, .NET-keretrendszer Windows.

Ez a dokumentum azt ismerteti, hogyan hozzon létre egy .NET Core-konzolalkalmazást, amely Windows, Linux és MacOS rendszeren fut, és hajtja végre a következő esetekben:

- Fájlok és könyvtárak feltöltése a Blob Storage.
- Adja meg a párhuzamos műveletek számát, adatátvitel során.
- Adatok átvitele folyamatban nyomon követése.
- Megszakított adatátvitel folytatódik.
- A Blob Storage URL-címről fájl másolása.
- A Blob Storage másolása Blob Storage-ból.

**Mire lesz szüksége:**

* [Visual Studio Code](https://code.visualstudio.com/)
* Egy [Azure-tárfiók](storage-quickstart-create-account.md)

> [!NOTE]
> Ez az útmutató feltételezi, hogy már ismeri a [Azure Storage](https://azure.microsoft.com/services/storage/). Ha nincs, Olvasás a [Azure Storage bemutatása](storage-introduction.md) dokumentáció hasznos lehet. Ennél is fontosabb, kell [hozzon létre egy tárfiókot](storage-quickstart-create-account.md) az adatátviteli könyvtár használatához.
>
>

## <a name="setup"></a>Beállítás

1. Látogasson el a [.NET Core telepítési útmutató](https://www.microsoft.com/net/core) .NET Core telepítéséhez. A környezet kiválasztásakor válassza ki a parancssori kapcsolót.
2. A parancssorból hozzon létre egy könyvtárat a projekthez. Keresse meg az ebben a könyvtárban, majd írja be `dotnet new console -o <sample-project-name>` C# konzol projekt létrehozásához.
3. Nyissa meg ezt a könyvtárat a Visual Studio Code-ban. Ebben a lépésben gyorsan megteheti a parancssor beírásával `code .` a Windows.
4. Telepítse a [C#-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) , a Visual Studio-piactéren. Indítsa újra a Visual Studio Code-ot.
5. Ezen a ponton futtatásánál két kérést kell látnia. Egyet az hozzáadása "fejlesztése és hibakeresése a szükséges objektumokat." Kattintson az "Igen" gombra. Egy másik kérdés megoldatlan függőségek visszaállításához van. Kattintson a "visszaállítás".
6. Módosítsa `launch.json` alatt `.vscode` külső Terminálszolgáltatások adatokként a konzolt. Ez a beállítás olvassa el ` "console": "externalTerminal"`
7. A Visual Studio Code lehetővé teszi a .NET Core-alkalmazások hibakeresését. Találati `F5` futtassa az alkalmazást, és ellenőrizze, hogy működik-e a telepítőt. Megjelenik a "Hello World!" a nyomtatott a konzolhoz.

## <a name="add-data-movement-library-to-your-project"></a>Adatátviteli könyvtár hozzáadása a projekthez

1. Adja hozzá az adatátviteli könyvtár a legújabb verzióját a `dependencies` szakaszában a `<project-name>.csproj` fájlt. Ez a verzió lenne írása idején `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. Kérdés megjelenjen a projekt visszaállítása. A "visszaállítás" gombra. Is helyreállíthatja a projekthez a parancssorból a parancs beírásával `dotnet restore` a projektkönyvtárba gyökerében.

Módosítsa `<project-name>.csproj`:

    <Project Sdk="Microsoft.NET.Sdk">

        <PropertyGroup>
            <OutputType>Exe</OutputType>
            <TargetFramework>netcoreapp2.0</TargetFramework>
        </PropertyGroup>
        <ItemGroup>
            <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
            </ItemGroup>
        </Project>

## <a name="set-up-the-skeleton-of-your-application"></a>Állítsa be az alkalmazás szerkezete
A legfontosabb, hogy az alkalmazás a "skeleton" kód be van állítva. Ez a kód USA bekéri a Tárfiók nevét és kulcsát, és ezeket a hitelesítő adatokat használ, hozzon létre egy `CloudStorageAccount` objektum. Ez az objektum kezelése a Storage-fiókot az összes átviteli forgatókönyv szolgál. A kód számunkra, hogy válassza ki az adatátviteli művelet végrehajtásához szeretnénk is kéri.

Módosítsa `Program.cs`:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="transfer-local-file-to-azure-blob"></a>Helyi fájl átvitele az Azure Blobba
Adja hozzá a metódusokat `GetSourcePath` és `GetBlob` való `Program.cs`:

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

Módosítsa a `TransferLocalFileToAzureBlob` módszer:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

Ez a kód kéri velünk a kapcsolatot egy helyi fájlba, egy új vagy meglévő tároló nevét és a blob nevét, egy új elérési útját. A `TransferManager.UploadAsync` metódus végzi el a feltöltés ezen információk alapján.

Találati `F5` az alkalmazás futtatásához. Ellenőrizheti, hogy történt-e a feltöltés a tárfiók megtekintésével a [Microsoft Azure Storage Explorer](http://storageexplorer.com/).

## <a name="set-number-of-parallel-operations"></a>Beállítása a párhuzamos műveletek száma
Az adatátviteli könyvtár által kínált nagyszerű jellemzője megadhatja azokat az adatokat átvitel teljesítménynövelés párhuzamos műveletek számát. Alapértelmezés szerint az adatátviteli könyvtár beállítja a párhuzamos műveletek számát 8 * a gépen a magok számát.

Ne feledje, hogy sok párhuzamos művelet egy kis sávszélességű környezetben túlterhelhetik futó a hálózati kapcsolatot, és ténylegesen a műveletek teljesen befejezze megelőzése. Ez a beállítás határozza meg, mi működik legjobban alapján a rendelkezésre álló hálózati sávszélességet a kísérletezhet kell.

Adjunk néhány kódot, amely lehetővé teszi számunkra, hogy állítsa be a párhuzamos műveletek számát. Adjunk is a kódot, amely túllépi az időt az adatátvitelhez végrehajtásához.

Adjon hozzá egy `SetNumberOfParallelOperations` metódust `Program.cs`:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Módosítsa a `ExecuteChoice` módszer `SetNumberOfParallelOperations`:

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

Módosítsa a `TransferLocalFileToAzureBlob` időzítő módszer:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>Track átvitel folyamatban
Kiválóan alkalmazható, hogy mennyi ideig tartottak az adatok átviteléhez. Azonban tudja, az átviteli folyamat előrehaladását *során* az adatátviteli művelet még jobb lenne. Ebben a forgatókönyvben eléréséhez, létre kell hozni egy `TransferContext` objektum. A `TransferContext` objektumot két formában érhető el: `SingleTransferContext` és `DirectoryTransferContext`. Az előbbi átvitelére egyetlen fájlt (amely mit teszünk most), az utóbbi pedig egy könyvtárat (amely a későbbiekben teszünk) fájlok átvitele.

Adja hozzá a metódusokat `GetSingleTransferContext` és `GetDirectoryTransferContext` való `Program.cs`:

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

Módosítsa a `TransferLocalFileToAzureBlob` módszer `GetSingleTransferContext`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>A megszakított átvitel folytatása
Egy másik kényelmes megoldás az adatátviteli könyvtár által kínált szolgáltatása a megszakított átvitel folytatódhasson. Adjunk néhány kódot, amely lehetővé teszi számunkra, hogy ideiglenesen írja be a az átadás megszakítása `c`, majd később a 3 másodperc folytatható az átvitel.

Módosítsa `TransferLocalFileToAzureBlob`:

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Eddig nagyon a `checkpoint` érték mindig beállítottuk a `null`. Most azt az átadás megszakítása, ha azt lekérése az utolsó ellenőrzőpont az átvitel, majd használja az új ellenőrzőpont az átadás a környezetben.

## <a name="transfer-local-directory-to-azure-blob-directory"></a>Helyi könyvtárba átvitele az Azure Blob-könyvtár
Címkézést lenne, ha az adatátviteli könyvtár csak továbbíthat egy fájl egy időben. Szerencsére ez nem a helyzet. Az adatátviteli kódtára lehetővé teszi a vihetők át fájlok és az összes alkönyvtárai egy könyvtárat. Adjunk néhány kódot, amely lehetővé teszi számunkra, hogy éppen ezt.

Először adja hozzá a metódust `GetBlobDirectory` való `Program.cs`:

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

Ezután módosítsa `TransferLocalDirectoryToAzureBlobDirectory`:

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Ez a módszer és a metódus egyetlen fájl feltöltése között néhány különbségek vannak. Most használjuk `TransferManager.UploadDirectoryAsync` és a `getDirectoryTransferContext` módszer a korábban létrehozott. Ezenkívül mostantól elérhető egy `options` a feltöltési művelet, amely lehetővé teszi számunkra, hogy jelezheti, hogy kívánja-e alkönyvtárak bevonni a feltöltési értéket.

## <a name="copy-file-from-url-to-azure-blob"></a>URL-cím az Azure-Blobba fájl másolása
Most adjuk hozzá a kódot, amely lehetővé teszi számunkra, hogy a fájl másolása az Azure Blob URL-címről.

Módosítsa `TransferUrlToAzureBlob`:

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

A szolgáltatás egy fontos használatieset van, amikor szüksége van egy másik felhőszolgáltatást (például: AWS) adatok áthelyezése az Azure-bA. Mindaddig, amíg van egy URL-címet, amely hozzáférést biztosít az erőforráshoz, egyszerűen áthelyezheti ennek az erőforrásnak az Azure-Blobok használatával a `TransferManager.CopyAsync` metódust. Ez a módszer is bemutatja egy új logikai paraméter. Ez a paraméter beállítása `true` azt jelzi, hogy szeretnénk tenni egy aszinkron kiszolgálóoldali másolat. Ez a paraméter beállítása `false` azt jelzi, hogy szinkron másolatot – ami azt jelenti, az erőforrás először a helyi számítógépre letöltött, majd az Azure-Blobba feltölteni. Azonban szinkron másolatot jelenleg csak egy másikra egy Azure Storage-erőforrások másolása érhető el.

## <a name="transfer-azure-blob-to-azure-blob"></a>Az Azure Blob átvitele az Azure-Blobba
Egy másik szolgáltatás, amely egyedi az adatátviteli könyvtár által biztosított rendszer azon képessége, egy Azure Storage-erőforrások másolása egy másikba.

Módosítsa `TransferAzureBlobToAzureBlob`:

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

Ebben a példában beállított logikai paraméter `TransferManager.CopyAsync` való `false` jelzi, hogy szeretnénk tenni egy szinkron másolatot. Ez azt jelenti, hogy az erőforrás először a helyi számítógépre letöltött, akkor az Azure-Blobba feltölteni. Ügyeljen arra, hogy a másolási művelet egységes sebesség nagyszerű lehetőséget, a szinkron példány lehetőség. Ezzel szemben egy aszinkron kiszolgálóoldali másolat sebességétől szolgáltatás a rendelkezésre álló hálózati sávszélességet a kiszolgálón, amely ingadozhaz függ. Szinkron másolatot azonban előfordulhat, hogy létre további kimenő forgalmi költségek képest aszinkron példányt. Az ajánlott módszer, hogy szinkron másolatot egy Azure virtuális gépen, amely a forrás tárfiókban kimenő forgalmi költségek elkerülése érdekében ugyanabban a régióban.

## <a name="conclusion"></a>Összegzés
Az adatok mozgását alkalmazás most már befejeződött. [A Githubon érhető el a teljes Kódmintát](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>További lépések
Az első lépéseket, létrehozott egy alkalmazást, amely kommunikál az Azure Storage és a Windows, Linux és macOS futtatja. Ez az első lépések a Blob Storage összpontosít. Azonban ezt a tudást is alkalmazható a File Storage. További tudnivalókért tekintse meg [Azure Storage adatátviteli könyvtár dokumentációjában](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

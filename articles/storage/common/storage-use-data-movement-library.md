---
title: A Microsoft Azure Storage adatátviteli könyvtár az adatok átviteléhez |} Microsoft Docs
description: Az adatátviteli könyvtár használatával helyezhet át adatokat, vagy a blob és a fájl tartalmát. Adatok másolása az Azure Storage a helyi fájlokból, vagy másolja az adatokat belül vagy tárfiókok között. Adatok áttelepítése egyszerű Azure Storage.
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/27/2017
ms.author: seguler
ms.openlocfilehash: eb96f3697d5369ba96a1b0c491e3eacf09e7aac4
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
ms.locfileid: "27564739"
---
# <a name="transfer-data-with-the-microsoft-azure-storage-data-movement-library"></a>A Microsoft Azure Storage adatátviteli könyvtár az adatok átvitele

## <a name="overview"></a>Áttekintés
A Microsoft Azure Storage adatátviteli könyvtár a platformok közötti nyílt forráskódú-szalagtár egyik célja a nagy teljesítményű feltöltését, letöltése és az Azure Storage Blobs és a fájlok másolása. Ebben a könyvtárban, az alapvető adatátviteli keretrendszeren, amely rendszert működtet [AzCopy](../storage-use-azcopy.md). Az adatátviteli könyvtár, amelyek nem érhetők el a hagyományos a kényelmes módszert biztosít [.NET Azure Storage ügyféloldali kódtár](../blobs/storage-dotnet-how-to-use-blobs.md). Ez lehetővé teszi a párhuzamos műveletek számának beállítása, átviteli nyomon követni, egyszerűen folytathatja a megszakított átvitel, és még sok más.  

Ebben a könyvtárban is használja a .NET Core, ami azt jelenti, hogy a .NET-alkalmazások a Windows, Linux és macOS fejlesztéskor használhatja. A .NET Core kapcsolatos további tudnivalókért tekintse meg a [.NET Core dokumentációja](https://dotnet.github.io/). Ebben a könyvtárban is működik, a hagyományos .NET-keretrendszer alkalmazások Windows. 

Ez a dokumentum bemutatja, hogyan hozzon létre egy .NET Core konzolalkalmazást, amely a Windows, Linux és macOS fut, és hajtja végre a következő esetekben:

- Fájlok és könyvtárak feltöltése a Blob Storage.
- Határozza meg a párhuzamos műveletek számát, adatátvitel során.
- Track adatok átvitel folyamatban van.
- Végezze el újra a megszakított adatátvitel. 
- A Blob Storage URL-címről fájl másolása. 
- A Blob Storage másolása a Blob Storage tárolóban.

**Mire van szüksége:**

* [Visual Studio Code](https://code.visualstudio.com/)
* Egy [Azure-tárfiók](storage-create-storage-account.md#create-a-storage-account)

> [!NOTE]
> Ez az útmutató feltételezi, hogy Ön már ismeri a [Azure Storage](https://azure.microsoft.com/services/storage/). Ha nem, olvasási a [Azure Storage bemutatása](storage-introduction.md) dokumentáció is hasznos. A legfontosabb, kell [hozzon létre egy tárfiókot](storage-create-storage-account.md#create-a-storage-account) az adatátviteli könyvtár elindítására.
> 
> 

## <a name="setup"></a>Beállítás  

1. Látogasson el a [.NET Core telepítési útmutató](https://www.microsoft.com/net/core) .NET Core telepítéséhez. A környezet kiválasztásakor válassza a parancssori kapcsolót. 
2. A parancssorból hozzon létre egy könyvtárat a projekthez. Keresse meg az ebben a könyvtárban, majd írja be a `dotnet new console -o <sample-project-name>` C# konzol projekt létrehozásához.
3. Nyissa meg a könyvtár a Visual Studio Code. Ez a lépés gyorsan megteheti a parancssor beírásával `code .` Windows.  
4. Telepítse a [C# bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) a Visual Studio Code piactérről. Indítsa újra a Visual Studio Code. 
5. Ekkor meg kell jelennie két megjelenő utasításokat. Egyet az hozzáadása "szükséges eszközök létrehozása és hibakeresési." Kattintson az "Igen" gombra. Egy másik kérdés feloldatlan függőségek visszaállítására van. Kattintson a "visszaállítás".
6. Módosítsa `launch.json` alatt `.vscode` konzolként külső Terminálszolgáltatások használandó. Ez a beállítás be kell olvasni` "console": "externalTerminal"`
7. A Visual Studio Code lehetővé teszi a .NET Core-alkalmazások hibakeresését. Találati `F5` futtassa az alkalmazást, és győződjön meg arról, hogy működik-e a beállításai. Láthatja a "Hello World!" kinyomtatott a konzolhoz. 

## <a name="add-data-movement-library-to-your-project"></a>Adatátviteli könyvtár hozzáadása a projekthez

1. Az adatátviteli könyvtár a legújabb verzióját a `dependencies` szakasza a `<project-name>.csproj` fájlt. Ebben a verzióban lenne írásának időpontjában`"Microsoft.Azure.Storage.DataMovement": "0.6.2"` 
2. A kérdés megjelenjen-e a projekt visszaállítása. A "visszaállítás" gombra. Visszaállíthatja a projekt a parancssorból a parancs beírásával `dotnet restore` projektkönyvtárban gyökérkönyvtárában.

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

## <a name="set-up-the-skeleton-of-your-application"></a>Az alkalmazás a vázat beállítása
A legfontosabb végezzük az alkalmazás a "skeleton" kód be van állítva. Ez a kód megadását kéri, a Tárfiók nevét és a fiók kulcsának, és ezeket a hitelesítő adatok használatával hozzon létre egy `CloudStorageAccount` objektum. Ez az objektum a Storage-fiókot az összes átviteli forgatókönyv együttműködhet szolgál. A kódot is kér, hogy válassza ki a fájlátviteli művelettel kell végrehajtani. 

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

## <a name="transfer-local-file-to-azure-blob"></a>Helyi fájl átvitele az Azure-blobba
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

Módosítsa a `TransferLocalFileToAzureBlob` módszert:

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

Ez a kód megadását kéri, elérési útját egy helyi fájlba, egy új vagy meglévő tároló neve és egy új blob neve. A `TransferManager.UploadAsync` metódus hajtja végre a feltöltés ezen információk alapján. 

Találati `F5` az alkalmazás futtatásához. Azt, hogy a feltöltés történt a tárfiók megtekintésével ellenőrizheti a [Microsoft Azure Tártallózó](http://storageexplorer.com/).

## <a name="set-number-of-parallel-operations"></a>Készlet száma párhuzamos művelet
Az adatátviteli könyvtár által kínált kiváló jellemzője meg az adatok átvitel átviteli sebesség növelése párhuzamos műveletek számát. Alapértelmezés szerint az adatátviteli könyvtár beállítja a párhuzamos műveletek számát 8 * a számítógépen lévő magok száma. 

Ne feledje, hogy sok párhuzamos művelet egy kis sávszélességű környezetben ne terhelje tovább a hálózati kapcsolatot, és ténylegesen megakadályozza a műveletek teljes befejezését. Ez a beállítás határozza meg, mi működik legjobban alapján a rendelkezésre álló hálózati sávszélességet a kísérletezhet lesz szüksége. 

Adjunk néhány kódot, amely lehetővé teszi, hogy állítsa be a párhuzamos műveletek számát. Adjunk is a kódot, amely alkalommal fordult elő az mennyi időt vesz igénybe az áthelyezés végrehajtásához.

Adja hozzá a `SetNumberOfParallelOperations` módszert `Program.cs`:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

Módosítsa a `ExecuteChoice` használandó módszer `SetNumberOfParallelOperations`:

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

Módosítsa a `TransferLocalFileToAzureBlob` időzítő használandó módszert:

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

## <a name="track-transfer-progress"></a>Track átvitel állapota
Annak ismerete, hogy mennyi ideig tartott az adatok átvitele nagy. Azonban igényt az átviteli előrehaladásának megtekintéséhez *során* az adatátviteli művelet még élvezetesebbé lenne. Ebben a forgatókönyvben eléréséhez létre kell hoznunk egy `TransferContext` objektum. A `TransferContext` objektum két formában származik: `SingleTransferContext` és `DirectoryTransferContext`. A korábbi átvitelére egyetlen fájlba (amely azt még tevékenységeit most), az utóbbi pedig egy könyvtárat (amely azt később felvenni) fájlok átvitele.

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

Módosítsa a `TransferLocalFileToAzureBlob` használandó módszer `GetSingleTransferContext`:

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
Az adatátviteli könyvtár által kínált egy másik kényelmes szolgáltatás azt a képességet a megszakított átvitel folytatása. Adjunk néhány kódot, amely lehetővé teszi, hogy ideiglenesen szakítsa meg a írja be az átvitelt `c`, és ezután úgy folytatódik, az átviteli 3 másodperc múlva.

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

Eddig a `checkpoint` érték mindig állították be `null`. Most az átvitel megszakítása, ha azt lekérése az utolsó ellenőrzőpont az átvitel, akkor használja ezt az új ellenőrzőpontot az átviteli környezetben. 

## <a name="transfer-local-directory-to-azure-blob-directory"></a>Helyi könyvtár átvitele az Azure Blob-könyvtár
Címkézést lenne, ha az adatátviteli könyvtár csak továbbíthat egy fájlban egyszerre. Szerencsére ez nem a helyzet. Az adatátviteli könyvtár lehetővé teszi a fájlok és alkönyvtárak könyvtár átvitele. Adjunk néhány kódot, amely lehetővé teszi, hogy éppen ez.

Első lépésként adja meg a metódus `GetBlobDirectory` való `Program.cs`:

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

Ez a metódus és a metódus egyetlen fájl feltöltése között néhány különbségek vannak. Most használunk `TransferManager.UploadDirectoryAsync` és a `getDirectoryTransferContext` korábban létrehozott metódust. Emellett mostantól elérhető egy `options` a feltöltési művelet, amely lehetővé teszi annak jelzésére, hogy szeretnénk alkönyvtárak szerepeljenek a feltöltési értéket. 

## <a name="copy-file-from-url-to-azure-blob"></a>Fájl másolása Azure Blob URL-címe
Most adjuk hozzá a kódot, amely lehetővé teszi, hogy a fájl másolása az Azure Blob URL-címről. 

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

A szolgáltatás egy fontos használati eset esetén az adatok áthelyezése egy másik felhőszolgáltatáshoz (pl. AWS) Azure kell. Mindaddig, amíg hozzáférést biztosít az erőforrás URL-CÍMMEL rendelkezik, egyszerűen áthelyezheti a erőforrást az Azure BLOB használatával a `TransferManager.CopyAsync` metódust. Ez a módszer is bevezet egy új logikai paraméter. Ha a paramétert a `true` azt jelzi, hogy szeretnénk egy aszinkron kiszolgálóoldali lemásolni. Ha a paramétert a `false` azt jelzi, ami azt jelenti, az erőforrás előbb letöltődik a helyi számítógépre, az Azure Blob feltöltött - szinkron másolatot. Azonban szinkron másolatot érhető el jelenleg csak egy Azure Storage erőforrás egy másikra történő másolását. 

## <a name="transfer-azure-blob-to-azure-blob"></a>Az Azure Blob átvitele az Azure Blob
Egy másik szolgáltatás, amely egyedileg az adatátviteli könyvtár biztosítja azt a képességet egy Azure Storage-erőforrások másolása a másikra. 

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

Az ebben a példában azt állítsa be a logikai paraméter `TransferManager.CopyAsync` való `false` annak jelzésére, hogy azt szeretné szinkron másolatot. Ez azt jelenti, hogy az erőforrás előbb letöltődik a helyi számítógépen, majd az Azure Blob feltöltése. A szinkron másolatot elem kiváló módja annak, ügyeljen arra, hogy a másolási művelet egységes sebesség. Ezzel szemben egy aszinkron kiszolgálóoldali másolatot sebességétől függ a rendelkezésre álló hálózati sávszélességet a kiszolgálón, amely hajlamos is. Azonban a szinkron másolatot hozhat létre további kilépő költség aszinkron másolási képest. Az ajánlott módszer, hogy szinkron másolatot használja egy Azure virtuális gép, amely ugyanabban a régióban, a forrás tárolási fiókkal kilépő költségek elkerülése érdekében.

## <a name="conclusion"></a>Összegzés
Az adatok mozgása alkalmazás most már befejeződött. [A teljes kód mintát a Githubon érhető el](https://github.com/azure-samples/storage-dotnet-data-movement-library-app). 

## <a name="next-steps"></a>További lépések
Az első lépések, létrehozott egy alkalmazás, amely együttműködik az Azure Storage és a Windows, Linux és macOS futtatja. Ez az első lépések a Blob Storage összpontosít. Azonban ez azonos Tudásbázis alkalmazhatja a File Storage. További tudnivalókért tekintse meg [Azure Storage adatátviteli könyvtár referenciadokumentációt](https://azure.github.io/azure-storage-net-data-movement).

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]





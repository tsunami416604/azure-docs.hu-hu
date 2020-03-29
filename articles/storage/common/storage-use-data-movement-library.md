---
title: Adatok átvitele a .NET adatmozgatási tárával
titleSuffix: Azure Storage
description: Az Adatmozgatás tárral adatokat helyezhet át vagy másolhat blob- és fájltartalomba. Adatok másolása az Azure Storage-ba helyi fájlokból, vagy adatok másolása a tárfiókokon belül vagy között. Egyszerűen áttelepítheti adatait az Azure Storage-ba.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5b8654500fd697685b38e4f51ba1069e0cf6ccfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942909"
---
# <a name="transfer-data-with-the-data-movement-library"></a>Adatok átvitele az adatátviteli kódtárral

Az Azure Storage Data Movement könyvtár egy platformfüggetlen nyílt forráskódú könyvtár, amely et a blobok és fájlok nagy teljesítményű feltöltésére, letöltésére és másolására terveztek. Az adatmozgatási könyvtár kényelmes módszereket biztosít, amelyek nem érhetők el az Azure Storage-ügyfélkódtárban . Ezek a módszerek lehetővé teszik a párhuzamos műveletek számának beállítását, az átvitel előrehaladásának nyomon követését, a visszavont átvitel egyszerű folytatását és még sok mást.

Ez a könyvtár a .NET Core-t is használja, ami azt jelenti, hogy használhatja a .NET alkalmazások készítéséhez Windows, Linux és macOS rendszeren. A .NET Core-ról a [.NET Core dokumentációjában](https://dotnet.github.io/)olvashat bővebben. Ez a könyvtár a windowsos .

Ez a dokumentum bemutatja, hogyan hozhat létre Windows, Linux és macOS rendszeren futó .NET Core konzolalkalmazást, és hogyan hajthatja végre a következő eseteket:

- Fájlok és könyvtárak feltöltése a Blob Storage szolgáltatásba.
- Adja meg a párhuzamos műveletek számát az adatok átvitelekekekekén.
- Az adatátvitel folyamatának nyomon követése.
- Folytassa a visszavont adatátvitelt.
- Fájl másolása URL-címről blob storage-ba.
- Másolás a Blob Storage-ból a Blob Storage-ba.

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio kód](https://code.visualstudio.com/)
- Egy [Azure-tárfiók](storage-account-create.md)

## <a name="setup"></a>Telepítés

1. A [.NET Core](https://www.microsoft.com/net/core) telepítéséhez látogasson el a .NET Core telepítési útmutatóba. A környezet kiválasztásakor válassza a parancssori kapcsolót.
2. A parancssorból hozzon létre egy könyvtárat a projekthez. Navigáljon ebbe a `dotnet new console -o <sample-project-name>` könyvtárba, majd írja be a C# konzolprojekt létrehozásához.
3. Nyissa meg ezt a könyvtárat a Visual Studio-kódban. Ez a lépés gyorsan elvégezhető `code .` a parancssorból a Windows beírásával.
4. Telepítse a [C# bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) a Visual Studio Kódpiactérről. Indítsa újra a Visual Studio kódját.
5. Ezen a ponton két kérdést kell látnia. Az egyik a "szükséges eszközök létrehozásához és hibakereséséhez" való hozzáadása. Kattintson az "igen" gombra. Egy másik kérdés a feloldatlan függőségek visszaállítása. Kattintson a "visszaállítás" gombra.
6. Módosítsa `launch.json` `.vscode` a külső terminálkonzolként való használatához. Ez a beállítás a következőként`"console": "externalTerminal"`
7. A Visual Studio Code lehetővé teszi a .NET Core alkalmazások hibakeresését. Nyomja `F5` meg az alkalmazás futtatásához, és ellenőrizze, hogy a beállítás működik.Hit to run your application and verify that your setup is working. Látnod kellene a "Hello World!"-et. a konzolra.

## <a name="add-the-data-movement-library-to-your-project"></a>Az Adatmozgatás könyvtár hozzáadása a projekthez

1. Adja hozzá az Adatmozgatás `dependencies` könyvtár legújabb `<project-name>.csproj` verzióját a fájl szakaszához. Abban az időben az írás, ez a verzió lenne`"Microsoft.Azure.Storage.DataMovement": "0.6.2"`
2. A projekt visszaállításához egy üzenet jelenik meg. Kattintson a "visszaállítás" gombra. A projektet a parancssorból is visszaállíthatja, ha beírja a parancsot `dotnet restore` a projektkönyvtár gyökerébe.

Módosítás `<project-name>.csproj`:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>Az alkalmazás vázának beállítása

Az első dolog, amit teszünk, hogy felállítjuk az alkalmazásunk "csontváz" kódját. Ez a kód egy tárfiók nevét és fiókkulcsát kéri, `CloudStorageAccount` és ezeket a hitelesítő adatokat használja egy objektum létrehozásához. Ez az objektum a storage-fiókunkkal való interakcióra szolgál minden átviteli forgatókönyvben. A kód arra is kéri, hogy válasszuk ki az átviteli művelet típusát, amelyet végre szeretnénk hajtani.

Módosítás `Program.cs`:

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

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

## <a name="upload-a-local-file-to-a-blob"></a>Helyi fájl feltöltése blobba

Adja hozzá `GetSourcePath` `GetBlob` a `Program.cs`módszereket, és :

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

Módosítsa `TransferLocalFileToAzureBlob` a módszert:

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

Ez a kód egy helyi fájl elérési útját, egy új vagy meglévő tároló nevét és egy új blob nevét kéri. A `TransferManager.UploadAsync` metódus ezen információk alapján hajtja végre a feltöltést.

Hit `F5` futtatni a kérelmet. A feltöltés tanuskodhat, ha megtekinti a Tárfiókot a [Microsoft Azure Storage Explorer](https://storageexplorer.com/)programban.

## <a name="set-the-number-of-parallel-operations"></a>A párhuzamos műveletek számának beállítása

Az adatmozgatási függvénytár által kínált egyik funkció a párhuzamos műveletek számának beállítása az adatátviteli átviteli sebesség növelése érdekében. Alapértelmezés szerint az Adatmozgatás könyvtár a párhuzamos műveletek számát 8 *-ra állítja a számítógépen lévő magok számára.

Ne feledje, hogy az alacsony sávszélességű környezetben számos párhuzamos művelet túlterhelheti a hálózati kapcsolatot, és ténylegesen megakadályozhatja a műveletek teljes befejezését. Ezzel a beállítással kell kísérleteznie, hogy a rendelkezésre álló hálózati sávszélesség alapján meghatározza, hogy mi működik a legjobban.

Adjunk hozzá néhány kódot, amely lehetővé teszi számunkra, hogy állítsa be a párhuzamos műveletek számát. Adjunk hozzá olyan kódot is, amely megkeresi, hogy mennyi ideig tart az átvitel befejezése.

Adjon `SetNumberOfParallelOperations` hozzá `Program.cs`egy módszert a következőhöz:

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

A `ExecuteChoice` használni `SetNumberOfParallelOperations`kívánt módszer módosítása:

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

Módosítsa `TransferLocalFileToAzureBlob` a módszert időzítő használatára:

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

## <a name="track-transfer-progress"></a>A nyomon követése átvitel folyamata

Az adatok átvitelének ideje segít. Azonban, hogy képes látni a haladást az átadás *során* az átadási művelet még jobb lenne. Ennek a forgatókönyvnek az eléréséhez létre kell hoznunk egy `TransferContext` objektumot. Az `TransferContext` objektum két formában `SingleTransferContext` `DirectoryTransferContext`kapható: és . Az előbbi egy fájl átvitelére szolgál, az utóbbi pedig a fájlok könyvtárának átvitelére.

Adja hozzá `GetSingleTransferContext` `GetDirectoryTransferContext` a `Program.cs`módszereket, és :

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

A `TransferLocalFileToAzureBlob` használni `GetSingleTransferContext`kívánt módszer módosítása:

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

## <a name="resume-a-canceled-transfer"></a>Visszavont átvitel folytatása

Az Adatmozgalom könyvtár a törölt átvitel folytatásának képessége. Adjunk hozzá néhány kódot, amely lehetővé teszi számunkra, hogy ideiglenesen megszünteti az átvitel tírja be, `c`majd folytassa az átvitel 3 másodperccel később.

Módosítás `TransferLocalFileToAzureBlob`:

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

Eddig az értékünk `checkpoint` mindig is `null`a . Ha töröljük az átvitelt, visszaszerezzük az átszállítás unk utolsó ellenőrzőpontját, és ezt az új ellenőrzőpontot használjuk az átigazolási környezetünkben.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Helyi könyvtár átvitele a Blob storage-ba

Kiábrándító lenne, ha az adatmozgalom-könyvtár egyszerre csak egy fájlt tudna átvinni. Szerencsére nem ez a helyzet. Az Adatmozgatás könyvtár lehetővé teszi a fájlok könyvtárának és az összes alkönyvtárának átvitelét. Adjunk hozzá néhány kódot, amely lehetővé teszi számunkra, hogy nem csak ezt.

Először adja `GetBlobDirectory` hozzá `Program.cs`a módszert a következőhöz:

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

Ezután `TransferLocalDirectoryToAzureBlobDirectory`módosítsa:

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

Van néhány különbség ez a módszer és az egyetlen fájl feltöltésének módja között. Most használjuk, `TransferManager.UploadDirectoryAsync` és `getDirectoryTransferContext` a módszer hoztunk létre korábban. Ezen kívül, most `options` egy értéket a mi feltöltési művelet, amely lehetővé teszi számunkra, hogy jelezze, hogy szeretnénk felvenni alkönyvtárak a mi feltölteni.

## <a name="copy-a-file-from-url-to-a-blob"></a>Fájl másolása URL-címből blobba

Most adjunk hozzá kódot, amely lehetővé teszi számunkra, hogy másolja a fájlt egy URL-t egy Azure Blob.

Módosítás `TransferUrlToAzureBlob`:

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

A szolgáltatás egyik fontos használati esete, amikor adatokat kell áthelyeznie egy másik felhőszolgáltatásból (pl. AWS) az Azure-ba. Mindaddig, amíg rendelkezik egy URL-címet, amely hozzáférést biztosít az erőforráshoz, `TransferManager.CopyAsync` könnyen áthelyezheti az erőforrást az Azure Blobs-ba a módszer használatával. Ez a módszer egy új logikai paramétert is bevezet. Ha ezt `true` a paramétert állítja be, az azt jelzi, hogy aszinkron kiszolgálóoldali másolatot szeretnénk készíteni. Ha ezt `false` a paramétert egy szinkron példányra állítja, azaz az erőforrást először letölti a helyi gépre, majd feltölti az Azure Blobba. Azonban szinkron másolat jelenleg csak az egyik Azure Storage-erőforrásból a másikba történő másoláshoz érhető el.

## <a name="copy-a-blob"></a>Blob másolása

Egy másik funkció, amely egyedülálló módon az adatmozgatási könyvtár az egyik Azure Storage-erőforrásból a másikba történő másolása.

Módosítás `TransferAzureBlobToAzureBlob`:

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

Ebben a példában `TransferManager.CopyAsync` a logikai `false` paramétert úgy állítjuk be, hogy azt jelzi, hogy szinkron másolatot szeretnénk készíteni. Ez azt jelenti, hogy az erőforrás először a helyi gépre töltődik le, majd feltölti az Azure Blobba. A szinkron másolási lehetőség nagyszerű módja annak, hogy a másolási művelet egyenletes sebességgel rendelkezzen. Ezzel szemben az aszinkron kiszolgálóoldali másolat sebessége a kiszolgálón rendelkezésre álló hálózati sávszélességtől függ, amely ingadozhat. A szinkron másolat azonban további kimenő forgalom költséget eredményezhet az aszinkron másolathoz képest. Az ajánlott megközelítés a szinkron példány használata egy Azure virtuális gép, amely ugyanabban a régióban, mint a forrás tárfiók a kimenő költségek elkerülése érdekében.

Az adatmozgatási alkalmazás befejeződött. [A teljes kódminta elérhető a GitHubon.](https://github.com/azure-samples/storage-dotnet-data-movement-library-app)

## <a name="next-steps"></a>További lépések

[Az Azure Storage Data Movement függvénytár referenciadokumentációja.](https://azure.github.io/azure-storage-net-data-movement)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]

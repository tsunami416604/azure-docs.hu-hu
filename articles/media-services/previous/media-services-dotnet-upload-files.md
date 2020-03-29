---
title: Fájlok feltöltése Media Services-fiókba a .NET | Microsoft dokumentumok
description: Ismerje meg, hogyan tölthet be médiatartalmakat a Media Services szolgáltatásba eszközök létrehozásával és feltöltésével.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 03b9995eab503ac1fcd4615882419dde31d4f8bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64869459"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Fájlok feltöltése Media Services-fiókba a .NET használatával 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

A Media Services szolgáltatásban a digitális fájlok feltöltése vagy kimenete egy adategységbe történik. Az **Eszköz** entitás tartalmazhat video-, hang-, kép-, miniatűr gyűjtemények, szöveges pályák és feliratfájlok (és a metaadatok ezekről a fájlokat.)  A fájlok feltöltése után a tartalom biztonságosan tárolódik a felhőben további feldolgozás és streamelés céljából.

Az adategységben található fájlokat **adategység-fájloknak** nevezzük. Az **AssetFile** példány és a tényleges médiafájl két különálló objektum. Az AssetFile példány metaadatokat tartalmaz a médiafájlról, míg a médiafájl a tényleges médiatartalmat tartalmazza.

## <a name="considerations"></a>Megfontolandó szempontok

A következő szempontokat kell figyelembe venni:
 
 * A Media Services a IAssetFile.Name tulajdonság értékét használja a streamelési tartalom URL-címeinek létrehozásakor (például http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Ezért a százalékos kódolás nem engedélyezett. A **Name** tulajdonság értéke nem tartalmazhatja a következő [százalékkódolást fenntartott karaktereket](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$/?%#[]". A fájlnévkiterjesztéséhez csak egy "" lehet.
* A név hossza nem lehet hosszabb 260 karakternél.
* A Media Services által feldolgozható maximális támogatott fájlméret korlátozott. A fájlméretre vonatkozó korlátozással kapcsolatban további információt [ebben](media-services-quotas-and-limitations.md) a cikkben talál.
* A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információt [ebben a cikkben](media-services-dotnet-manage-entities.md#limit-access-policies) talál.

Eszközök létrehozásakor a következő titkosítási beállításokat adhatja meg:

* **Nincs** – Nincs titkosítás. Ez az alapértelmezett érték. Ha ezt a beállítást használja, a tartalom nem védett az átvitel vagy a tárolóban lévő nyugalmi helyen.
  Ha progresszív letöltéssel szeretne MP4-et szállítani, használja ezt a lehetőséget: 
* **CommonEncryption** - Akkor használja ezt a beállítást, ha olyan tartalmat tölt fel, amelymár titkosítva van és közös titkosítással vagy PlayReady DRM-mel védett (például PlayReady DRM-mel védett sima streamelés).
* **EnvelopeEncrypted** – Akkor használja ezt a beállítást, ha az AES-sel titkosított HLS-t tölti fel. Megjegyzés: ehhez a fájlokat a Transform Manager használatával kell kódolni és titkosítani.
* **StorageEncrypted** – Titkosítja a tiszta tartalom helyileg AES-256 bites titkosítással, majd feltölti azt az Azure Storage, ahol tárolja titkosítva. A Storage-titkosítással védett adategységek titkosítása a kódolás előtt automatikusan fel lesz oldva, és egy titkosított fájlrendszerbe kerülnek; az új kimeneti adategységként való újbóli feltöltés előtt pedig lehetőség van az újbóli titkosításukra. A Storage-titkosítás elsősorban akkor hasznos, ha a kiváló minőségű bemeneti médiafájljait erős titkosítással szeretné védeni a lemezen való tároláskor.
  
    A Media Services lemezes tárolási titkosítást biztosít az eszközök számára, nem pedig vezetéken keresztüli titkosítást, például a Digitális jogkezelő (DRM).
  
    Ha az adategységen tárolótitkosítást alkalmaz, konfigurálnia kell az adategység továbbítási házirendjét. További információ: [Asset delivery policy configur.](media-services-dotnet-configure-asset-delivery-policy.md)

Ha azt adja meg, hogy az eszköz titkosítva legyen egy **CommonEncrypted** beállítással vagy egy **EnvelopeEncrypted** beállítással, az eszközt egy **ContentKey-hez**kell társítania. További információ: [Hogyan hozhatlétre contentkey.For](media-services-dotnet-create-contentkey.md)more information, to How to create a ContentKey . 

Ha megadja, hogy az eszköz **titkosítása StorageEncrypted** beállítással legyen titkosítva, a Media Services SDK for .NET egy **StorageEncrypted** **ContentKey-t** hoz létre az eszközhöz.

Ez a cikk bemutatja, hogyan használható a Media Services .NET SDK és a Media Services .NET SDK-bővítmények használata fájlok Media Services-eszközökbe való feltöltéséhez.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Egyetlen fájl feltöltése a Media Services .NET SDK szolgáltatással

A következő kód a .NET segítségével tölt fel egyetlen fájlt. Az AccessPolicy és a Locator a Feltöltés funkcióval jön létre és semmisül meg. 

```csharp
        static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, assetCreationOptions);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }
```


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Több fájl feltöltése a Media Services .NET SDK szolgáltatással
A következő kód bemutatja, hogyan hozhat létre egy eszközt, és töltsön fel több fájlt.

A kód a következőket teszi:

* Üres eszközt hoz létre az előző lépésben definiált CreateEmptyAsset metódus használatával.
* Létrehoz **AccessPolicy** egy AccessPolicy-példányt, amely meghatározza az eszközhöz való hozzáférés engedélyeit és időtartamát.
* Létrehoz egy **lokátor** példányt, amely hozzáférést biztosít az eszközhöz.
* Létrehoz egy **BlobTransferClient-példányt.** Ez a típus az Azure-blobokon működő ügyfelet jelöli. Ebben a példában az ügyfél figyeli a feltöltési folyamatot. 
* A megadott könyvtárban lévő fájlok számbavétele, és minden fájlhoz létrehoz egy **AssetFile** példányt.
* Feltölti a fájlokat a Media Services szolgáltatásba az **UploadAsync** metódussal. 

> [!NOTE]
> Az UploadAsync metódus segítségével győződjön meg arról, hogy a hívások nem blokkolódnak, és a fájlok feltöltése párhuzamosan történik.
> 
> 

```csharp
        static public IAsset CreateAssetAndUploadMultipleFiles(AssetCreationOptions assetCreationOptions, string folderPath)
        {
            var assetName = "UploadMultipleFiles_" + DateTime.UtcNow.ToString();

            IAsset asset = _context.Assets.Create(assetName, assetCreationOptions);

            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
                                                                AccessPermissions.Write | AccessPermissions.List);

            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

            var blobTransferClient = new BlobTransferClient();
            blobTransferClient.NumberOfConcurrentTransfers = 20;
            blobTransferClient.ParallelTransferThreadCount = 20;

            blobTransferClient.TransferProgressChanged += blobTransferClient_TransferProgressChanged;

            var filePaths = Directory.EnumerateFiles(folderPath);

            Console.WriteLine("There are {0} files in {1}", filePaths.Count(), folderPath);

            if (!filePaths.Any())
            {
                throw new FileNotFoundException(String.Format("No files in directory, check folderPath: {0}", folderPath));
            }

            var uploadTasks = new List<Task>();
            foreach (var filePath in filePaths)
            {
                var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
                Console.WriteLine("Created assetFile {0}", assetFile.Name);

                // It is recommended to validate AssetFiles before upload. 
                Console.WriteLine("Start uploading of {0}", assetFile.Name);
                uploadTasks.Add(assetFile.UploadAsync(filePath, blobTransferClient, locator, CancellationToken.None));
            }

            Task.WaitAll(uploadTasks.ToArray());
            Console.WriteLine("Done uploading the files");

            blobTransferClient.TransferProgressChanged -= blobTransferClient_TransferProgressChanged;

            locator.Delete();
            accessPolicy.Delete();

            return asset;
        }

    static void  blobTransferClient_TransferProgressChanged(object sender, BlobTransferProgressChangedEventArgs e)
    {
        if (e.ProgressPercentage > 4) // Avoid startup jitter, as the upload tasks are added.
        {
            Console.WriteLine("{0}% upload competed for {1}.", e.ProgressPercentage, e.LocalFile);
        }
    }
```


Nagy számú eszköz feltöltésekénvegye figyelembe a következőket:

* Hozzon létre egy új **CloudMediaContext** objektumot szálonként. A **CloudMediaContext** osztály nem szálbiztos.
* Növelje a NumberOfConcurrentTransfers értékét az alapértelmezett 2 értékről egy magasabb értékre, például 5-re. A tulajdonság beállítása a **CloudMediaContext**összes példányára hatással van. 
* A ParallelTransferThreadCount megtartása 10-es alapértelmezett értéken.

## <a name="ingesting-assets-in-bulk-using-media-services-net-sdk"></a><a id="ingest_in_bulk"></a>Eszközök tömeges betöltése a Media Services .NET SDK használatával
Nagy eszközfájlok feltöltése szűk keresztmetszetet okozhat az eszköz létrehozása során. Eszközök ömlesztve vagy "Tömeges betöltése", magában foglalja az eszköz létrehozásának leválasztása a feltöltési folyamatról. Tömeges betöltési megközelítés használatához hozzon létre egy jegyzékfájlt (IngestManifest), amely leírja az eszközt és a hozzá tartozó fájlokat. Ezután a választott feltöltési módszerrel töltse fel a társított fájlokat a jegyzékfájl blobtárolóba. A Microsoft Azure Media Services figyeli a jegyzékfájlhoz társított blob tároló. Miután egy fájlt feltöltött a blob tárolóba, a Microsoft Azure Media Services befejezi az eszköz létrehozása alapján a konfiguráció az eszköz a jegyzékben (IngestManifestAsset).

Új BealegkisebbManifest létrehozásához hívja meg a Create metódust, amelyet a CloudMediaContext Inasztika gyűjteménye elérhetővé tett. Ez a módszer létrehoz egy új BeestManifest a megadott jegyzékfájl nevét.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

Hozza létre a tömeges betöltési jegyzékhez társított eszközöket. Konfigurálja a kívánt titkosítási beállításokat az eszköz tömeges betöltésére.

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

Egy BeadtManifestAsset társít egy eszközt egy tömeges betöltési tömeges betöltése. Azt is társítja a AssetFiles, amely az egyes eszközök. IngestManifestAsset létrehozásához használja a Create metódust a kiszolgálókörnyezetben.

A következő példa bemutatja két új IngestManifestAssets hozzáadása, amely a korábban létrehozott két eszközt társítja a tömeges betöltési jegyzékhez. Minden egyes BeadtManifestAsset is társít egy fájlkészletet, amely feltölti az egyes eszközök tömeges betöltése során.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Bármilyen nagy sebességű ügyfélalkalmazást használhat, amely képes feltölteni az eszközfájlokat a blob storage tároló URI-ba, amelyet az **IngestManifest IIngestManifest.BlobStorageUriForUpload** tulajdonság a Bead. 

A következő kód bemutatja, hogyan lehet a .NET SDK használatával feltölteni az eszközfájlokat.

```csharp
    static void UploadBlobFile(string containerName, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);

            string[] splitfilename = filename.Split('\\');
            var blob = blobContainer.GetBlockBlobReference(splitfilename[splitfilename.Length - 1]);

            using (var stream = System.IO.File.OpenRead(filename))
                blob.UploadFromStream(stream);

            lock (consoleWriteLock)
            {
                Console.WriteLine("Upload for {0} completed.", filename);
            }
        });

        copytask.Start();
    }
```

A cikkben használt minta eszközfájljainak feltöltéséhez használt kód a következő kódpélda látható:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Az **IngestManifest-hez** társított összes eszköz tömeges betöltésének folyamatát az **IngestManifest**Statisztikai tulajdonságának lekérdezésével határozhatja meg. A folyamatadatok frissítéséhez minden alkalommal új **CloudMediaContext környezetet** kell használnia, amikor leteszi a Statistics tulajdonságot.

A következő példa bemutatja az IngestManifest lekérdezését az **azonosítója**segítségével.

```csharp
    static void MonitorBulkManifest(string manifestID)
    {
       bool bContinue = true;
       while (bContinue)
       {
          CloudMediaContext context = GetContext();
          IIngestManifest manifest = context.IngestManifests.Where(m => m.Id == manifestID).FirstOrDefault();

          if (manifest != null)
          {
             lock(consoleWriteLock)
             {
                Console.WriteLine("\nWaiting on all file uploads.");
                Console.WriteLine("PendingFilesCount  : {0}", manifest.Statistics.PendingFilesCount);
                Console.WriteLine("FinishedFilesCount : {0}", manifest.Statistics.FinishedFilesCount);
                Console.WriteLine("{0}% complete.\n", (float)manifest.Statistics.FinishedFilesCount / (float)(manifest.Statistics.FinishedFilesCount + manifest.Statistics.PendingFilesCount) * 100);

                if (manifest.Statistics.PendingFilesCount == 0)
                {
                   Console.WriteLine("Completed\n");
                   bContinue = false;
                }
             }

             if (manifest.Statistics.FinishedFilesCount < manifest.Statistics.PendingFilesCount)
                Thread.Sleep(60000);
          }
          else // Manifest is null
             bContinue = false;
       }
    }
```


## <a name="upload-files-using-net-sdk-extensions"></a>Fájlok feltöltése .NET SDK-bővítmények használatával
A következő példa bemutatja, hogyan tölthet fel egyetlen fájlt a .NET SDK-bővítmények használatával. Ebben az esetben a **CreateFromFile** metódust használja a rendszer, de az aszinkron verzió is elérhető (**CreateFromFileAsync**). A **CreateFromFile** metódus lehetővé teszi a fájlnév, a titkosítási beállítás és a visszahívás megadását a fájl feltöltési folyamatának jelentéséhez.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

A következő példa meghívja az UploadFile függvényt, és a tárolótitkosítást adja meg eszközlétrehozási lehetőségként.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>További lépések

Most már kódolhatja a feltöltött adategységeket. További információ: [Encode Assets](media-services-portal-encode.md) (Adategységek kódolása).

Emellett az Azure Functions használatával is elindíthatja a kódolási feladatokat a konfigurált tárolóba érkező fájlok alapján. További információkért tekintse meg [ezt a mintát](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Következő lépés
Most, hogy feltöltött egy eszközt a Media Services szolgáltatásba, folytassa a [Hogyan kaphat médiaprocesszort][How to Get a Media Processor] cikket.

[How to Get a Media Processor]: media-services-get-media-processor.md


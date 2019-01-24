---
title: Fájlok feltöltése Media Services-fiókba a .NET használatával |} A Microsoft Docs
description: Megtudhatja, hogyan médiatartalmak kerülnek a Media Services létrehozása és feltöltése az eszközök által.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2018
ms.author: juliako
ms.openlocfilehash: 43da365a6424fa9d489db1f5cd74ff6aeb8f0d7c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819883"
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Fájlok feltöltése Media Services-fiókba a .NET használatával
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portál](media-services-portal-upload-files.md)
> 
> 

A Media Services szolgáltatásban a digitális fájlok feltöltése vagy kimenete egy adategységbe történik. A **eszköz** entitás videókhoz, audiofájlokhoz, képeket, miniatűröket, szöveges nyomon követi és akadálymentes felirat fájlokat (és mindezen fájlok metaadatait.) tartalmazhat.  A fájlok feltöltése után a tartalom a felhőben lesz biztonságosan tárolva további feldolgozás és adatfolyam-továbbítás céljából.

Az adategységben található fájlokat **adategység-fájloknak** nevezzük. A **AssetFile** -példány és a tényleges médiafájl két különböző objektumot. A AssetFile-példány a médiafájl kapcsolatos metaadatokat tartalmaz, amíg az adathordozó-fájl tartalmazza a tényleges médiatartalmakat.

> [!NOTE]
> A következő szempontokat kell figyelembe venni:
> 
> * A Media Services a IAssetFile.Name tulajdonság értékét használja, URL-címek létrehozását, a streamelési tartalom (például http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Ebből kifolyólag százalék-kódolást nem engedélyezett. Értékét a **neve** tulajdonság nem lehet a következő [százalék-kódolás – fenntartott karakterek](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Emellett csak lehet egy "." a fájlnév kiterjesztésével.
> * A név hossza nem lehet nagyobb, mint 260 karakter hosszúságú lehet.
> * A Media Services által feldolgozható maximális támogatott fájlméret korlátozott. A fájlméretre vonatkozó korlátozással kapcsolatban további információt [ebben](media-services-quotas-and-limitations.md) a cikkben talál.
> * A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.
> 

Eszközök létrehozásakor a következő titkosítási beállításokat adhatja meg:

* **Nincs** – Nincs titkosítás. Ez az alapértelmezett érték. Ez a beállítás használatakor a tartalom nem védett átvitel, sem tárolás.
  Ha azt tervezi, hogy egy MP4-fájlt progresszív letöltés, használja ezt a beállítást: 
* **CommonEncryption** -használja ezt a beállítást, ha már titkosítva és általános titkosítás vagy a PlayReady DRM (például védett Smooth Streaming a PlayReady DRM technológiával) által védett tartalmat tölt fel.
* **EnvelopeEncrypted** – használja ezt a beállítást, ha AES által titkosított HLS tölt fel. Megjegyzés: ehhez a fájlokat a Transform Manager használatával kell kódolni és titkosítani.
* **StorageEncrypted** – a tiszta tartalom helyileg AES-256 bites titkosítást titkosítja, és ezután feltölti az Azure Storage helyén titkosítása. A Storage-titkosítással védett adategységek titkosítása a kódolás előtt automatikusan fel lesz oldva, és egy titkosított fájlrendszerbe kerülnek; az új kimeneti adategységként való újbóli feltöltés előtt pedig lehetőség van az újbóli titkosításukra. A Storage-titkosítás elsősorban akkor hasznos, ha a kiváló minőségű bemeneti médiafájljait erős titkosítással szeretné védeni a lemezen való tároláskor.
  
    Media Services biztosítja az eszközöket, nem több mint átvitel közbeni digitális Manager (DRM) például a lemezes tárolás titkosítása.
  
    Ha az objektum tárolása titkosítva, konfigurálnia kell a állít be objektumtovábbítási szabályzatot. További információkért lásd: [objektumtovábbítási szabályzat konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md).

Állít be az eszköz titkosítását a egy **CommonEncrypted** lehetőséget, vagy egy **EnvelopeEncrypted** lehetőség, hozzá kell rendelni az eszközintelligencia-egy **ContentKey**. További információkért lásd: [létrehozása egy ContentKey](media-services-dotnet-create-contentkey.md). 

Ha adja meg az eszköz titkosítását a egy **StorageEncrypted** beállítás, a Media Services SDK for .NET hoz létre egy **StorageEncrypted** **ContentKey** az objektum.

Ez a cikk bemutatja, hogyan használható a Media Services .NET SDK, valamint a Media Services .NET SDK-bővítmények a fájlok feltöltése a Media Services-eszköz.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>A Media Services .NET SDK-val egy fájl feltöltése
A következő kód .NET egyetlen fájl feltöltéséhez használja. A AccessPolicy és lokátor létrehozása és megsemmisíteni a feltöltési funkció. 

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


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>A Media Services .NET SDK-val több fájl feltöltése
A következő kód bemutatja, hogyan hozzon létre egy objektumot, és több fájl tölthető fel.

A kód a következőket teszi:

* Létrehoz egy üres az előző lépésben meghatározott CreateEmptyAsset mód használatával.
* Létrehoz egy **AccessPolicy** példányt, amely meghatározza az engedélyeket és a hozzáférés időtartama az eszközre.
* Létrehoz egy **kereső** példányt, amely hozzáférést biztosít az eszközre.
* Létrehoz egy **BlobTransferClient** példány. Ez a típus jelképezi ügyfél, amely az Azure-blobok működik. Ebben a példában az ügyfél a feltöltési folyamat figyeli. 
* A megadott könyvtárban található fájlok keresztül enumerálása, és létrehoz egy **AssetFile** példány minden egyes fájl.
* Feltölti a fájlokat, a Media Services használatával a **UploadAsync** metódust. 

> [!NOTE]
> Győződjön meg arról, hogy a hívások nem korlátozzák-e, és párhuzamosan a fájlok feltöltése a UploadAsync módszert használja.
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


Eszközök nagy mennyiségű feltöltését, vegye figyelembe a következőket:

* Hozzon létre egy új **CloudMediaContext** szálanként objektum. A **CloudMediaContext** osztály nem szálbiztos.
* 2. az alapértelmezett érték 5 például értéke NumberOfConcurrentTransfers növelése Ez a tulajdonság beállítása hatással van az összes példányát **CloudMediaContext**. 
* Az alapértelmezett érték 10 ParallelTransferThreadCount megőrizni.

## <a id="ingest_in_bulk"></a>Eszközök fürtjét egyszerre több Media Services .NET SDK használatával
Nagy méretű adategység-fájlok feltöltése lehet szűk keresztmetszetté objektum létrehozása során. Eszközök tömeges vagy a "Tömeges fürtjét" fürtjét, elválasztás objektum létrehozása a feltöltési folyamat magában foglalja. A tömeges megközelítés fürtjét használatához hozzon létre a jegyzékfájl (IngestManifest), amely az eszköz és az ahhoz tartozó fájlokat ismerteti. A választott a feltöltési módszer használatával töltse fel a társított fájlok a jegyzékfájl blob-tárolóba. A Microsoft Azure Media Services figyeli a jegyzékfájl társított blob-tárolóban. Egy fájlt a blobtárolóba való feltöltését követően a Microsoft Azure Media Services befejezte az eszköz a jegyzékfájlban (IngestManifestAsset) konfigurációja alapján eszköz létrehozását.

Hozzon létre egy új IngestManifest, hívja meg a Create metódus az a CloudMediaContext a IngestManifests gyűjtemény által elérhetővé tett. Ez a módszer létrehoz egy új IngestManifest az alkalmazásjegyzék-nevet.

```csharp
    IIngestManifest manifest = context.IngestManifests.Create(name);
```

A tömeges IngestManifest társított adategységek létrehozása. Az eszközök tömeges fürtjét a kívánt titkosítási beállításainak megadása

```csharp
    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);
```

Egy IngestManifestAsset tömeges IngestManifest tömeges fürtjét az adategység társítja. Hozzárendeli az, hogy minden eszköz AssetFiles is. Egy IngestManifestAsset létrehozásához használja a Create metódus a kiszolgáló környezetben.

A következő példa bemutatja, hogy hozzáadásával két új IngestManifestAssets, amely társításához a két eszközök tömeges a korábban létrehozott jegyzék betöltését. Minden egyes IngestManifestAsset hozzárendeli is az egyes eszközök feltöltött fájlokat során kötegelt feldolgozására.  

```csharp
    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });
```

Bármely nagy sebességű ügyfélalkalmazás képes az adategység-fájlok feltöltése a blob storage-tároló által biztosított URI-t is használhatja a **IIngestManifest.BlobStorageUriForUpload** a IngestManifest tulajdonságát. 

A következő kód bemutatja, hogyan lehet az eszközök fájlok feltöltése a .NET SDK használatával.

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

A kód a minta a cikk ezt használja az adategység-fájlok feltöltése az alábbi kód példa látható:

```csharp
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);
```

Megadhatja, hogy a kötegelt feldolgozására társított összes eszköz előrehaladását egy **IngestManifest** a statisztikák tulajdonságának lekérdezésével a **IngestManifest**. Végrehajtási adatok frissítéséhez kell használnia egy új **CloudMediaContext** minden alkalommal, amikor lekérdezi a statisztikák tulajdonság.

A következő példa bemutatja egy IngestManifest által lekérdezési annak **azonosító**.

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


## <a name="upload-files-using-net-sdk-extensions"></a>Fájlok feltöltése a .NET SDK-bővítmények használatával
Az alábbi példa bemutatja, hogyan töltse fel a .NET SDK-bővítmények használatával egyetlen fájlt. Ebben az esetben a **CreateFromFile** módszert használja, de a aszinkron verziója is elérhető (**CreateFromFileAsync**). A **CreateFromFile** módszer lehetővé teszi, hogy Ön adja meg a fájl nevét, a titkosítási beállítás és a egy visszahívást az annak érdekében, hogy a fájl feltöltési folyamatáról.

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

Az alábbi példa UploadFile függvényt hívja, és a tárolás titkosítása az eszköz beállítás határozza meg.  

```csharp
    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);
```

## <a name="next-steps"></a>További lépések

Most már kódolhatja a feltöltött adategységeket. További információ: [Encode Assets](media-services-portal-encode.md) (Adategységek kódolása).

Emellett az Azure Functions használatával is elindíthatja a kódolási feladatokat a konfigurált tárolóba érkező fájlok alapján. További információkért tekintse meg [ezt a mintát](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Következő lépés
Most, hogy a Media Services-feltöltött adategység, nyissa meg a [beszerzése egy Médiaprocesszorral] [ How to Get a Media Processor] cikk.

[How to Get a Media Processor]: media-services-get-media-processor.md


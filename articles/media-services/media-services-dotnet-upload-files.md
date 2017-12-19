---
title: "Fájlok feltöltése a Media Services-fiók használata a .NET |} Microsoft Docs"
description: "Útmutató a médiatartalom feltölti a Media Services létrehozásával és feltöltésével eszközök."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: c9c86380-9395-4db8-acea-507c52066f73
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2017
ms.author: juliako
ms.openlocfilehash: ec8c1da633374ba684f6a0a895c542ee76ef73b8
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="upload-files-into-a-media-services-account-using-net"></a>Fájlok feltöltése a Media Services-fiók .NET használatával
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portál](media-services-portal-upload-files.md)
> 
> 

A Media Services szolgáltatásban a digitális fájlok feltöltése vagy kimenete egy adategységbe történik. A **eszköz** entitás tartalmazhat videót, hang, képek, miniatűröket, szöveg nyomon követi és feliratfájlokat fájlokat (és a mindezen fájlok metaadatait.)  A fájlok feltöltése után a tartalom a felhőben lesz biztonságosan tárolva további feldolgozás és adatfolyam-továbbítás céljából.

Az adategységben található fájlokat **adategység-fájloknak** nevezzük. A **AssetFile** példány és a tényleges médiafájl két különböző objektum. A AssetFile példány media fájl metaadatainak tartalmaz, míg a médiafájl tartalmazza a tényleges médiatartalmakat.

> [!NOTE]
> A következők érvényesek:
> 
> * A Media Services a IAssetFile.Name tulajdonság értékét használja, amikor az adatfolyam-tartalmak (például http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) a URL-címek kiépítéséhez Emiatt százalék-kódolás nem engedélyezett. Értékét a **neve** tulajdonság nem lehet a következő [százalék kódolás-fenntartott karakterek](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Emellett csak lehet egy "." a fájlnévkiterjesztés.
> * A név hossza nem lehet hosszabb 260 karakternél.
> * A Media Services által feldolgozható maximális támogatott fájlméret korlátozott. A fájlméretre vonatkozó korlátozással kapcsolatban további információt [ebben](media-services-quotas-and-limitations.md) a témakörben talál.
> * A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információ [ebben](media-services-dotnet-manage-entities.md#limit-access-policies) a témakörben érhető el.
> 

Eszközök létrehozásakor a következő titkosítási beállításokat is megadhat. 

* **Nincs** – Nincs titkosítás. Ez az alapértelmezett érték. Vegye figyelembe, hogy ez a beállítás használatakor a tartalom nem védett átvitel, sem tárolás közben.
  Ha egy MP4-fájlt progresszív letöltés útján tervez továbbítani, használja ezt a lehetőséget. 
* **CommonEncryption** -használja ezt a beállítást, ha már titkosítva és általános titkosítás vagy a PlayReady DRM által (például védett Smooth Streaming egy PlayReady DRM) védett tartalmat.
* **EnvelopeEncrypted** – használja ezt a beállítást, ha AES által titkosított HLS. Megjegyzés: ehhez a fájlokat a Transform Manager használatával kell kódolni és titkosítani.
* **StorageEncrypted** - titkosítja a tiszta tartalom helyileg az AES-256 bites titkosítás használata, és feltölti azt Azure Storage helyén titkosítása. A Storage-titkosítással védett adategységek titkosítása a kódolás előtt automatikusan fel lesz oldva, és egy titkosított fájlrendszerbe kerülnek; az új kimeneti adategységként való újbóli feltöltés előtt pedig lehetőség van az újbóli titkosításukra. A tárolás titkosítása elsődleges használati eset az, amikor biztonságossá tételéhez a kiváló minőségű bemeneti médiafájljait erős titkosítással aktívan a lemezen.
  
    A Media Services biztosít az eszközök, nem over tömörített digitális Manager (DRM) például a lemezen tárolás titkosítása.
  
    Ha az adategységen tárolótitkosítást alkalmaz, konfigurálnia kell az adategység továbbítási házirendjét. További információ: [objektumtovábbítási szabályzat konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md).

Ha az eszköz titkosítva, amelyet megad egy **CommonEncrypted** lehetőséget, vagy egy **EnvelopeEncypted** beállítást, szüksége lesz az objektum hozzárendelése egy **ContentKey**. További információkért lásd: [létrehozása egy ContentKey](media-services-dotnet-create-contentkey.md). 

Ha az eszköz titkosítva, amelyet megad egy **StorageEncrypted** beállítás, a Media Services SDK, a .NET hoz létre egy **StorateEncrypted** **ContentKey** a a eszköz.

Ez a témakör bemutatja a Media Services .NET SDK-t, valamint a Media Services .NET SDK-bővítmények használata fájlok feltöltése a Media Services-objektumba.

## <a name="upload-a-single-file-with-media-services-net-sdk"></a>Media Services .NET SDK-val egy fájl feltöltése
Az alábbi példakód .NET SDK használatával egyetlen fájl feltöltése. A AccessPolicy és lokátor létrehozása és a feltöltés függvény megsemmisül. 


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


## <a name="upload-multiple-files-with-media-services-net-sdk"></a>Media Services .NET SDK-val több fájl feltöltése
A következő kód bemutatja, hogyan hozzon létre egy eszközt, és több fájl feltöltése.

A kód a következőket teszi:

* Létrehoz egy üres eszköz az előző lépésben meghatározott CreateEmptyAsset metódussal.
* Létrehoz egy **AccessPolicy** -példányt, engedélyekkel és hozzáférési időtartama határozza meg az eszközre.
* Létrehoz egy **lokátor** példánya, amely hozzáférést biztosít az eszközhöz.
* Létrehoz egy **BlobTransferClient** példány. Ez a típus jelképezi ügyfél, amely az Azure-blobokat működik. Ebben a példában használjuk az ügyfél feltöltési előrehaladásának figyeléséhez. 
* A megadott könyvtárban található fájlok keresztül enumerálása, és létrehoz egy **AssetFile** -példány minden fájlt.
* A Media Services segítségével feltölti a fájlokat a **UploadAsync** metódust. 

> [!NOTE]
> A UploadAsync módszer segítségével győződjön meg arról, hogy a hívások nem korlátozzák, és a fájlok feltöltése párhuzamosan.
> 
> 

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

                // It is recommended to validate AccestFiles before upload. 
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



Eszközök nagy számú feltöltését, vegye figyelembe a következőket.

* Hozzon létre egy új **CloudMediaContext** szálankénti objektum. A **CloudMediaContext** osztály nincs többszálú futtatásra.
* Az alapértelmezett érték 2 nagyobb értékre mint 5 NumberOfConcurrentTransfers növelése A tulajdonság beállítása hatással van az összes példányát **CloudMediaContext**. 
* Az alapértelmezett érték 10 ParallelTransferThreadCount megőrizni.

## <a id="ingest_in_bulk"></a>A Media Services .NET SDK használatával tömeges választásával dolgozhat fel eszközök
Nagy eszköz fájlok feltöltése lehet a szűk keresztmetszetek eszköz létrehozása során. Választásával dolgozhat fel eszközök tömeges vagy a "tömeges választásával dolgozhat" fel, magában foglalja a leválasztásával eszköz létrehozását a feltöltési folyamat során. A tömeges választásával dolgozhat fel módszer használatához hozzon létre a jegyzékfájl (IngestManifest), amely az eszköz és az ahhoz tartozó fájlokat ismerteti. Az Ön által választott a fájlfeltöltési módszer segítségével a társított fájlok feltöltése a jegyzékfájl blob tárolóhoz. A Microsoft Azure Media Services a blob tároló a jegyzékfájl társított figyeli. Miután egy fájlt tölt fel az a blob-tároló, a Microsoft Azure Media Services befejezte a jegyzékfájlban (IngestManifestAsset) az eszköz konfigurációján alapul eszköz létrehozását.

Hozzon létre egy új IngestManifest hívja meg a Create metódus által a CloudMediaContext az IngestManifests gyűjtemény. Ezzel a módszerrel hoz létre egy új IngestManifest a jegyzékfájl-nevet.

    IIngestManifest manifest = context.IngestManifests.Create(name);

Az eszközöket, amely a tömeges IngestManifest társítva lesz létrehozni. Az eszköz tömeges választásával dolgozhat fel a kívánt titkosítási beállításainak megadása

    // Create the assets that will be associated with this bulk ingest manifest
    IAsset destAsset1 = _context.Assets.Create(name + "_asset_1", AssetCreationOptions.None);
    IAsset destAsset2 = _context.Assets.Create(name + "_asset_2", AssetCreationOptions.None);

Egy IngestManifestAsset egy eszköz társít egy tömeges IngestManifest tömeges választásával dolgozhat fel. Azt is, amelyek minden eszköz a AssetFiles társítja. Hozzon létre egy IngestManifestAsset, használja a Create metódussal a kiszolgáló a környezetben.

A következő példa bemutatja, hozzáadását két új IngestManifestAssets, amelyek a két eszközök tömeges korábban létrehozott társítják betöltési jegyzékben. Minden IngestManifestAsset is hozzárendeli a minden eszköz lesz feltöltve fájlokat alatt tömeges választásával dolgozhat fel.  

    string filename1 = _singleInputMp4Path;
    string filename2 = _primaryFilePath;
    string filename3 = _singleInputFilePath;

    IIngestManifestAsset bulkAsset1 =  manifest.IngestManifestAssets.Create(destAsset1, new[] { filename1 });
    IIngestManifestAsset bulkAsset2 =  manifest.IngestManifestAssets.Create(destAsset2, new[] { filename2, filename3 });

A nagy sebességű ügyfélalkalmazás képes az eszköz fájlok feltöltése a blob storage tárolók által megadott URI-Azonosítót is használhatja a **IIngestManifest.BlobStorageUriForUpload** a IngestManifest tulajdonsága. Egy figyelmet a jelentősebb nagy sebességű feltöltési szolgáltatás [Aspera igény szerinti Azure alkalmazáshoz](https://datamarket.azure.com/application/2cdbc511-cb12-4715-9871-c7e7fbbb82a6). Ahogy az az alábbi Kódpélda az eszközök fájlok feltöltéséhez kódot is írhat.

    static void UploadBlobFile(string destBlobURI, string filename)
    {
        Task copytask = new Task(() =>
        {
            var storageaccount = new CloudStorageAccount(new StorageCredentials(_storageAccountName, _storageAccountKey), true);
            CloudBlobClient blobClient = storageaccount.CreateCloudBlobClient();
            CloudBlobContainer blobContainer = blobClient.GetContainerReference(destBlobURI);

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

A minta a jelen témakörben használt eszköz fájlok feltöltése a kódját a következő kódrészlet példa látható.

    UploadBlobFile(manifest.BlobStorageUriForUpload, filename1);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename2);
    UploadBlobFile(manifest.BlobStorageUriForUpload, filename3);


A tömeges választásával dolgozhat fel társított összes eszköz előrehaladását segítségével meghatározhatja egy **IngestManifest** statisztika tulajdonságának lekérdezésével a **IngestManifest**. Folyamatban lévő információk frissítéséhez kell használnia egy új **CloudMediaContext** minden alkalommal, amikor lekérdezi a statisztika tulajdonság.

A következő példa bemutatja, hogy egy IngestManifest által lekérdezési a **azonosító**.

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



## <a name="upload-files-using-net-sdk-extensions"></a>Töltse fel a fájlokat a .NET SDK-bővítmények
Az alábbi példa bemutatja, hogyan .NET SDK-bővítmények használatával egyetlen fájl feltöltéséhez. Ebben az esetben a **CreateFromFile** módszert használ, de az aszinkron verzióját is rendelkezésre áll (**CreateFromFileAsync**). A **CreateFromFile** módszer lehetővé teszi a adja meg a fájl, a titkosítási beállítással, és egy visszahívási ahhoz, hogy a fájl feltöltési folyamatáról.

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

Az alábbi példa UploadFile függvényt, és adja meg a tárolás titkosítása az eszköz létrehozása beállításként.  

    var asset = UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.StorageEncrypted);

## <a name="next-steps"></a>Következő lépések

Most már kódolhatja a feltöltött adategységeket. További információ: [Encode Assets](media-services-portal-encode.md) (Adategységek kódolása).

Emellett az Azure Functions használatával is elindíthatja a kódolási feladatokat a konfigurált tárolóba érkező fájlok alapján. További információkért tekintse meg [ezt a mintát](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Következő lépés
Most, hogy egy eszköz a Media Services feltöltött, navigáljon a [hogyan kérhet egy adathordozó processzor] [ How to Get a Media Processor] témakör.

[How to Get a Media Processor]: media-services-get-media-processor.md


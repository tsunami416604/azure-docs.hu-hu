---
title: Telepítse át az Azure Media Services-v2 v3 |} Microsoft Docs
description: Ez a cikk az Azure Media Services v3 bevezetett módosításokat írja le, és két verziója közötti különbségeket mutatja.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, stream, szórás, élő, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 06/12/2018
ms.author: juliako
ms.openlocfilehash: a382af644d30f9f0ebb586273c982ef1766f50b0
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295686"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Telepítse át a Media Services-v2 v3

> [!NOTE]
> Az Azure Media Services legújabb verziója előzetes verzió, és v3 néven is ismert.

Ez a cikk az Azure Media Services (AMS) v3 bevezetett módosításokat írja le, és két verziója közötti különbségeket mutatja.

## <a name="why-should-you-move-to-v3"></a>Miért kell helyez át v3?

### <a name="api-is-more-approachable"></a>API több könnyű

*  V3 alapul, amely az Azure Resource Manager beépített felügyeleti és a műveletek funkcióinak egyesített API felületen. Az Azure Resource Manager-sablonok létrehozása és telepítése átalakítások, Streaming végpontok, LiveEvents és további használható.
* Nyissa meg az API (más néven Swagger) specifikációját tartalmazó dokumentumban.
* SDK-k érhető el a .NET-hez, a .net Core, Node.js, Python, Java, Ruby.
* Azure CLI-integráció.

### <a name="new-features"></a>Új funkciók

* Most már támogatja a kódolást HTTPS feldolgozó (bemeneti Url-alapú).
* Átalakítások v3 jelennek meg. Átalakítás konfigurációk megosztani, Azure Resource Manager-sablonok létrehozása és különíteni egy adott ügyfélhez vagy a bérlő kódolási beállítások segítségével. 
* Egy eszköz több StreamingLocators lehet különböző dinamikus csomagolás és a dinamikus titkosítás beállításai.
* Tartalomvédelem több kulcsfontosságú szolgáltatásokat támogatja. 
* LiveEvent előzetes verziója támogatja a dinamikus csomagolás és a dinamikus titkosítás. Ez lehetővé teszi, hogy a védett tartalom megtekintése, valamint DASH vagy HLS csomagban.
* LiveOuput egyszerűbb, mint a régebbi Program entitás használja. 
* Az RBAC-támogatást a entitások hozzá lett adva.

## <a name="changes-from-v2"></a>V2-módosítások

* A Media Services v3 tárolás titkosítása (az AES-256 titkosítás) van csak a visszamenőleges kompatibilitás létrehozásakor támogatott az eszközök volt a Media Services v2. Tehát a meglévő tárhely v3 együttműködik eszközök titkosítottak, de nem teszi lehetővé a újakat.

    A v3 eszközök hozta létre, a Media Services támogatja a [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) kiszolgálóoldali tárolási titkosítás.
    
* Media Services SDK-k különválik a Storage szolgáltatás SDK a Storage szolgáltatás SDK több ellenőrzést ad, amely használja, és ezzel elkerülheti a versioning problémákat. 
* A v3 minden kódolási átviteli sebességet van bit / másodperc. Ez eltér a többi v2 Media Encoder Standard készleteket. Például a v2 sávszélességű 128 volna meg, de a v3 128000 lenne. 
* A v3 AssetFiles, AccessPolicies, IngestManifests nem léteznek.
* ContentKeys már nincs entitás, a StreamingLocator tulajdonsága.
* Esemény rács támogatási NotificationEndpoints váltja fel.
* Bizonyos entitások volt átnevezése

  * JobOutput feladat, most már csak egy részét a feladat váltja fel.
  * LiveEvent csatorna váltja fel.
  * LiveOutput Program váltja fel.
  * StreamingLocator lokátor váltja fel.

## <a name="code-changes"></a>Kódmódosítások

### <a name="create-an-asset-and-upload-a-file"></a>Hozzon létre egy eszközt, és a fájl feltöltése 

#### <a name="v2"></a>v2

```csharp
IAsset asset = context.Assets.Create(assetName, storageAccountName, options);

IAssetFile assetFile = asset.AssetFiles.Create(assetFileName);

assetFile.Upload(filePath);
```

#### <a name="v3"></a>v3

```csharp
Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, permissions: AssetContainerPermission.ReadWrite, expiryTime: DateTime.Now.AddHours(1));

var sasUri = new Uri(response.AssetContainerSasUrls.First());
CloudBlobContainer container = new CloudBlobContainer(sasUri);

var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
blob.UploadFromFile(fileToUpload);
```

### <a name="submit-a-job"></a>Feladat elküldése

#### <a name="v2"></a>v2

```csharp
IMediaProcessor processor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

IJob job = jobs.Create($"Job for {inputAsset.Name}");

ITask task = job.Tasks.AddNew($"Task for {inputAsset.Name}", processor, taskConfiguration);

task.InputAssets.Add(inputAsset);

task.OutputAssets.AddNew(outputAssetName, outputAssetStorageAccountName, outputAssetOptions);

job.Submit();
```

#### <a name="v3"></a>v3

```csharp
client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, new Asset());

JobOutput[] jobOutputs = { new JobOutputAsset(outputAssetName)};

JobInput jobInput = JobInputAsset(assetName: assetName);

Job job = client.Jobs.Create(resourceGroupName,
accountName, transformName, jobName,
new Job {Input = jobInput, Outputs = jobOutputs});
```

### <a name="publish-an-asset-with-aes-encryption"></a>Tegye közzé az adategységet AES titkosítással 

#### <a name="v2"></a>v2

1. ContentKeyAuthorizationPolicyOption létrehozása
2. ContentKeyAuthorizationPolicy létrehozása
3. AssetDeliveryPolicy létrehozása
4. Eszköz létrehozása és a feladat vagy elküldése tartalom feltöltése és a kimeneti eszköz használata
5. AssetDeliveryPolicy társítani Asset
6. ContentKey létrehozása
7. Eszköz ContentKey csatolása
8. AccessPolicy létrehozása
9. Azonosító létrehozása

#### <a name="v3"></a>v3

1. Tartalom kulcs házirend létrehozása
2. Eszköz létrehozása
3. Tartalom feltöltése vagy JobOutput eszköz használata
4. StreamingLocator létrehozása

## <a name="next-steps"></a>További lépések

A [fájlok streamelésével](stream-files-dotnet-quickstart.md) foglalkozó témakörben tekintheti meg, hogy milyen egyszerűen kezdheti meg a videofájlok kódolását és streamelését. 


---
title: Telepítse át az Azure Media Services v3 a v2 |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Media Services v3 bevezetett módosítások és láthatók a két verziója közötti különbségek.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, szórás, élő, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: a17bad5beb651aaa085c626296c200a00c30647e
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376362"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Telepítse át a Media Services v2 v3

Ez a cikk ismerteti az Azure Media Services (AMS) v3 bevezetett módosítások és láthatók a két verziója közötti különbségek.

## <a name="why-should-you-move-to-v3"></a>Miért kell helyez át v3?

### <a name="api-is-more-approachable"></a>API több programozása

*  V3 alapján egy egységes API-felület, amely az Azure Resource Managerrel létrehozott felügyeleti és a műveletek funkciókkal rendelkezik. Az Azure Resource Manager-sablonok létrehozása és üzembe helyezése átalakítások, adatfolyam-továbbítási végpontok, LiveEvents és egyéb használható.
* Nyissa meg az API-t (más néven Swagger-) specifikáció szerinti dokumentum.
* SDK-k legyen az .net, .net Core, Node.js, Python, Java, Ruby.
* Azure CLI-integráció.

### <a name="new-features"></a>Új funkciók

* Kódolás mostantól támogatja a HTTPS (a bemeneti Url-alapú) képes feldolgozni.
* Átalakítások nem ismeri a v3-as. Egy-egy átalakítási konfigurációk megosztani, az Azure Resource Manager-sablonok létrehozása és különíteni egy adott ügyfél vagy a bérlő kódolási beállítások segítségével. 
* Egy eszköz rendelkezhet több StreamingLocators különböző dinamikus becsomagolást és a dinamikus titkosítás beállításokkal.
* A Content protection támogatja a többszörös legfontosabb funkcióit. 
* Videókhoz előzetes verziója támogatja a dinamikus becsomagolást és dinamikus titkosítást. Ez lehetővé teszi a content protection előzetes verzió, valamint a DASH vagy HLS csomagolásra.
* LiveOuput egyszerűbb, mint a régebbi Program entitás használni. 
* Az RBAC-támogatást az entitások hozzá lett adva.

## <a name="changes-from-v2"></a>V2-módosítások

* A Media Services v3, tárolás titkosítása (AES-256 titkosítás) van csak támogatott a visszamenőleges kompatibilitás a Media Services v2 létrehozásakor a rendszer az eszközöket. Tehát v3 együttműködik a meglévő tárhely eszközök titkosított, de nem teszi meg újakat.

    Az eszközök létrehozni a v3-as, a Media Services támogatja a [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) kiszolgálóoldali tárolási titkosítást.
    
* A Media Services SDK-k különválik a Storage SDK, így jobban szabályozhatja a Storage SDK-t használja, és elkerülhető az versioning problémák. 
* A v3-as a kódolási átviteli sebességek összes bit / másodperc. Ez eltér a többi v2 Media Encoder Standard készletek. Ha például az átviteli sebesség a v2-ben ezt kell megadni, mint 128, de a v3-as 128000 lenne. 
* A v3-as AssetFiles, AccessPolicies, IngestManifests nem léteznek.
* Tartalomkulcsok nem lesznek egy entitás, a StreamingLocator tulajdonságát.
* Event Grid támogatási NotificationEndpoints váltja fel.
* Bizonyos entitások lettek átnevezve

  * JobOutput váltja fel a feladat, most már csak egy részét a feladatot.
  * Videókhoz csatorna váltja fel.
  * LiveOutput Program váltja fel.
  * StreamingLocator kereső váltja fel.

## <a name="code-changes"></a>Kódmódosítások

### <a name="create-an-asset-and-upload-a-file"></a>Hozzon létre egy objektumot, és a egy fájl feltöltése 

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

### <a name="publish-an-asset-with-aes-encryption"></a>Teszi közzé az objektumot az AES-titkosítás 

#### <a name="v2"></a>v2

1. ContentKeyAuthorizationPolicyOption létrehozása
2. ContentKeyAuthorizationPolicy létrehozása
3. AssetDeliveryPolicy létrehozása
4. Eszköz létrehozása és töltse fel a feladat vagy terjeszt tartalmat, és használja a kimeneti adategység
5. Az Eszközintelligencia AssetDeliveryPolicy társítása
6. ContentKey létrehozása
7. Az Eszközintelligencia ContentKey csatolása
8. AccessPolicy létrehozása
9. Kereső létrehozása

#### <a name="v3"></a>v3

1. Tartalmi kulcs szabályzat létrehozása
2. Eszköz létrehozása
3. Tartalom feltöltése, vagy JobOutput eszköz használata
4. StreamingLocator létrehozása

## <a name="next-steps"></a>További lépések

A [fájlok streamelésével](stream-files-dotnet-quickstart.md) foglalkozó témakörben tekintheti meg, hogy milyen egyszerűen kezdheti meg a videofájlok kódolását és streamelését. 


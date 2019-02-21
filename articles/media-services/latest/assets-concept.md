---
title: Eszközök a Media Services – Azure |} A Microsoft Docs
description: Ez a cikk biztosítja az eszközök vannak, és hogyan használják az Azure Media Services ismertetése.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/19/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2ec2ddbac5d0368aaf1b46208c9ebb44bf12a622
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447310"
---
# <a name="assets"></a>Objektumok

Az Azure Media Services- [eszköz](https://docs.microsoft.com/rest/api/media/assets) mindezen fájlok metaadatait és a digitális fájlok (beleértve a videót, hangot, képeket, miniatűröket, szövegsávok és feliratfájlok) tartalmaz. Miután a digitális fájlok feltöltése egy adategységbe, azok a Media Services encoding, streaming, tartalom munkafolyamatok elemzése használható. További információkért lásd: a [digitális fájlok feltöltése eszközök](#upload-digital-files-into-assets) szakaszt.

Egy eszköz található blob-tárolóra lesz leképezve a [Azure Storage-fiók](storage-account-concept.md) és az adategységben található fájlokat a tárolóban blokkblobként vannak tárolva. A Media Services támogatja a Blob-rétegek, ha a fiók használ az általános célú v2 (GPv2) storage. A GPv2, helyezze át a fájlok [ritka elérésű vagy archív tárolási](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Archív** storage alacsony költségű forrásfájlokat, ha már nincs szükség (például, miután azok kódolású) ideális választás.

A **archív** tárolási réteg csak ajánlott nagyon nagy méretű forrásfájlokat, amely már kódolva, és a kódolási feladat kimenetének okot egy kimeneti blob-tárolóban. A blobok a kimeneti tárolóhoz, hogy egy eszköz és -felhasználási streamelésére, vagy a tartalomelemzés társítani kívánt léteznie kell egy **interaktív** vagy **ritkán használt adatok** tárolási szinten.

> [!NOTE]
> A dátum/idő típus objektum tulajdonságait mindig UTC formátumban vannak.

## <a name="upload-digital-files-into-assets"></a>Eszközök digitális fájlok feltöltése

A gyakori munkafolyamatokat a Media Services egyik feltöltése, kódolása és streamelése egy fájlt. Ez a szakasz az általános lépéseket ismerteti.

1. A Media Services v3 API használatával hozzon létre egy új "bemeneti" objektumot. Ez a művelet létrehoz egy tárolót a Media Services-fiókjához társított storage-fiókban. Az API-t adja vissza a tároló nevét (például `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Ha már rendelkezik egy adategység társítani kívánt blob-tárolóba, megadhatja a tároló neve, az eszköz létrehozásakor. Media Services jelenleg csak támogatja a blobok a tároló legfelső szintű és nem a fájlnévben szereplő elérési utakat. Így egy tároló, a "input.mp4" fájl nevével fog működni. Azonban az "videos/inputs/input.mp4" nevű tároló nem fog működni.

    Az Azure CLI segítségével feltöltés közvetlenül a tárfiók és tároló, amely megfelelő jogosultságokkal rendelkezik az előfizetésében. <br/>A tároló neve egyedi legyen, és hajtsa végre a tároló elnevezési irányelveinek. A név nem kell hajtsa végre a formázás Media Services eszköz Tárolónév (objektum-GUID). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Egy SAS URL-cím olvasási és írási engedélyekkel, hogy a rendszer a digitális fájlok feltöltése az Eszközintelligencia-tároló lekéréséhez. A Media Services API-t is használhatja [eszköz tároló URL-címek listája](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Az Azure Storage API-k és SDK-kkal (például a [Storage REST API-val](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), vagy [.NET SDK-val](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) fájlok feltöltése az Eszközintelligencia-tárolóba. 
4. A Media Services v3 API-k használatával hozzon létre egy-egy átalakítási és a egy feladatot a "bemeneti" Eszközintelligencia feldolgozásához. További információkért lásd: [átalakítások és feladatok](transform-concept.md).
5. A Stream a tartalom a "kimeneti" objektum.

A teljes .NET-példa bemutatja, hogyan: hozható létre az eszköz, írható SAS URL-cím lekérése a storage-tárolóba az eszköz, a fájl feltöltése a tárolóba, a storage, az SAS URL-cím használatával, lásd: [hozzon létre egy feladat bemenete egy helyi fájlból](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Új eszköz létrehozása

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

A REST-példa: a [hozzon létre egy objektumot a REST segítségével](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) példa.

A példa bemutatja, hogyan hozhat létre a **kérelem törzse** , ahol megadható hasznos információkat, például leírása, a tároló neve, a storage-fiók és az egyéb információkat.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Egy teljes példa: [hozzon létre egy feladat bemenete egy helyi fájlból](job-input-from-local-file-how-to.md). A Media Services v3-as, a feladat bemenetének is létrehozhatók a HTTPS URL (lásd: [hozzon létre egy feladat bemenete HTTPS URL-címet](job-input-from-http-how-to.md)).

## <a name="filtering-ordering-paging"></a>Szűrési, rendezési, stránkování

Lásd: [szűrése, rendezése, a Media Services entitások lapozás](entities-overview.md).

## <a name="storage-side-encryption"></a>Storage ügyféloldali titkosítása

Az inaktív eszközök védelmére, titkosítani kell az eszközök által a storage ügyféloldali titkosítása. Az alábbi táblázat a storage ügyféloldali titkosítása működését mutatja a Media Services:

|Titkosítási beállítás|Leírás|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services, tárolás titkosítása|AES-256 titkosítással, kulcsfontosságú a Media Services által felügyelt|Támogatott<sup>(1)</sup>|Nem támogatott<sup>(2)</sup>|
|[A Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|A kiszolgálóoldali titkosítást az Azure Storage által kínált kulcs felügyelt ügyfél vagy Azure által|Támogatott|Támogatott|
|[Storage ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Az Azure storage, a Key Vault az ügyfél által felügyelt kulcs által kínált ügyféloldali titkosítás|Nem támogatott|Nem támogatott|

<sup>1</sup> közben a Media Services támogatja a tartalom kezelésére, a titkosítatlan/bármilyen titkosítás nélkül, ez nem ajánlott.

<sup>2</sup> a Media Services v3, tárolás titkosítása (AES-256 titkosítás) van csak támogatott a visszamenőleges kompatibilitás a Media Services v2 létrehozásakor a rendszer az eszközöket. Tehát v3 együttműködik a meglévő tárhely eszközök titkosított, de nem teszi meg újakat.

## <a name="next-steps"></a>További lépések

* [Fájl streamelése](stream-files-dotnet-quickstart.md)
* [Használatával a felhőalapú DVR-Funkciókkal](live-event-cloud-dvr.md)
* [Különbségeit a Media Services v2 és v3](migrate-from-v2-to-v3.md)

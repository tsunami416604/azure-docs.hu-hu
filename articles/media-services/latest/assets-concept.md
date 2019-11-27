---
title: Objektumok
titleSuffix: Azure Media Services
description: Ismerje meg, hogy milyen eszközöket és hogyan használják a Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ab4eebf56abd2d328ccf86929a043d4354ca157c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186316"
---
# <a name="assets-in-azure-media-services"></a>Eszközök a Azure Media Services

Azure Media Services egy [eszköz](https://docs.microsoft.com/rest/api/media/assets) az Azure Storage-ban tárolt digitális fájlokról tartalmaz információkat (beleértve a videó, a hang, a képek, a miniatűr gyűjtemények, a szöveges számok és a lezárt feliratok fájljait).

Az eszköz az [Azure Storage-fiókban](storage-account-concept.md) található blob-tárolóra van leképezve, és az adategységben található fájlok a tárolóban blokk blobként tárolódnak. Media Services támogatja a blob-rétegeket, ha a fiók általános célú v2 (GPv2) tárolót használ. A GPv2 használatával fájlokat helyezhet át a ritka elérésű [vagy archív tárolóba](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Az **archiválási** tárterület alkalmas a forrásfájlok archiválására, ha már nincs rá szükség (például a kódolásuk után).

Az **archív** tárolási szint csak olyan nagy méretű forrásfájlok esetében ajánlott, amelyek már kódoltak, és a kódolási feladatok kimenete kimeneti blob-tárolóba került. A kimeneti tárolóban lévő blobokat, amelyeket egy adategységhez szeretne rendelni, és a tartalom adatfolyamként való továbbítására vagy elemzésére használja, **gyakran vagy ritkán** **használt tárolási** rétegben kell lennie.

### <a name="naming-blobs"></a>Blobok elnevezése

Az eszközön belüli fájlok/Blobok nevének a [blob neve](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) és az [NTFS-név követelményeit](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)is követnie kell. Ennek a követelménynek az oka, hogy a fájlok a blob Storage-ból egy helyi NTFS-lemezre másolhatók feldolgozásra.

## <a name="upload-digital-files-into-assets"></a>Digitális fájlok feltöltése eszközökre

Miután a digitális fájlokat feltöltötte a Storage-ba, és egy objektumhoz társítva van, a Media Services kódolás, a folyamatos átvitel és a tartalom elemzése munkafolyamatokban is használhatók. Az egyik gyakori Media Services munkafolyamat egy fájl feltöltése, kódolása és továbbítása. Ez a szakasz az általános lépéseket ismerteti.

> [!TIP]
> A fejlesztés megkezdése előtt tekintse át [Media Services V3 API](media-services-apis-overview.md) -kkal való fejlesztést (az API-k elérésére, az elnevezési konvenciók és így tovább) vonatkozó információkat.

1. Hozzon létre egy új „bemeneti” adategységet a Media Services v3 API használatával. Ez a művelet létrehoz egy tárolót a Media Services-fiókjához társított tárfiókban. Az API a tároló nevét adja vissza (például `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Ha már rendelkezik egy eszközhöz társítandó blob-tárolóval, megadhatja a tároló nevét az eszköz létrehozásakor. A Media Services jelenleg csak a tároló gyökerében található blobokat támogatja, a fájlnévben elérési utat tartalmazó blobokat nem. Ennélfogva egy „input.mp4” nevű fájlt tartalmazó tároló használható lesz. Azonban a "videos/Inputs/input. mp4" fájlnévvel rendelkező tároló nem fog működni.

    Az Azure CLI-vel közvetlenül feltölthet bármilyen tárfiókba és tárolóba, amelyhez jogosultsággal rendelkezik az előfizetésében.

    A tárolók nevének egyedinek kell lennie, és követnie kell a tárolók elnevezésére vonatkozó irányelveket. A névnek nem kell követnie a Media Services adategység-tárolójának elnevezési formátumát (Adategység-GUID).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Használjon egy olvasási és írási engedélyekkel rendelkező SAS URL-címet a digitális fájlok az adategység-tárolóba történő feltöltéséhez. A Media Services API segítségével [kilistázhatja az adategység-tárolók URL-címét](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Az Azure Storage API-jait vagy SDK-kat (például a [Storage REST API](../../storage/common/storage-rest-api-auth.md) vagy a [.net SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)-t) használva tölthet fel fájlokat az Asset tárolóba.
4. A Media Services v3 API-k segítségével hozzon létre egy Átalakítást és egy Feladatot a „bemeneti” adategység feldolgozásához. További információt az [átalakításokkal és feladatokkal](transform-concept.md) kapcsolatos cikkben olvashat.
5. Továbbítsa a tartalmat a "output" objektumból.

Egy teljes .NET-példa, amely bemutatja, hogyan hozhatja létre az adategységet, kérhet egy írható SAS URL-címet az objektum tárolójában a tárolóban, majd feltöltheti a fájlt a Storage tárolóba a SAS URL-cím használatával. a [feladatok helyi fájlból való létrehozása](job-input-from-local-file-how-to.md)című témakörben talál további információt.

### <a name="create-a-new-asset"></a>Új eszköz létrehozása

> [!NOTE]
> A DateTime típusú tulajdonságok mindig UTC formátumban jelennek meg.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

A REST-példákat lásd: [eszköz létrehozása Rest](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples) -példával.

A példa bemutatja, hogyan hozhatja létre a **kérelem törzsét** , ahol megadhatja a leírást, a tároló nevét, a Storage-fiókot és más hasznos információkat.

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

Egy teljes példát a következő témakörben talál: [Job input létrehozása helyi fájlból](job-input-from-local-file-how-to.md). Media Services V3 esetében a feladatok bemenete HTTPS URL-címekről is létrehozható (lásd: [feladatok bevitele HTTPS URL-címről](job-input-from-http-how-to.md)).

## <a name="map-v3-asset-properties-to-v2"></a>V3-eszköz tulajdonságainak hozzárendelése v2-re

A következő táblázat azt mutatja be, hogy az adategység tulajdonságai hogyan jelennek meg az objektumnak [a v2](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)-ben lévő tulajdonságok tulajdonságában.

|v3-tulajdonságok|v2 tulajdonságai|
|---|---|
|`id` – (egyedi) a teljes Azure Resource Manager elérési út, lásd: példák az [eszközön](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name` – (egyedi) – lásd: [elnevezési konvenciók](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|a `Id`-(egyedi) érték a `nb:cid:UUID:` előtaggal kezdődik.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (létrehozási beállítások)|
|`type`||

## <a name="storage-side-encryption"></a>Storage ügyféloldali titkosítása

Az inaktív eszközök védelmére, titkosítani kell az eszközök által a storage ügyféloldali titkosítása. Az alábbi táblázat a storage ügyféloldali titkosítása működését mutatja a Media Services:

|Titkosítási beállítás|Leírás|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services, tárolás titkosítása|AES-256 titkosítás, Media Services által felügyelt kulcs.|Támogatott<sup>(1)</sup>|Nem támogatott<sup>(2)</sup>|
|[Inaktív adatok Storage Service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Az Azure Storage által kínált kiszolgálóoldali titkosítás, amelyet az Azure vagy az ügyfél kezel.|Támogatott|Támogatott|
|[Storage ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Az Azure Storage által kínált ügyféloldali titkosítás, amelyet az ügyfél felügyel Key Vaultban.|Nem támogatott|Nem támogatott|

<sup>1</sup> míg a Media Services támogatja a tartalom tiszta/titkosítás nélküli kezelését, ezért nem ajánlott.

<sup>2</sup> Media Services v3-as verzióban a Storage encryption (AES-256 encryption) csak akkor támogatott a visszamenőleges kompatibilitás érdekében, ha az eszközök Media Services v2-mel lettek létrehozva. A v3 azt jelenti, hogy a meglévő tárolók titkosított eszközeivel működik együtt, de nem engedélyezi újak létrehozását.

## <a name="filtering-ordering-paging"></a>Szűrési, rendezési, stránkování

Lásd: [Media Services entitások szűrése, rendezése és lapozása](entities-overview.md).

## <a name="next-steps"></a>További lépések

* [Fájl streamelése](stream-files-dotnet-quickstart.md)
* [Egy felhőalapú DVR használata](live-event-cloud-dvr.md)
* [Media Services v2 és v3 közötti különbségek](migrate-from-v2-to-v3.md)

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
ms.openlocfilehash: 68bb1fea88ab7ba30e0ba07839f2d962840b7818
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921181"
---
# <a name="assets-in-azure-media-services"></a>Eszközök a Azure Media Services

A Azure Media Servicesban az [](https://docs.microsoft.com/rest/api/media/assets) adategység alapvető fogalom. Itt adhatja meg az adathordozót (például feltöltéssel vagy élő betöltéssel), kimeneti adathordozóval (a feladatok kimenetéről), és közzéteheti az adathordozót (adatfolyamként). 

Az eszköz az [Azure Storage-fiókban](storage-account-concept.md) található blob-tárolóra van leképezve, és az adategységben található fájlok a tárolóban blokk blobként tárolódnak. Az eszközök tartalmazzák az Azure Storage-ban tárolt digitális fájlokra vonatkozó információkat (beleértve a videó, a hang, a képek, a miniatűr gyűjtemények, a szöveges számok és a lezárt feliratok fájljait).

Media Services támogatja a blob-rétegeket, ha a fiók általános célú v2 (GPv2) tárolót használ. A GPv2 használatával fájlokat helyezhet át a ritka elérésű [vagy archív tárolóba](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Az **archiválási** tárterület alkalmas a forrásfájlok archiválására, ha már nincs rá szükség (például a kódolásuk után).

Az **archív** tárolási szint csak olyan nagy méretű forrásfájlok esetében ajánlott, amelyek már kódoltak, és a kódolási feladatok kimenete kimeneti blob-tárolóba került. A kimeneti tárolóban lévő blobokat, amelyeket egy adategységhez szeretne rendelni, és a tartalom adatfolyamként való továbbítására vagy elemzésére használja, **gyakran vagy ritkán** **használt tárolási** rétegben kell lennie.

## <a name="naming"></a>Elnevezés 

### <a name="assets"></a>Objektumok

Az eszköz nevének egyedinek kell lennie. A (z) Media Services v3-erőforrások neve (például eszközök, feladatok, átalakítások) Azure Resource Manager elnevezési megkötések hatálya alá tartozik. További információ: [elnevezési konvenciók](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobok

Az eszközön belüli fájlok/Blobok nevének a [blob neve](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) és az [NTFS-név követelményeit](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)is követnie kell. Ennek a követelménynek az oka, hogy a fájlok a blob Storage-ból egy helyi NTFS-lemezre másolhatók feldolgozásra.

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

## <a name="next-steps"></a>Következő lépések

[Eszközök kezelése a Media Servicesban](manage-asset-concept.md)

## <a name="also-see"></a>Lásd még:

[Media Services v2 és v3 közötti különbségek](migrate-from-v2-to-v3.md)

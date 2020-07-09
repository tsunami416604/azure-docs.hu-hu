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
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9b04941a5799955097fbd54ad9bdf50eccb87541
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79087910"
---
# <a name="assets-in-azure-media-services-v3"></a>Eszközök a Azure Media Services v3-ban

A Azure Media Servicesban az [Asset](https://docs.microsoft.com/rest/api/media/assets) adategység alapvető fogalom. Itt adhatja meg az adathordozót (például feltöltéssel vagy élő betöltéssel), kimeneti adathordozóval (a feladatok kimenetéről), és közzéteheti az adathordozót (adatfolyamként). 

Az eszköz az [Azure Storage-fiókban](storage-account-concept.md) található blob-tárolóra van leképezve, és az adategységben található fájlok a tárolóban blokk blobként tárolódnak. Az eszközök tartalmazzák az Azure Storage-ban tárolt digitális fájlokra vonatkozó információkat (beleértve a videó, a hang, a képek, a miniatűr gyűjtemények, a szöveges számok és a lezárt feliratok fájljait).

Media Services támogatja a blob-rétegeket, ha a fiók általános célú v2 (GPv2) tárolót használ. A GPv2 használatával fájlokat helyezhet át a ritka elérésű [vagy archív tárolóba](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). Az **archiválási** tárterület alkalmas a forrásfájlok archiválására, ha már nincs rá szükség (például a kódolásuk után).

Az **archív** tárolási szint csak olyan nagy méretű forrásfájlok esetében ajánlott, amelyek már kódoltak, és a kódolási feladatok kimenete kimeneti blob-tárolóba került. A kimeneti tárolóban lévő blobokat, amelyeket egy adategységhez szeretne rendelni, és a tartalom adatfolyamként való továbbítására vagy elemzésére használja, **gyakran vagy ritkán** **használt tárolási** rétegben kell lennie.

## <a name="naming"></a>Elnevezés 

### <a name="assets"></a>Objektumok

Az eszköz nevének egyedinek kell lennie. A (z) Media Services v3-erőforrások neve (például eszközök, feladatok, átalakítások) Azure Resource Manager elnevezési megkötések hatálya alá tartozik. További információ: [elnevezési konvenciók](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobok

Az eszközön belüli fájlok/Blobok nevének a [blob neve](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) és az [NTFS-név követelményeit](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)is követnie kell. Ennek a követelménynek az oka, hogy a fájlok a blob Storage-ból egy helyi NTFS-lemezre másolhatók feldolgozásra.

## <a name="next-steps"></a>További lépések

[Eszközök kezelése a Media Servicesban](manage-asset-concept.md)

## <a name="see-also"></a>Lásd még

[Media Services v2 és v3 közötti különbségek](migrate-from-v2-to-v3.md)

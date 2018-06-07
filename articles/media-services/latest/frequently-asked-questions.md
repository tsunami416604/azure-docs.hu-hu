---
title: Az Azure Media Services v3 kapcsolatos gyakori kérdések |} Microsoft Docs
description: Ez a cikk gyakran ismételt kérdések Azure Media Services v3 választ ad.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/29/2018
ms.author: juliako
ms.openlocfilehash: 7fe59763162ca53ea0256d5902aeece90525f214
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655197"
---
# <a name="azure-media-services-v3-preview-frequently-asked-questions"></a>Az Azure Media Services v3 (előzetes verzió) kapcsolatos gyakori kérdések

Ez a cikk gyakran ismételt kérdések Azure Media Services (AMS) v3 választ ad.

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Az Azure-portál használatával v3-erőforrások kezelése?

még nem. A támogatott SDK-k egyikét használhatja. Lásd: oktatóanyagok és minták a doc-készletben.

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>Van-e az API-k Media fenntartott egységek konfigurálásához?

A Media Services team van kiküszöbölése RUs v3. Azonban a szükséges szolgáltatás feladata, nem teljes. Addig a felhasználóknak kell RUs beállításához használja az Azure-portálon vagy az AMS v2 API-k (a [media feldolgozási skálázás](../previous/media-services-scale-media-processing-overview.md). 

S3 RUs szükség a **VideoAnalyzerPreset** és **AudioAnalyzerPreset** V3 készletek.

## <a name="does-v3-asset-have-no-assetfile-concept"></a>Rendelkezik a V3 eszközök nem AssetFile koncepció?

A AssetFiles el lett távolítva az AMS API ahhoz, hogy eltérő a Media Services, Storage szolgáltatás SDK-függőség külön. Most tárolási, nem a Media Services tárolási tartja a tartozó adatokat. 

## <a name="where-did-client-side-storage-encryption-go"></a>Ügyféloldali tárolás titkosítása eltűnt

Most ajánlott kiszolgálóoldali tárolás titkosítása, (amelyek alapértelmezés szerint be van). További információkért lásd: [Azure Storage szolgáltatás titkosítási inaktív adatok](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="what-is-the-recommended-upload-method"></a>Az ajánlott fájlfeltöltési módszer mekkora?

Azt javasoljuk, hogy ingests http (s) használatát. További információkért lásd: [http (s) betöltési](job-input-from-http-how-to.md).

## <a name="how-does-pagination-work"></a>Hogyan működik a tördelési?

A Media Services $top támogatja az OData-t támogató erőforrások, de az átadott $top érték kisebb, mint 1000 (például lapmérete tördelési) kell lennie.

Ez lehetővé teszi a lapon, ha minden elemekről tördelési vagy $top (például a 100 legújabb hírek) használó elemek mintát vagy beszerzéséhez. 

A Media Services nem támogatja az adatok a megadott felhasználóhoz lapozást oldalméretével.

További információkért lásd: [szűrési, rendezési, lapozás](assets-concept.md#filtering-ordering-paging)

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Hogyan lehet lekérni a Media Services v3 entitás?

egyesített API felületen, amely felfedi beépített felügyeleti és a műveletek funkcióinak alapú v3 **Azure Resource Manager**. Összhangban **Azure Resource Manager**, mindig egyediek-erőforrás nevét. Ebből kifolyólag a erőforrásnevek összes egyedi azonosítót sztringet (például GUID) is használhatja. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Media Services v3 – áttekintés](media-services-overview.md)

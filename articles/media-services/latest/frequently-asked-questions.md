---
title: Az Azure Media Services v3 – gyakori kérdések |} A Microsoft Docs
description: Ez a cikk – gyakori kérdések az Azure Media Services v3 választ ad.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: dccbc6e57e970ec7089f81fccb33b741b9c00e74
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49376720"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Az Azure Media Services v3 – gyakori kérdések

Ez a cikk – gyakori kérdések az Azure Media Services (AMS) v3-as választ ad.

## <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Az Azure portal használatával v3-erőforrások kezelése?

még nem. A támogatott SDK-k egyikét használhatja. Oktatóanyagaink és példáink ezekben az doc talál.

## <a name="is-there-an-api-for-configuring-media-reserved-units"></a>Van egy API-t a Media szolgáltatás számára fenntartott egységek konfigurálásához?

A Media Services-csapat RUs távolítsa el a v3-as van. Azonban a szükséges szolgáltatást munkahelyi még nem fejeződött be. Addig a felhasználóknak kell az Azure portal vagy az AMS v2 API-k használatával állítsa be a kérelemegység (leírtak szerint [médiafeldolgozás skálázás](../previous/media-services-scale-media-processing-overview.md). 

Használata esetén **VideoAnalyzerPreset** és/vagy **AudioAnalyzerPreset**, a Media Services-fiók beállítása 10 S3 szintű Media szolgáltatás számára fenntartott egységet.

## <a name="does-v3-asset-have-no-assetfile-concept"></a>Rendelkezik a v3-as eszköz nincs AssetFile fogalom?

A AssetFiles annak érdekében, hogy a Media Services elkülönítése Storage SDK-függőség eltávolítása az AMS API-ból. Most már tárolási, nem a Media Services, Storage-ban tartja az információ, amelyhez tartozik. 

## <a name="where-did-client-side-storage-encryption-go"></a>Hová tűntek a storage ügyféloldali titkosítását?

Most már javasoljuk, hogy a kiszolgálóoldali storage encryption szolgáltatással (amely alapértelmezés szerint be van kapcsolva). További információkért lásd: [Azure Storage Service Encryption az inaktív adatok](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="what-is-the-recommended-upload-method"></a>Mi az az ajánlott feltöltési módszer?

Azt javasoljuk, hogy fogadnak http (s) használatát. További információkért lásd: [http (s) betöltési](job-input-from-http-how-to.md).

## <a name="how-does-pagination-work"></a>Hogyan működik a tördelés?

A Media Services OData támogató erőforrások $top támogatja, de átadott $top értéke nem lehet kisebb, mint 1000 (például az oldal méretét a tördelés).

Ez lehetővé teszi, vagy egy elemet a $top (például a 100 legutóbbi elemek) kis méretű mintát vagy való azonban tördelés használó összes elemet oldalon. 

A Media Services nem támogatja a lapozást az adatok között a megadott felhasználóhoz oldalméret.

További információkért lásd: [szűrése, rendezése, lapozás](assets-concept.md#filtering-ordering-paging)

## <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Hogyan kérheti le a Media Services v3 entitás?

V3 alapján egy egységes API-felület, épülő felügyeleti és a műveletek funkciókkal rendelkezik, amely **Azure Resource Manager**. Szolgáltatást **Azure Resource Manager**, az erőforrások nevei mindig egyediek. Így bármilyen egyedi azonosító sztringet (pl. GUID-ok) használhat erőforrásnévként. 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Media Services v3 áttekintése](media-services-overview.md)

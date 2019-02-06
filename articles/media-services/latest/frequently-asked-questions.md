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
ms.date: 02/05/2019
ms.author: juliako
ms.openlocfilehash: be4c08bc31c8811655230ab89b48271f4c2b3164
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756580"
---
# <a name="azure-media-services-v3-frequently-asked-questions"></a>Az Azure Media Services v3 – gyakori kérdések

Ez a cikk – gyakori kérdések az Azure Media Services (AMS) v3-as választ ad.

## <a name="v3-apis"></a>V3 API-k

### <a name="how-do-i-configure-media-reserved-units"></a>Hogyan konfigurálhatom a Media szolgáltatás számára fenntartott egységeket?

A hangelemzés és videó elemzési feladatokat a Media Services v3 által aktivált vagy a Video Indexer azt javasoljuk 10 S3 szintű helyet a fiók kiépítése. Ha több mint 10 S3-helyet, nyisson meg egy támogatási jegyet a a [az Azure portal](https://portal.azure.com/).

További információkért lásd: [méretezhető médiafeldolgozás a parancssori felület segítségével](media-reserved-units-cli-how-to.md).

### <a name="what-is-the-recommended-method-to-process-videos"></a>Mi az az ajánlott módszer a folyamat videókhoz?

Javasoljuk, hogy Ön egy HTTP (s) a videóra mutató URL-címet használó feladatok elküldéséhez. További információ [http (s) betöltési](job-input-from-http-how-to.md). Nem kell hoznia egy objektumot a bemeneti videó előtt fel lehessen dolgozni.

### <a name="how-does-pagination-work"></a>Hogyan működik a tördelés?

A Media Services OData támogató erőforrások $top támogatja, de átadott $top értéke nem lehet kisebb, mint 1000 (például az oldal méretét a tördelés).

Ez lehetővé teszi, vagy egy elemet a $top (például a 100 legutóbbi elemek) kis méretű mintát vagy való azonban tördelés használó összes elemet oldalon. 

A Media Services nem támogatja a lapozást az adatok között a megadott felhasználóhoz oldalméret.

További információkért lásd: [szűrése, rendezése, lapozás](entities-overview.md).

### <a name="how-to-retrieve-an-entity-in-media-services-v3"></a>Hogyan kérheti le a Media Services v3 entitás?

V3 alapján egy egységes API-felület, épülő felügyeleti és a műveletek funkciókkal rendelkezik, amely **Azure Resource Manager**. Szolgáltatást **Azure Resource Manager**, az erőforrások nevei mindig egyediek. Bármely egyedi azonosító karakterláncot (például, GUID), használhatja az erőforrás.

## <a name="live-streaming"></a>Live streaming (Élő adatfolyam) 

###  <a name="how-to-insert-breaksvideos-and-image-slates-during-live-stream"></a>Hogyan szúrhat be a sortöréseket és videók és lemezkép befutók során élő streamet?

A Media Services v3 valós idejű kódolás még nem támogatja beszúrása video- vagy képfájl befutók során élő streamet. 

Használhat egy [élő helyszíni kódolót](recommended-on-premises-live-encoders.md) váltani a forrásvideókat. Számos alkalmazás lehetővé teszi, hogy váltson a forrásból, például Telestream Wirecast, váltó Studio (az iOS esetében), OBS Studio (ingyenes app) és sok más.

## <a name="media-services-v2-vs-v3"></a>A Media Services v2 és v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Az Azure portal használatával v3-erőforrások kezelése?

még nem. A támogatott SDK-k egyikét használhatja. Oktatóanyagaink és példáink ezekben az doc talál.

### <a name="is-there-an-assetfile-concept-in-v3"></a>Van egy AssetFile fogalma a v3-as?

A AssetFiles annak érdekében, hogy a Media Services elkülönítése Storage SDK-függőség eltávolítása az AMS API-ból. Most már tárolási, nem a Media Services, Storage-ban tartja az információ, amelyhez tartozik. 

További információkért lásd: [áttelepítése a Media Services v3](migrate-from-v2-to-v3.md).

### <a name="where-did-client-side-storage-encryption-go"></a>Hová tűntek a storage ügyféloldali titkosítását?

Most már a kiszolgálóoldali tártitkosítás (amely alapértelmezés szerint be van kapcsolva) használata ajánlott. További információkért lásd: [Azure Storage Service Encryption az inaktív adatok](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Media Services v3 áttekintése](media-services-overview.md)

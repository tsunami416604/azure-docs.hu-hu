---
title: Regisztráljon a Video Indexerre, és töltse fel első videóját – Azure
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan regisztrálhat és töltheti fel az első videóját a Video Indexer portállal.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: juliako
ms.openlocfilehash: 22b06ce99333750d48d1cb65d9f60779572723b9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79499625"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Rövid útmutató: Hogyan regisztráljon és töltse fel az első videót

Ez az első lépések rövid útmutató bemutatja, hogyan jelentkezik be a Video Indexer webhelyre, és hogyan töltheti fel az első videót.

A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős beállítással létrehozhat egy Video Indexer-fiókot, amely [az Azure-előfizetéséhez kapcsolódik, valamint egy Azure Media Services-fiókot.](connect-to-azure.md) Ön az indexelt perceket és az Azure Media Services-fiókkal kapcsolatos díjakat fizeti ki. 

## <a name="sign-up-for-video-indexer"></a>Regisztrálás a Video Indexerre

A Video Indexerrel való fejlesztés megkezdéséhez nyissa meg a [Video Indexer](https://www.videoindexer.com) webhelyet, és regisztráljon.

> [!NOTE]
> A Video Indexer használatának megkezdése után az összes tárolt és feltöltött tartalom titkosítva lesz egy Microsoft által kezelt kulccsal.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Videó feltöltése a Video Indexer webhellyel

### <a name="supported-file-formats-for-video-indexer"></a>A Video Indexer támogatott fájlformátumai

A Video Indexer segítségével használható fájlformátumok listáját a [beviteli tároló/fájlformátumok](../latest/media-encoder-standard-formats.md#input-containerfile-formats) ról szóló cikkben találja.

### <a name="upload-a-video"></a>Videó feltöltése

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre.
2. A videó feltöltéséhez kattintson a **Feltöltés** gombra vagy hivatkozásra.

    > [!NOTE]
    > A videó neve nem lehet 80 karakternél nagyobb.

    ![Feltöltés](./media/video-indexer-get-started/video-indexer-upload.png)

    A videó feltöltése után a Video Indexer elkezdi indexelni és elemezni a videót.

    ![Feltöltve](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Ha a Video Indexer befejezte az elemzést, kapni fog egy értesítést, benne a videóra mutató hivatkozással és a videóban talált tartalom rövid leírásával. Például: személyek, témák, OCR-ek.

## <a name="see-also"></a>Lásd még

További részletekért [lásd: Videók feltöltése és indexelése.](upload-index-videos.md)

A videó feltöltése és indexelése után megkezdheti a [Video Indexer](video-indexer-view-edit.md) webhely vagy a [Video Indexer Developer Portal](video-indexer-use-apis.md) használatát a videó elemzési adatainak megtekintéséhez. 

[API-k használatának megkezdése](video-indexer-use-apis.md)

## <a name="next-steps"></a>További lépések

A részletes bevezetésért kérjük, látogasson el [a bevezetés labor](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md). 

A workshop végén jól ismeri a video- és audiotartalmakból kinyerhető információk típusait, felkészültebb lesz a tartalomintelligenciával kapcsolatos lehetőségek azonosítására, az Azure-on megjelenő videoai-k és a bemutatók több forgatókönyveket a Video Indexer.scenarios on Video Indexer.


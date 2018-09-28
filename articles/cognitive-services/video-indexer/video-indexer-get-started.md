---
title: 'Oktatóanyag: Regisztrálás a Video Indexerre és az első videó feltöltése'
titlesuffix: Azure Cognitive Services
description: Ismerje meg, hogyan regisztrálhat és töltheti fel az első videóját a Video Indexer portállal.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: tutorial
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: f7c86985d12d52ac2dc8b4d2952f048969d3c339
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984474"
---
# <a name="tutorial-how-to-sign-up-and-upload-your-first-video"></a>Oktatóanyag: Regisztrálás és az első videó feltöltése

Ez az első lépéseket ismertető oktatóanyag bemutatja, hogyan jelentkezhet be a Video Indexer webhelyre, és hogyan töltheti fel az első videóját.

A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős lehetőséggel Ön létrehoz egy Video Indexer-fiókot, amely [össze van kapcsolva az Azure-előfizetésével és egy Azure Media Services-fiókkal](connect-to-azure.md). Ön az indexelt perceket és az Azure Media Services-fiókkal kapcsolatos díjakat fizeti ki. 

## <a name="sign-up-for-video-indexer"></a>Regisztrálás a Video Indexerre

A Video Indexerrel való fejlesztés megkezdéséhez nyissa meg a [Video Indexer](https://www.videoindexer.com) webhelyet, és regisztráljon.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Videó feltöltése a Video Indexer webhellyel

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre.
2. A videó feltöltéséhez kattintson a **Feltöltés** gombra vagy hivatkozásra.

    ![Feltöltés](./media/video-indexer-get-started/video-indexer-upload.png)

    A videó feltöltése után a Video Indexer elkezdi indexelni és elemezni a videót.

    ![Feltöltve](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Ha a Video Indexer befejezte az elemzést, kapni fog egy értesítést, benne a videóra mutató hivatkozással és a videóban talált tartalom rövid leírásával. Például: személyek, témák, OCR-ek.

## <a name="next-steps"></a>További lépések

Most már használhatja a [Video Indexer](video-indexer-view-edit.md) webhelyet vagy a [Video Indexer fejlesztői portált](video-indexer-use-apis.md) a videó elemzéseinek megtekintésére. 

## <a name="see-also"></a>Lásd még

[A Video Indexer áttekintése](video-indexer-overview.md)

[Ismerkedés az API-kkal](video-indexer-use-apis.md).


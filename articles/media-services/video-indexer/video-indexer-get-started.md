---
title: Regisztráljon a Video Indexerre, és töltse fel első videóját – Azure
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan regisztrálhat és töltheti fel az első videóját a Video Indexer portállal.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: 1f3d442ea7cf6d95427aaa9c072410119e2735af
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989807"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Gyors útmutató: regisztráció és az első videó feltöltése

Ez az első lépéseket ismertető oktatóanyag bemutatja, hogyan jelentkezhet be a Video Indexer webhelyre, és hogyan töltheti fel az első videóját.

A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős megoldással olyan Video Indexer fiókot hozhat létre, amely az [Azure-előfizetéshez és egy Azure Media Services-fiókhoz csatlakozik](connect-to-azure.md). Ön az indexelt perceket és az Azure Media Services-fiókkal kapcsolatos díjakat fizeti ki. 

## <a name="sign-up-for-video-indexer"></a>Regisztrálás a Video Indexerre

A Video Indexerrel való fejlesztés megkezdéséhez nyissa meg a [Video Indexer](https://www.videoindexer.com) webhelyet, és regisztráljon.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Videó feltöltése a Video Indexer webhellyel

> [!NOTE]
> A videó neve nem lehet nagyobb, mint 80 karakter.

### <a name="supported-file-formats-for-video-indexer"></a>A Video Indexer támogatott fájlformátumai

A Video Indexer használatával használható fájlformátumok listáját a [bemeneti tároló/fájlformátumok](../latest/media-encoder-standard-formats.md#input-containerfile-formats) című cikkben találja.

### <a name="upload-a-video"></a>Videó feltöltése

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre.
2. A videó feltöltéséhez kattintson a **Feltöltés** gombra vagy hivatkozásra.

    ![Feltöltés](./media/video-indexer-get-started/video-indexer-upload.png)

    A videó feltöltése után a Video Indexer elkezdi indexelni és elemezni a videót.

    ![Feltöltve](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Ha a Video Indexer befejezte az elemzést, kapni fog egy értesítést, benne a videóra mutató hivatkozással és a videóban talált tartalom rövid leírásával. Például: személyek, témák, OCR-ek.

## <a name="see-also"></a>Lásd még:

További részletekért tekintse meg a [videók feltöltése és indexelése](upload-index-videos.md) című témakört.

A videók feltöltése és indexelése után megkezdheti [video Indexer](video-indexer-view-edit.md) webhely használatát, vagy [video Indexer fejlesztői portálon](video-indexer-use-apis.md) megtekintheti a videóban bekövetkező eredményeket. 

[Az API-k használatának megkezdése](video-indexer-use-apis.md)

## <a name="next-steps"></a>Következő lépések

A részletes bevezetést a [bevezető laborban](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md)tekintheti meg. 

A workshop végén jól megértette a videó-és hangtartalomból kinyerhető információk természetét, így jobban felkészült a tartalmi intelligenciával, a Pitch video AI-vel kapcsolatos lehetőségek azonosítására az Azure-ban, és számos bemutató forgatókönyvek Video Indexer.


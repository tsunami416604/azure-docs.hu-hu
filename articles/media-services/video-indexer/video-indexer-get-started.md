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
ms.openlocfilehash: f6c3953947e6f7e84f4cf9b565d2f66648b177f7
ms.sourcegitcommit: 666303748238dfdf9da30d49d89b915af73b0468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85130755"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Gyors útmutató: regisztráció és az első videó feltöltése

Ez az első lépéseket bemutató rövid útmutató bemutatja, hogyan jelentkezhet be a Video Indexer webhelyre, és hogyan töltheti fel első videóját.

A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős megoldással olyan Video Indexer fiókot hozhat létre, amely az [Azure-előfizetéshez és egy Azure Media Services-fiókhoz csatlakozik](connect-to-azure.md). Ön az indexelt perceket és az Azure Media Services-fiókkal kapcsolatos díjakat fizeti ki. 

## <a name="sign-up-for-video-indexer"></a>Regisztrálás a Video Indexerre

A Video Indexerrel való fejlesztés megkezdéséhez nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyet, és regisztráljon.

> [!NOTE]
> A Video Indexer használatának megkezdése után az összes tárolt és feltöltött tartalom titkosítása a Microsoft által felügyelt kulccsal történik.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Videó feltöltése a Video Indexer webhellyel

### <a name="supported-file-formats-for-video-indexer"></a>A Video Indexer támogatott fájlformátumai

A Video Indexer használatával használható fájlformátumok listáját a [bemeneti tároló/fájlformátumok](../latest/media-encoder-standard-formats.md#input-containerfile-formats) című cikkben találja.

### <a name="upload-a-video"></a>Videó feltöltése

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre.
2. A videó feltöltéséhez kattintson a **Feltöltés** gombra vagy hivatkozásra.

    > [!NOTE]
    > A videó neve nem lehet nagyobb, mint 80 karakter.

    ![Feltöltés](./media/video-indexer-get-started/video-indexer-upload.png)

    A videó feltöltése után a Video Indexer elkezdi indexelni és elemezni a videót.

    ![Feltöltve](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Ha a Video Indexer befejezte az elemzést, kapni fog egy értesítést, benne a videóra mutató hivatkozással és a videóban talált tartalom rövid leírásával. Például: személyek, témák, OCR-ek.

## <a name="see-also"></a>Lásd még

További részletekért tekintse meg a [videók feltöltése és indexelése](upload-index-videos.md) című témakört.

A videók feltöltése és indexelése után megkezdheti [video Indexer](video-indexer-view-edit.md) webhely használatát, vagy [video Indexer fejlesztői portálon](video-indexer-use-apis.md) megtekintheti a videóban bekövetkező eredményeket. 

[Az API-k használatának megkezdése](video-indexer-use-apis.md)

## <a name="next-steps"></a>További lépések

A részletes bevezetést a [bevezető laborban](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md)tekintheti meg. 

A workshop végén jól megértette a videó-és hangtartalomból kinyerhető információk típusát, így jobban felkészült a tartalmi intelligenciával, a Pitch video AI-vel kapcsolatos lehetőségek azonosítására az Azure-ban, és számos forgatókönyv bemutatása Video Indexer.


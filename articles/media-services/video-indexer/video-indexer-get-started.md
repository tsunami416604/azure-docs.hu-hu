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
ms.date: 10/19/2020
ms.author: juliako
ms.openlocfilehash: 6b154db25f1f13b60e4110d1b1866308ee952487
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219790"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Gyors útmutató: regisztráció és az első videó feltöltése

Ez az első lépéseket bemutató rövid útmutató bemutatja, hogyan jelentkezhet be a Video Indexer webhelyre, és hogyan töltheti fel első videóját.

A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős lehetőség használata esetén létre kell hoznia egy Video Indexer-fiókot, amely [össze van kapcsolva az Azure-előfizetésével és egy Azure Media Services-fiókkal](connect-to-azure.md). Ön az indexelt perceket és az Azure Media Services-fiókkal kapcsolatos díjakat fizeti ki. 

## <a name="sign-up-for-video-indexer"></a>Regisztrálás a Video Indexerre

A Video Indexerrel való fejlesztés megkezdéséhez nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyet, és regisztráljon.

> [!NOTE]
> A Video Indexer használatának megkezdése után az összes tárolt és feltöltött tartalom titkosítása a Microsoft által felügyelt kulccsal történik.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Videó feltöltése a Video Indexer webhellyel

### <a name="supported-file-formats-for-video-indexer"></a>A Video Indexer támogatott fájlformátumai

A Video Indexer használatával használható fájlformátumok listáját a [bemeneti tároló/fájlformátumok](../latest/media-encoder-standard-formats.md#input-containerfile-formats) című cikkben találja.

### <a name="upload-a-video"></a>Videó feltöltése

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre.
1. A videó feltöltéséhez kattintson a **Feltöltés** gombra vagy hivatkozásra.

    > [!NOTE]
    > A videó neve nem lehet nagyobb, mint 80 karakter.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="Feltöltés":::
1. A videó feltöltése után a Video Indexer elkezdi indexelni és elemezni a videót. Ekkor megjelenik a folyamat állapota. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="Feltöltés":::
1. A Video Indexer elemzése után egy e-mailt fog kapni a videóra mutató hivatkozással, és egy rövid leírást arról, hogy mi található a videóban. Például: személyek, beszélt és írott szavak, témakörök és elnevezett entitások.
1. Később megkeresheti a videóját a könyvtár listán, és különböző műveleteket hajthat végre. Például: keresés, újra index, szerkesztés.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="Feltöltés":::
 
## <a name="see-also"></a>Lásd még

További részletekért tekintse meg a [videók feltöltése és indexelése](upload-index-videos.md) című témakört.

A videók feltöltése és indexelése után megkezdheti [video Indexer webhely](video-indexer-view-edit.md) használatát, vagy [video Indexer fejlesztői portálon](video-indexer-use-apis.md) megtekintheti a videóban bekövetkező eredményeket. 

[Az API-k használatának megkezdése](video-indexer-use-apis.md)

## <a name="next-steps"></a>Következő lépések

A részletes bevezetést a [bevezető laborban](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md)tekintheti meg. 

A workshop végén jól megértette a videó-és hangtartalomból kinyerhető információk típusát, így jobban felkészült a tartalmi intelligenciával, a Pitch video AI-vel kapcsolatos lehetőségek azonosítására az Azure-ban, és számos forgatókönyv bemutatása Video Indexer.


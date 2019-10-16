---
title: Regisztráljon a Video Indexerre, és töltse fel első videóját – Azure
titlesuffix: Azure Media Services
description: Ismerje meg, hogyan regisztrálhat és töltheti fel az első videóját a Video Indexer portállal.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: juliako
ms.openlocfilehash: 9d89247740852b7bfc185fbcf864947efe30ef0f
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372349"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>Gyors útmutató: regisztráció és az első videó feltöltése

Ez az első lépéseket ismertető oktatóanyag bemutatja, hogyan jelentkezhet be a Video Indexer webhelyre, és hogyan töltheti fel az első videóját.

A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős megoldással olyan Video Indexer fiókot hozhat létre, amely az [Azure-előfizetéshez és egy Azure Media Services-fiókhoz csatlakozik](connect-to-azure.md). Ön az indexelt perceket és az Azure Media Services-fiókkal kapcsolatos díjakat fizeti ki. 

## <a name="sign-up-for-video-indexer"></a>Regisztrálás a Video Indexerre

A Video Indexerrel való fejlesztés megkezdéséhez nyissa meg a [Video Indexer](https://www.videoindexer.com) webhelyet, és regisztráljon.

## <a name="upload-a-video-using-the-video-indexer-website"></a>Videó feltöltése a Video Indexer webhellyel

> [!NOTE]
> A videó neve nem lehet nagyobb, mint 80 karakter.

1. Jelentkezzen be a [Video Indexer](https://www.videoindexer.ai/) webhelyre.
2. A videó feltöltéséhez kattintson a **Feltöltés** gombra vagy hivatkozásra.

    ![Feltöltés](./media/video-indexer-get-started/video-indexer-upload.png)

    A videó feltöltése után a Video Indexer elkezdi indexelni és elemezni a videót.

    ![Feltöltve](./media/video-indexer-get-started/video-indexer-uploaded.png) 

    Ha a Video Indexer befejezte az elemzést, kapni fog egy értesítést, benne a videóra mutató hivatkozással és a videóban talált tartalom rövid leírásával. Például: személyek, témák, OCR-ek.

## <a name="next-steps"></a>Következő lépések

Most már használhatja a [video Indexer](video-indexer-view-edit.md) webhelyet, vagy [video Indexer fejlesztői portálon](video-indexer-use-apis.md) megtekintheti a videó információit. 

## <a name="see-also"></a>Lásd még:

[A Video Indexer áttekintése](video-indexer-overview.md)

[Ismerkedés az API-kkal](video-indexer-use-apis.md).


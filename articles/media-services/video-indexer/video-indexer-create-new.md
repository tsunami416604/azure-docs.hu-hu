---
title: Videóelemzések létrehozása meglévő videókból – Azure
titlesuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan hozhat létre és tehet közzé videóelemzéseket meglévő videófájlok alapján.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 4a65e88e3f94f64a56bde882b535030968ae354d
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991276"
---
# <a name="create-highlights-from-existing-videos"></a>Kiemelt létrehozása meglévő videókból

Ez a témakör bemutatja, hogyan hozhat létre és tehet közzé videóelemzéseket más videók alapján.

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.
2. Keressen egy videót, amelyből létre szeretné hozni a videóelemzéseket.
3. Kattintson a **Lejátszás** gombra.

    Az oldal megjeleníti a videó összefoglalt elemzéseit. 

    ![Insights](./media/video-indexer-create-new/video-indexer-summarized-insights.png)
3. Kattintson a **Szerkesztés** gombra.

    Ez az oldal megjeleníti egy videó teljes lebontását. A lebontás blokkokra van osztva. A blokkok megkönnyítik az adatok áttekintését. A blokkokat például aszerint lehet bontani, hogy mikor váltanak a beszélők, vagy hogy mikor tartanak hosszú szünetet. Létrehozhatja a saját lejátszási listáját, amely csak a kívánt sorokat tartalmazza. Ha csak a forrásvideó bizonyos részeit szeretné megjeleníteni, témakörök/kulcsszavak, hangulatok, személyek és beszélők alapján szűrhet. Dönthet úgy, hogy csak a videó átiratát vagy az OCR-t tekinti meg.    

    ![Insights](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)
4. Hozza létre a lejátszási listáját.

    A sorok lejátszási listához való hozzáadásához, illetve arról való eltávolításához kattintson a **+**/**-** gombra.
5. Tekintse meg a lejátszási lista előnézetét.

    A lejátszási lista létrehozása után kattintson az **Előnézet** gombra.
6. Tegye közzé a lejátszási listát.

    Miután megtekintette az előnézetét, közzéteheti a lejátszási listát.

    A közzétett lejátszási listát a rendszer hozzáadja a videóelemzések listájához.


## <a name="next-steps"></a>További lépések 

Ha létrehozta az új lejátszási listát, az alábbi témakörökben leírtak szerint folytathatja a feldolgozását: 

- [Tartalomfeldolgozás a Video Indexer REST API-val](video-indexer-use-apis.md)
- [Vizuális widgetek beágyazása egy saját alkalmazásba](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Lásd még

[A Video Indexer áttekintése](video-indexer-overview.md) 

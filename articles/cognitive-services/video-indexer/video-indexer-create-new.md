---
title: 'Oktatóanyag: Videóelemzések létrehozása meglévő videókból'
titlesuffix: Azure Cognitive Services
description: Ez a témakör bemutatja, hogyan hozhat létre és tehet közzé videóelemzéseket meglévő videófájlok alapján.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: tutorial
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 3a3e5c62d9eb96563d395106db62cc91a8307658
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983917"
---
# <a name="tutorial-create-highlights-from-existing-videos"></a>Oktatóanyag: Kiemelések létrehozása meglévő videókból

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

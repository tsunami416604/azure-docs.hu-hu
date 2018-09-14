---
title: Az Azure Video Indexer segítségével videóelemzések létrehozása meglévő videókból |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan hozhat létre, és néhány egyéb videó alapján feltárását közzététele.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 09/09/2018
ms.author: juliako
ms.openlocfilehash: a713a85a3301b211f922268d6afc4d047bd12023
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2018
ms.locfileid: "45541701"
---
# <a name="create-highlights-from-existing-videos"></a>Kiemelt létrehozása meglévő videókból

Ez a témakör bemutatja, hogyan hozhat létre, és néhány egyéb videó alapján feltárását közzététele.

1. Keresse meg a [Video Indexer](https://www.videoindexer.ai/) webhelyet, és jelentkezzen be.
2. Keresse meg egy videót, amelyből hozzá szeretne létrehozni a feltárását.
3. Nyomja meg **lejátszása**.

    A lapon látható a videó összefoglaló insights. 

    ![Insights](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. Nyomja le az **szerkesztése** gombra.

    Ezen a lapon látható a videó teljes áttekintését. Táblázat összefoglalja a blokkok van osztva. Blokkok itt, hogy egyszerűbb legyen halad át az adatokat. Például blokk előfordulhat, hogy bonthatók fel alapján előadói módosítása, vagy hosszabb szünet. Létrehozhat saját csak a kívánt sorokat tartalmazó lista. Videó megjelenítése a forrás csak bizonyos részeit, a témakörök/kulcsszavakat, hangulati, személyek, előadói szűrhet. Ha szeretné, csak megtekinteni a videót a szövegben vagy OCR.    

    ![Insights](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. A lista létrehozása.

    Hozzáadhat és eltávolíthat a sorok és- tárolókról a lista, nyomja le a **+** / **-**.

5. A lista az előzetes verziójú.

    Ha elkészült a lista létrehozása, nyomja le az ENTER **előzetes**.
6. A lista közzététele.

    Miután a lista megtekintéséhez teheti közzé.

    Miután közzéteszi a listára, az hozzáadódik a videó elemzések listája.


## <a name="next-steps"></a>További lépések 

Miután létrehozta az új listát, továbbra is feldolgozása, ezek a témakörök leírtak szerint: 

- [Tartalomfeldolgozás a Video Indexer REST API-val](video-indexer-use-apis.md)
- [Vizuális widgetek beágyazása egy saját alkalmazásba](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Lásd még

[Video Indexer – áttekintés](video-indexer-overview.md) 

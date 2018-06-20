---
title: Használja az Azure videó indexelőt videó adatmodellünk a meglévő videók |} Microsoft Docs
description: Ez a témakör bemutatja, hogyan hozzon létre, és néhány egyéb videó alapján videó insights közzététele.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 06/19/2018
ms.author: juliako
ms.openlocfilehash: 8b336f2818769af8372320cadc20e3a152e51de2
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265580"
---
# <a name="create-highlights-from-existing-videos"></a>Hozzon létre emeli ki a meglévő videók

Ez a témakör bemutatja, hogyan hozzon létre, és néhány egyéb videó alapján videó insights közzététele.

1. Jelentkezzen be a [videó indexelő](https://api-portal.videoindexer.ai/) fiók.
2. Videó, amelyből el kívánja létrehozni az videó elemzések keresése.
3. Nyomja le az **lejátszása**.

    A lapon látható a videó röviden bemutatják azok insights. 

    ![Insights](./media/video-indexer-create-new/video-indexer-summarized-insights.png)

3. Nyomja meg a **szerkesztése** gombra.

    Ezen a lapon látható a teljes körű információkat a videó. A részletes információkat blokkokra megszakad. Blokkok jelenleg itt könnyebb halad át az adatokat. Például blokk előfordulhat, hogy kell bontani alapján amikor hangszórók változnak, vagy hosszú szünetet. Létrehozhat saját kívánt sorokat tartalmazó lista. Videó megjelenítése a forrás csak meghatározott részeit, témakörök/kulcsszavak, hangulati elemek, személyek, hangszórók szerint szűrheti. Ha szeretné, csak megtekinteni a videó Beszélgetés szövegének vagy OCR.    

    ![Insights](./media/video-indexer-create-new/video-indexer-create-new-playlist.png)

4. A lista létrehozása.

    Hozzáadni vagy eltávolítani a lista az sorok, nyomja meg az **+** / **-**.

5. A lista megtekintése

    Ha elkészült a lista létrehozása, nyomja le az **előzetes**.
6. A lista közzététele.

    Miután a lista megtekintéséhez is közzéteheti.

    Miután a lista közzétételéhez az videó elemzések listájára kerül.


## <a name="next-steps"></a>További lépések 

Miután létrehozta az új listát, továbbra is feldolgozása, az alábbi témakörökben találja leírtak szerint: 

- [Folyamatok tartalom videó indexelő REST API-hoz](video-indexer-use-apis.md)
- [Az alkalmazás visual widgeteket beágyazása](video-indexer-embed-widgets.md)

## <a name="see-also"></a>Lásd még

[Az indexelő áttekintő videó](video-indexer-overview.md) 

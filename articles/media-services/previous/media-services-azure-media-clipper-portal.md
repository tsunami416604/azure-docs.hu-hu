---
title: Az Azure Media Clipper használata a portálon | Microsoft Docs
description: Klipek létrehozása az Azure Media Clipper használatával az Azure Portalról
services: media-services
keywords: klip; alklip; kódolás; média
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ec43fa469547dcd6481c0c6781c438f42ab4e2bd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684987"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Klipek létrehozása az Azure Media Clipper-vel a portálon  

A portálon az Azure Media Clipper használatával klipeket hozhat létre a Media Services-fiókok eszközeiből. Első lépésként navigáljon a Media Services-fiókhoz a portálon. Ezután válassza az **alklip** fület.

Az **alklip** lapon megkezdheti a klipek szerkesztését. A portálon a Clipper egy érvényes adatfolyam-keresővel közzétett egyszeres sávszélességű MP4, többszörös sávszélességű MP4 és élő archívumokat tölt be. A nem közzétett eszközök nincsenek betöltve.

A Clipper jelenleg nyilvános előzetes verzióban érhető el. A Azure Portal lévő Clipper eléréséhez nyissa meg ezt a [nyilvános előnézet lapot](https://portal.azure.com/?feature.subclipper=true).

Az alábbi ábra a Media Services-fiókban található adattárolók kezdőlapját mutatja be: ![Azure Portal Azure Media Clipper-t](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Klipek készítése
Klip létrehozásához húzzon egy objektumot a klip felületére. Ha a megjelölési idő ismert, manuálisan is megadhatja azokat a felületre. Ellenkező esetben az eszköz lejátszásával vagy a pozícióhoz húzásával megkeresheti a kívánt jelölési és kijelzési időt. Ha nincs megadva a megjelölési vagy a kijelzési idő, a klip az elejétől kezdődik, vagy a bemeneti eszköz végéig folytatódik.

A frame-pontossággal/GOP-pontossággal való mozgáshoz használja a frame-Forward/GOP-Forward vagy a frame-visszafelé/GOP-visszafelé gombokat. Több eszköz kivágásához húzza a több objektumot a klip felületére az eszköz kiválasztása panelen. Az adategységeket kiválaszthatja és átrendezheti a kezelőfelületen a kívánt sorrendbe. Az eszköz kiválasztása panel minden eszköz esetében biztosítja az eszköz időtartamát, típusát és feloldási metaadatait. Több eszköz összefűzése esetén vegye figyelembe az egyes bemeneti fájlok forrás-feloldását. Ha a forrás felbontása eltérő, az alacsonyabb felbontású bemenet a legmagasabb megoldási eszköz felbontásának megfelelő. A kivágási feladatok kimenetének megtekintéséhez kattintson a Preview (előnézet) gombra, és a klip a kijelölt jelölési időpontokban játszik le.

## <a name="producing-dynamic-manifest-filters"></a>Dinamikus jegyzékfájl-szűrők készítése
A [dinamikus jegyzékfájlok szűrői](https://azure.microsoft.com/blog/dynamic-manifest/) a jegyzékfájlok és az eszközök idővonala alapján határozzák meg a szabályokat. Ezek a szabályok határozzák meg, hogy a folyamatos átviteli végpont hogyan kezelje a kimeneti listát (manifest). A szűrő használatával megváltoztathatja, hogy a rendszer mely szegmenseket továbbítsa a lejátszáshoz. A Clipper által létrehozott szűrők helyi szűrők, és a forrás-eszközre jellemzőek. A megjelenített klipekkel ellentétben a szűrők nem új eszközök, és nem igényelnek kódolási feladatot a létrehozásához. Ezek gyorsan létrehozhatók a [.net SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) -val vagy a [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest)használatával, de csak GOP-pontosak. Az adatfolyamként kódolt adategységek általában két másodperces, GOP-os mérettel rendelkeznek.

Dinamikus jegyzékfájl-szűrő létrehozásához navigáljon az **assets (eszközök** ) lapra, és válassza ki a kívánt objektumot. Válassza ki a felső menü **alklip** gombját. A speciális beállítások menüből válassza ki a dinamikus jegyzékfájl szűrése lehetőséget a kivágási mód beállításnál. Ezután ugyanezt a folyamatot követve hozhat létre egy megjelenített klipet a szűrő létrehozásához. Szűrők csak egyetlen eszközből állíthatók elő.

Az alábbi ábrán látható, hogy a Azure Portal dinamikus jegyzékfájl szűrési módja a következő képen látható: ![Azure Media Clipper a dinamikus jegyzékfájlok szűrési módjában Azure Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Levágási feladatok elküldése
Amikor befejezte a klip megfogalmazását, kattintson a küldési feladatok gombra a megfelelő levágási vagy dinamikus jegyzékfájl hívásának elindításához.

## <a name="next-steps"></a>További lépések
Az Azure Media Clipper használatának megkezdéséhez olvassa el az [első lépések](media-services-azure-media-clipper-getting-started.md) című cikket, amely részletesen ismerteti a widget üzembe helyezésének lépéseit.

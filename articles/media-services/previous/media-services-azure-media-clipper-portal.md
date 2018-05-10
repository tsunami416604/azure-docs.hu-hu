---
title: Kövesse a portálon, az Azure Media Webmontázskészítő |} Microsoft Docs
description: Azure Media Webmontázskészítő az Azure portálról használatával videóklipeket létrehozása
services: media-services
keywords: Klip; subclip; kódolás; adathordozó
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 1deca68cd6a61ede7536c4d5544036a10c54209b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Az Azure Media Webmontázskészítő videóklipeket létrehozása a portálon
Azure Media Webmontázskészítő a portálon hozhat létre videóklipeket eszközökből, a media services partnerek. A kezdéshez navigáljon a portálon a media services-fiókhoz. Ezután válassza ki a **Subclip** fülre.

Az a **Subclip** lapon elvégezheti videóklipeket összeállítása megkezdéséhez. A portálon a Webmontázskészítő egyféle sávszélességű MP4, a többszörös sávszélességű MP4 és az élő archívumot, amelyek közzététele egy érvényes streamelési locator tölti be. Közzé nem tett eszközök nincsenek betöltve.

A Webmontázskészítő jelenleg nyilvános előzetes verziójához. Az Azure portálon Webmontázskészítő eléréséhez nyissa meg a [nyilvános villámnézeti lap](https://portal.azure.com/?feature.subclipper=true).

Az alábbi ábrán a media services-fiók Webmontázskészítő kezdőlapja: ![Azure Media Webmontázskészítő Azure-portálon](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Videóklipeket elkészítése
Klip létrehozásához áthúzása egy eszköz, a klip felület. Ha a jel alkalommal ismert, manuálisan megadhatja azokat a felületén. Ellenkező esetben az eszköz lejátszás vagy húzza a playhead kívánt be van jelölve a Keresés és az idő. Ha be van jelölve a vagy a végpont egyszerre nem áll rendelkezésre, a klip elejétől kezdődik, vagy továbbra is a bemeneti eszköz végén kulcsattribútumokkal.

Keresse meg a keret-pontosság/GOP-pontosságának, használja a keret-továbbításra/GOP-továbbító vagy keret-visszamenőlegesen/GOP-hátra gombokat. A Kivágás adategységek ellen, áthúzása adategységek a klip felületén, az eszköz kijelölés panelről. Válassza ki, és újra sorrendbe állítja a felületen, a kívánt sorrendbe eszközök. Az eszköz ablaktáblát eszköz időtartama, típusa és feloldási metaadatok biztosít minden eszközhöz. Több eszköz együtt hozzáfűzésével, vegye figyelembe az egyes bemeneti fájlok a forrás feloldásához. Ha a forrás-megoldások eltérő, alacsonyabb feloldási bemeneti feloldása a legnagyobb felbontást eszköz a megfelelő van upscaled. A Kivágás-feladat eredményének megtekintéséhez válassza ki a kijelölt megjelölés idők az Előnézet gombra és a klip betöltött szerepe.

## <a name="producing-dynamic-manifest-filters"></a>Dinamikus jegyzék szűrők elkészítése
[Dinamikus jegyzék szűrők](https://azure.microsoft.com/blog/dynamic-manifest/) jegyzék attribútumok és az eszköz ütemterv alapján a szabályok ismertetik. Ezek a szabályok meghatározzák, hogyan kezeli a streamvégpontján a a kimeneti lista (jegyzékfájl). A szűrő módosítása, mely szegmensek átvitt lejátszás használható. A szűrők a Webmontázskészítő által létrehozott helyi szűrők és a forrás-eszközre jellemző. Eltérően megjelenített videóklipeket szűrők nem új eszközök, és nem szükséges egy kódolási feladat létrehozásához. Is gyorsan létrehozható keresztül a [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) vagy [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), azonban csak GOP pontos. Az adatfolyamként történő kódolású eszközök általában két másodpercen GOP méretet kell.

Dinamikus jegyzék szűrő létrehozásához keresse meg a **eszközök** lapra, és válassza ki a kívánt eszközt. Válassza ki a **Subclip** gombra a felső menüben. Válassza ki a dinamikus jegyzék szűrőt a Speciális beállítások menüből vágást módot. Kövesse ugyanezt az eljárást a szűrő létrehozásához megjelenített klip létrehozásához. Szűrők csak egyetlen eszköz előállíthatók.

Az alábbi ábrán a Webmontázskészítő módban dinamikus jegyzék szűrő az Azure-portálon: ![Azure Media Webmontázskészítő módban dinamikus jegyzék szűrő Azure-portálon](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Kivágás feladatok elküldése
Ha a klip létrehozásának befejezése után, válassza ki a kezdeményezése a küldési feladat gombra, a megfelelő Kivágás feladat vagy a dinamikus jegyzék hívás.

## <a name="next-steps"></a>További lépések
Azure Media Webmontázskészítő használatának megkezdéséhez olvassa el a [bevezetés](media-services-azure-media-clipper-getting-started.md) cikkben talál részletes információt a widget központi telepítéséről.
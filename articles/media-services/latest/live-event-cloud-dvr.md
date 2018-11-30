---
title: Az Azure Media Services videókhoz és a egy felhőalapú DVR-Kezelésre |} A Microsoft Docs
description: Ez a cikk leírja, mi LiveOutput és hogyan használhatja a felhőalapú DVR-Funkciókkal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 5de7496d73ebe1c89ce27ef27df73b197f34e7c7
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642290"
---
# <a name="using-a-cloud-dvr"></a>Használatával a felhőalapú DVR-Funkciókkal

A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) lehetővé teszi, hogy szabályozza a kimenő élő streamet, mekkora az adatfolyam rögzíti (például a kapacitás a felhőbeli DVR), és -e a nézők megkezdheti a élő streamet például tulajdonságait. Közötti kapcsolat egy **videókhoz** és a hozzá tartozó **LiveOutput**s kapcsolat hasonlít a hagyományos televíziós adás, amelynek során egy csatornát (**videókhoz**) egy videó és a egy felvételt állandó adatfolyam jelöli (**LiveOutput**) egy adott időpont szegmens (például esténként hírek a 18:30:00, 7:00 és 18) hatókörét. Rögzítheti a televízió digitális videót rögzítő (DVR) használatával – az egyenértékű LiveEvents szolgáltatással kezelt ArchiveWindowLength tulajdonságon keresztül. Egy ISO-8601 timespan időtartam (például PTHH:MM:SS), amely meghatározza a DVR kapacitását, és legfeljebb 25 óra legalább 3 percig állítható be.

## <a name="liveoutput"></a>LiveOutput

A **ArchiveWindowLength** érték határozza meg, milyen megjelenítő egy időben vissza az aktuális élő pozíciótól is kérhet.  **ArchiveWindowLength** is meghatározza, hogy mennyi ideig az ügyfél jegyzékfájljai.

Tegyük fel, hogy meg vannak streamelési labdarúgó játékot, és rendelkezik egy **ArchiveWindowLength** csak 30 perc. Az esemény figyelése 45 perc után a játék indító megjelenítő is megpróbálják vissza legfeljebb a 15 perces be van jelölve. A **LiveOutput**a játék s folytatódik, amíg a **videókhoz** le van állítva, de a tartalom, amely esik kívül **ArchiveWindowLength** folyamatosan elveti azokat a tárolás és nem állítható helyre. Ebben a példában a videó között az esemény kezdő és a 15 perces mark lenne eltávolításuk a DVR-Kezelésre, és a blob Storage-tárolóból a [eszköz](https://docs.microsoft.com/rest/api/media/assets). Az archívum nem helyreállítható, és eltávolítják az Azure blob storage-tárolóból.

Minden egyes **LiveOutput** társítva van egy **eszköz**, amellyel a videó rögzítése a társított Azure blob storage tárolóba. A LiveOutput közzétételéhez létre kell hoznia egy **StreamingLocator** , amely a **eszköz**. Miután létrehozott egy [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators), megadhatja, hogy a streamelési URL-cím hozhat létre.

A **videókhoz** támogatja a legfeljebb három egyidejűleg zajló **LiveOutput**s, legfeljebb 3 felvételek/archívumok hozhat létre egy élő streamet. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Tegyük fel, hogy szórási kell egy 24 x 7 élő lineáris hírcsatorna, és hozzon létre a különböző programok, igény szerinti tartalom utólagos megtekinthető az ügyfelek számára kínálnak a nap folyamán "oktatások". Ebben a forgatókönyvben, először hozzon létre egy elsődleges LiveOutput egy rövid archiválási ablak 1 óra vagy kevesebb mint – ez az, hogy a nézők lenne hangolása az elsődleges élő közvetítés. Akkor kell létrehoznia egy **StreamingLocator** ehhez **LiveOutput** , és tegye közzé az alkalmazás vagy webhely, a "Live" hírcsatorna. Bár a **videókhoz** van fut, programozott módon létrehozhat egy második párhuzamosan futó **LiveOutput** programot (vagy biztosít néhány leírók 5 perccel később trim) elején. Ebben a második **LiveOutput** 5 perc, a program befejezése után lehet törölni. Az ebben a második **eszköz**, létrehozhat egy új **StreamingLocator** közzétenni ezt a programot az alkalmazáskatalógusban igény szerinti eszközként. Ismételje meg a folyamat többször más program határok vagy emeli ki, akinél szeretné megosztani, igény szerinti videók, a "Live" eközben a hírcsatorna az első **LiveOutput** továbbra is a lineáris hírcsatorna szórási. 

> [!NOTE]
> **LiveOutput**s létrehozás indítása és leállítása, ha törli. Ha töröl a **LiveOutput**, nem törli az alapul szolgáló **eszköz** és a tartalom az eszközben.  

## <a name="next-steps"></a>További lépések

- [Élő adatfolyam – áttekintés](live-streaming-overview.md)
- [Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)


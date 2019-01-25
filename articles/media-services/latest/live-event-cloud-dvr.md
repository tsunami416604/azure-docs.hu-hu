---
title: Az Azure Media Services Live Event és a egy felhőalapú DVR-Kezelésre |} A Microsoft Docs
description: Ez a cikk leírja, mi élő kimeneti és használata egy felhőalapú DVR-Funkciókkal.
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
ms.date: 01/14/2019
ms.author: juliako
ms.openlocfilehash: 4dd14587ec7e1473953981c1ef8c32c59eb9a1d6
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888526"
---
# <a name="using-a-cloud-dvr"></a>Egy felhőalapú DVR használata

A [élő kimeneti](https://docs.microsoft.com/rest/api/media/liveoutputs) lehetővé teszi, hogy szabályozza a kimenő élő streamet, mekkora az adatfolyam rögzíti (például a kapacitás a felhőbeli DVR), és -e a nézők megkezdheti a élő streamet például tulajdonságait. Közötti kapcsolat egy **élő esemény** és a hozzá tartozó **élő kimeneti**s hasonlít a hagyományos televíziós adás, amelynek során egy csatornát (**élő esemény**) állandó jelöli Stream-videó és a egy felvételt (**élő kimeneti**) egy adott időpont szegmens (például esténként hírek a 18:30:00, 7:00 és 18) hatókörét. Rögzítheti a televízió digitális videót rögzítő (DVR) használatával – az élő események egyenértékű szolgáltatással kezelt ArchiveWindowLength tulajdonságon keresztül. Egy ISO-8601 timespan időtartam (például PTHH:MM:SS), amely meghatározza a DVR kapacitását, és legfeljebb 25 óra legalább 3 percig állítható be.

## <a name="live-output"></a>Élő kimenet

A **ArchiveWindowLength** érték határozza meg, milyen megjelenítő egy időben vissza az aktuális élő pozíciótól is kérhet.  **ArchiveWindowLength** is meghatározza, hogy mennyi ideig az ügyfél jegyzékfájljai.

Tegyük fel, hogy meg vannak streamelési labdarúgó játékot, és rendelkezik egy **ArchiveWindowLength** csak 30 perc. Az esemény figyelése 45 perc után a játék indító megjelenítő is megpróbálják vissza legfeljebb a 15 perces be van jelölve. A **élő kimeneti**a játék s folytatódik, amíg a **élő esemény** le van állítva, de a tartalom, amely esik kívül **ArchiveWindowLength** folyamatosan elveti azokat a tárolás és nem állítható helyre. Ebben a példában a videó között az esemény kezdő és a 15 perces mark lenne eltávolításuk a DVR-Kezelésre, és a blob Storage-tárolóból a [eszköz](https://docs.microsoft.com/rest/api/media/assets). Az archívum nem helyreállítható, és eltávolítják az Azure blob storage-tárolóból.

Minden egyes **élő kimeneti** társítva van egy **eszköz**, amellyel a videó rögzítése a társított Azure blob storage tárolóba. Az élő kimeneti közzétételéhez létre kell hoznia egy **Streamelési lokátor** , amely a **eszköz**. Miután létrehozott egy [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators), megadhatja, hogy a streamelési URL-cím hozhat létre.

A **élő esemény** támogatja a legfeljebb három egyidejűleg zajló **élő kimeneti**s, legfeljebb 3 felvételek/archívumok hozhat létre egy élő streamet. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Tegyük fel, hogy szórási kell egy 24 x 7 élő lineáris hírcsatorna, és hozzon létre a különböző programok, igény szerinti tartalom utólagos megtekinthető az ügyfelek számára kínálnak a nap folyamán "oktatások". Ebben a forgatókönyvben egy elsődleges élő kimenet létrehozása egy rövid archiválási ablak 1 óra, vagy kevesebb mint – ez az, hogy a nézők lenne hangolása az elsődleges élő közvetítés. Akkor kell létrehoznia egy **Streamelési lokátor** ehhez **élő kimeneti** , és tegye közzé az alkalmazás vagy webhely, a "Live" hírcsatorna. Bár a **élő esemény** van fut, programozott módon létrehozhat egy második párhuzamosan futó **élő kimeneti** programot (vagy biztosít néhány leírók 5 perccel később trim) elején. Ebben a második **élő kimeneti** 5 perc, a program befejezése után lehet törölni. Az ebben a második **eszköz**, létrehozhat egy új **Streamelési lokátor** közzétenni ezt a programot az alkalmazáskatalógusban igény szerinti eszközként. Ismételje meg a folyamat többször más program határok vagy emeli ki, akinél szeretné megosztani, igény szerinti videók, a "Live" eközben a hírcsatorna az első **élő kimeneti** továbbra is a lineáris hírcsatorna szórási. 

> [!NOTE]
> **Kimeneti élő**s létrehozás indítása és leállítása, ha törli. Ha töröl a **élő kimeneti**, nem törli az alapul szolgáló **eszköz** és az eszköz a tartalom. 
>
> Miután közzétette a **élő kimeneti** eszköz használatával egy **Streamelési lokátor**, a **élő esemény** (akár a DVR időszak hossza) továbbra is csak megtekinthető a **Streamelési lokátor**a lejárati vagy törlését, amelyik először bekövetkezik.

## <a name="next-steps"></a>További lépések

- [Élő adatfolyam – áttekintés](live-streaming-overview.md)
- [Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)


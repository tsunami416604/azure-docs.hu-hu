---
title: Élő események állapotai és számlázása Azure Media Servicesban | Microsoft Docs
description: Ez a témakör áttekintést nyújt Azure Media Services élő események állapotáról és számlázásáról.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 2d3d3f5c56bd42aeb148c19fefebc0e7d364cd1c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782365"
---
# <a name="live-event-states-and-billing"></a>Élő események állapotai és számlázása

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services egy élő esemény azonnal megkezdi a számlázást, amint az állapota **fut** vagy **készenléti** állapotba kerül. Akkor is számítunk fel díjat, ha nincs a szolgáltatáson keresztül áramló videó. Az élő esemény számlázásának leállításához le kell állítania az élő eseményt. Az élő átiratok számlázása ugyanúgy történik, mint az élő esemény.

Ha az [élő esemény](/rest/api/media/liveevents) **LiveEventEncodingType** standard vagy Premium1080p értékűre van állítva, akkor Media Services automatikusan kikapcsol minden olyan élő eseményt, amely még mindig a **futó** állapotban van, 12 órával a bemeneti csatorna elvesztése után, és nem fut **élő kimeneti** s. Azonban továbbra is fizetnie kell arra az időre, amíg az élő esemény **futó** állapotban volt.

> [!NOTE]
> Az áteresztő élő események nem állnak le automatikusan, és az API-n keresztül explicit módon le kell állítani őket, hogy elkerülje a túlzott mértékű számlázást.

## <a name="states"></a>Állapotok

Az élő esemény a következő állapotok egyikében lehet.

|Állam|Leírás|
|---|---|
|**Leállítva**| Ez az élő esemény kezdeti állapota a létrehozás után (kivéve, ha az autostart értéke TRUE (igaz).) Ebben az állapotban nem történik számlázás. Az élő esemény nem tud bemenetet fogadni. |
|**Indítás**| A rendszer elindítja az élő eseményt, és kiosztja az erőforrásokat. Ebben az állapotban nem történik számlázás.  Ha hiba lép fel, az élő esemény visszaállítja a leállított állapotot.|
| **Elosztásának** | A foglalási művelet meghívásra került az élő eseményen, és az erőforrások üzembe helyezése ehhez az élő eseményhez történik. Ha a művelet sikeresen befejeződött, az élő esemény készenléti állapotba kerül.
|**Készenléti**| az élő események erőforrásai üzembe lettek kiépítve, és készen állnak az indításra. A számlázás ebben az állapotban történik.  A legtöbb tulajdonság továbbra is frissíthető, azonban a betöltés vagy a folyamatos átvitel nem engedélyezett ebben az állapotban.
|**Futó**| Az élő esemény erőforrásai lefoglalva, betöltési és előnézeti URL-címek lettek létrehozva, és képesek élő streamek fogadására. Ezen a ponton a számlázás aktív. A további számlázás leállításához explicit módon hívnia kell az élő esemény erőforrásának leállítását.|
|**Leállítás**| Az élő esemény leáll, és a rendszer kiépíti az erőforrásokat. Ebben az átmeneti állapotban nem történik számlázás. |
|**Törlés**| Folyamatban van az élő esemény törlése. Ebben az átmeneti állapotban nem történik számlázás. |

Dönthet úgy, hogy engedélyezi az élő átírásokat az élő esemény létrehozásakor. Ha így tesz, az élő átírások után számítunk fel díjat, ha az élő esemény **fut** állapotban van. Vegye figyelembe, hogy a számlázás akkor is megtörténik, ha az élő eseményen nem folyik hang.

## <a name="next-steps"></a>Következő lépések

- [Élő közvetítés – áttekintés](live-streaming-overview.md)
- [Élő közvetítés – oktatóanyag](stream-live-tutorial-with-api.md)

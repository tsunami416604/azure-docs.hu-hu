---
title: LiveEvent-állapotok és számlázás az Azure Media Servicesszolgáltatásban | Microsoft dokumentumok
description: Ez a témakör áttekintést nyújt az Azure Media Services LiveEvent állapotairól és számlázásáról.
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
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: e4eee3b9f3f97bf7cd7a7b61425ec5c9a3a198ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543075"
---
# <a name="live-event-states-and-billing"></a>Élő esemény állapotai és számlázása

Az Azure Media Services szolgáltatásban az élő esemény akkor kezdi meg a számlázást, amikor az állapota **futásra vált.** Akkor is számlázunk, ha nincs videó, ami a szolgáltatáson keresztül áramlik. Az élő esemény számlázásának leállításához le kell állítania az élő eseményt. Az élő átírás számlázása ugyanúgy történik, mint az élő eseménynek.

Ha a **LiveEventEncodingType** a [Live Event Event edin](https://docs.microsoft.com/rest/api/media/liveevents) Standard vagy Premium1080p beállításra van állítva, a Media Services automatikusan kikapcsolja azokat a Live Event eseményeket, amelyek még mindig **futó** állapotban vannak 12 órával a bemeneti hírcsatorna elvész után, és **nincsenek futó live outputok.** Azonban továbbra is kiszámlázunk az élő esemény **futási** állapotának idejére.

> [!NOTE]
> Az átadó élő események nem kapcsolnak ki automatikusan, és a túlzott számlázás elkerülése érdekében explicit módon le kell állítani őket az API-n keresztül. 

## <a name="states"></a>Állapotok

Az élő esemény az alábbi állapotok egyikében lehet.

|Állapot|Leírás|
|---|---|
|**Leállítva**| Ez az élő esemény létrehozása utáni kezdeti állapota (kivéve, ha az automatikus indítás értéke igaz.) Ebben az állapotban nem történik számlázás. Ebben az állapotban az élő esemény tulajdonságai frissíthetők, de a streamelés nem engedélyezett.|
|**Kezdő**| Az élő esemény indítása folyamatban van, és erőforrások kiosztása folyamatban van. Ebben az állapotban nem történik számlázás. Ebben az állapotban nem engedélyezettek a frissítések vagy a streamelés. Hiba esetén az élő esemény visszaáll a Leállított állapotba.|
|**Fut**| Az élő esemény erőforrásai le vannak foglalva, a betöltési és előnézeti URL-címek létrejöttek, és képes élő közvetítések fogadására. Ezen a ponton a számlázás aktív. A további számlázás leállításához kifejezetten meg kell hívnia a Stop on the Live Event erőforrást.|
|**Leállítás**| Az élő esemény leállítása és az erőforrások kiépítésének megszűnése folyamatban van. Ebben az átmeneti állapotban nem történik számlázás. Ebben az állapotban nem engedélyezettek a frissítések vagy a streamelés.|
|**Törlés**| Az élő esemény törlődik. Ebben az átmeneti állapotban nem történik számlázás. Ebben az állapotban nem engedélyezettek a frissítések vagy a streamelés.|

Az élő esemény létrehozásakor engedélyezheti az élő átiratokat. Ha így tesz, akkor az élő átiratok ért ünk ki számlázást, amikor az élő esemény **futó** állapotban van. Ne feledje, hogy akkor is számlázunk önnek, ha az élő eseményen keresztül nem folyik át hang.

## <a name="next-steps"></a>További lépések

- [Élő közvetítés – áttekintés](live-streaming-overview.md)
- [Élő közvetítésoktató anyaga](stream-live-tutorial-with-api.md)

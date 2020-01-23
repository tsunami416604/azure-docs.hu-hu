---
title: LiveEvent állapotok és számlázás Azure Media Servicesban | Microsoft Docs
description: Ez a témakör áttekintést nyújt Azure Media Services LiveEvent állapotáról és számlázásáról.
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
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543075"
---
# <a name="live-event-states-and-billing"></a>Élő események állapotai és számlázása

Azure Media Services egy élő esemény azonnal megkezdi a számlázást, amint az állapota a **futásra**változik. Akkor is számítunk fel díjat, ha nincs a szolgáltatáson keresztül áramló videó. Az élő esemény számlázásának leállításához le kell állítania az élő eseményt. Az élő átiratok számlázása ugyanúgy történik, mint az élő esemény.

Ha az [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) **LiveEventEncodingType** standard vagy Premium1080p értékűre van állítva, akkor Media Services automatikusan kikapcsol minden olyan élő eseményt, amely még mindig a **futó** állapotban van, 12 órával a bemeneti csatorna elvesztése után, és nem fut **élő kimeneti**s. Azonban továbbra is fizetnie kell arra az időre, amíg az élő esemény **futó** állapotban volt.

> [!NOTE]
> Az áteresztő élő események nem állnak le automatikusan, és az API-n keresztül explicit módon le kell állítani őket, hogy elkerülje a túlzott mértékű számlázást. 

## <a name="states"></a>Állapotok

Az élő esemény a következő állapotok egyikében lehet.

|Állami|Leírás|
|---|---|
|**Megállt**| Ez az élő esemény kezdeti állapota a létrehozás után (kivéve, ha az autostart értéke TRUE (igaz).) Ebben az állapotban nem történik számlázás. Ebben az állapotban az élő esemény tulajdonságai módosíthatók, a streamelés azonban nem engedélyezett.|
|**Indítása**| Az élő esemény elindult, és az erőforrások le vannak foglalva. Ebben az állapotban nem történik számlázás. A frissítések vagy a folyamatos átvitel nem engedélyezett ebben az állapotban. Ha hiba lép fel, az élő esemény visszaállítja a leállított állapotot.|
|**Fut**| Az élő esemény erőforrásai lefoglalva, betöltési és előnézeti URL-címek lettek létrehozva, és képesek élő streamek fogadására. Ezen a ponton a számlázás aktív. A további számlázás leállításához explicit módon hívnia kell az élő esemény erőforrásának leállítását.|
|**Leállítása**| Az élő esemény leáll, és a rendszer kiépíti az erőforrásokat. Ebben az átmeneti állapotban nem történik számlázás. A frissítések vagy a folyamatos átvitel nem engedélyezett ebben az állapotban.|
|**Törlése**| Az élő esemény törlése folyamatban van. Ebben az átmeneti állapotban nem történik számlázás. A frissítések vagy a folyamatos átvitel nem engedélyezett ebben az állapotban.|

Dönthet úgy, hogy engedélyezi az élő átírásokat az élő esemény létrehozásakor. Ha így tesz, az élő átírások után számítunk fel díjat, ha az élő esemény **fut** állapotban van. Vegye figyelembe, hogy a számlázás akkor is megtörténik, ha az élő eseményen nem folyik hang.

## <a name="next-steps"></a>Következő lépések

- [Élő közvetítés – áttekintés](live-streaming-overview.md)
- [Élő közvetítés – oktatóanyag](stream-live-tutorial-with-api.md)

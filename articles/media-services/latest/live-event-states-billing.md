---
title: LiveEvent állapotok és számlázás Azure Media Servicesban | Microsoft Docs
description: Ez a témakör áttekintést nyújt Azure Media Services LiveEvent állapotáról és számlázásáról.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 37098b8268d5a451935c925114fb762c574fa55c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265303"
---
# <a name="live-event-states-and-billing"></a>Élő események állapotai és számlázása

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services egy élő esemény azonnal megkezdi a számlázást, amint az állapota a **futásra**változik. Akkor is számítunk fel díjat, ha nincs a szolgáltatáson keresztül áramló videó. Az élő esemény számlázásának leállításához le kell állítania az élő eseményt. Az élő átiratok számlázása ugyanúgy történik, mint az élő esemény.

Ha az [élő esemény](/rest/api/media/liveevents) **LiveEventEncodingType** standard vagy Premium1080p értékűre van állítva, akkor Media Services automatikusan kikapcsol minden olyan élő eseményt, amely még mindig a **futó** állapotban van, 12 órával a bemeneti csatorna elvesztése után, és nem fut **élő kimeneti**s. Azonban továbbra is fizetnie kell arra az időre, amíg az élő esemény **futó** állapotban volt.

> [!NOTE]
> Az áteresztő élő események nem állnak le automatikusan, és az API-n keresztül explicit módon le kell állítani őket, hogy elkerülje a túlzott mértékű számlázást. 

## <a name="states"></a>Állapotok

Az élő esemény a következő állapotok egyikében lehet.

|Állam|Leírás|
|---|---|
|**Leállítva**| Ez az élő esemény kezdeti állapota a létrehozás után (kivéve, ha az autostart értéke TRUE (igaz).) Ebben az állapotban nem történik számlázás. Ebben az állapotban az élő esemény tulajdonságai frissíthetők, de a folyamatos átvitel nem engedélyezett.|
|**Indítás**| Az élő esemény elindult, és az erőforrások le vannak foglalva. Ebben az állapotban nem történik számlázás. A frissítések vagy a folyamatos átvitel nem engedélyezett ebben az állapotban. Ha hiba lép fel, az élő esemény visszaállítja a leállított állapotot.|
|**Fut**| Az élő esemény erőforrásai lefoglalva, betöltési és előnézeti URL-címek lettek létrehozva, és képesek élő streamek fogadására. Ezen a ponton a számlázás aktív. A további számlázás leállításához explicit módon hívnia kell az élő esemény erőforrásának leállítását.|
|**Leállítás**| Az élő esemény leáll, és a rendszer kiépíti az erőforrásokat. Ebben az átmeneti állapotban nem történik számlázás. A frissítések vagy a folyamatos átvitel nem engedélyezett ebben az állapotban.|
|**Törlés**| Folyamatban van az élő esemény törlése. Ebben az átmeneti állapotban nem történik számlázás. A frissítések vagy a folyamatos átvitel nem engedélyezett ebben az állapotban.|

Dönthet úgy, hogy engedélyezi az élő átírásokat az élő esemény létrehozásakor. Ha így tesz, az élő átírások után számítunk fel díjat, ha az élő esemény **fut** állapotban van. Vegye figyelembe, hogy a számlázás akkor is megtörténik, ha az élő eseményen nem folyik hang.

## <a name="next-steps"></a>További lépések

- [Élő közvetítés – áttekintés](live-streaming-overview.md)
- [Élő közvetítés – oktatóanyag](stream-live-tutorial-with-api.md)

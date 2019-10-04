---
title: Videókhoz állapotok és az Azure Media Servicesben számlázási |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt az Azure Media Services videókhoz állapotok és a számlázás.
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
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 2907b5be7f8d5fda3d510484179e80b065ab64b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074892"
---
# <a name="live-event-states-and-billing"></a>Élő esemény állapotok és számlázás

Az Azure Media Services egy élő eseményt megkezdődik, amint állapotában átkerül számlázási **futó**. Az élő esemény, a számlázás megszüntetéséhez akkor állítsa le az élő esemény.

Amikor **LiveEventEncodingType** a a [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) van beállítva, a Standard vagy Premium1080p, automatikus után állítja le minden olyan élő eseményt, amely továbbra is a Media Services a **futó** 12 állapot óra után a bemeneti hírcsatorna elvész, és nincsenek nincs **élő kimeneti**futó s. Azonban továbbra is díjköteles volt az élő esemény indításakor a **futó** állapota.

## <a name="states"></a>Állapotok

Az élő esemény lehet a következő állapotok valamelyikében.

|Állapot|Leírás|
|---|---|
|**Stopped**| Ez a kezdeti állapota, az élő esemény a létrehozása után (kivéve, ha az autostart lett beállítva igaz.) Ebben az állapotban nem számlázási akkor fordul elő. Ebben az állapotban az élő esemény tulajdonságai frissíthetők, de a streamelés nem engedélyezett.|
|**Indítása**| Az élő esemény indítása folyamatban van, és a lefoglalt erőforrásokat. Ebben az állapotban nem számlázási akkor fordul elő. Frissítések vagy a streamelés nem engedélyezett ebben az állapotban. Ha hiba történik, az élő esemény leállított állapotra adja vissza.|
|**Fut**| Az élő esemény erőforrások van lefoglalva, betöltési és URL-címek előzetes jött létre, és élő adatfolyamok fogadni képes. Ezen a ponton a számlázási lesz aktív. Leállítás explicit módon kell meghívnia, további számlázási megállítani az élő esemény erőforráson.|
|**Leállítása**| Az élő esemény leállítás alatt van, és erőforrások eltávolítjuk folyamatban van. Nincs számlázási ez átmeneti állapot történik. Frissítések vagy a streamelés nem engedélyezett ebben az állapotban.|
|**Törlése**| Az élő esemény törlése folyamatban van. Nincs számlázási ez átmeneti állapot történik. Frissítések vagy a streamelés nem engedélyezett ebben az állapotban.|

## <a name="next-steps"></a>További lépések

- [Élő adatfolyam – áttekintés](live-streaming-overview.md)
- [Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)

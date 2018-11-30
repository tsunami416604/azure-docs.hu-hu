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
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: 1a49f62d7b5e21fe9d6483f71b729a9100aff1a3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585552"
---
# <a name="liveevent-states-and-billing"></a>Videókhoz állapotok és számlázás

Az Azure Media Services, a videókhoz megkezdődik, amint állapotában átkerül számlázási **futó**. Le szeretné állítani a számlázás a videókhoz, akkor állítsa le a videókhoz.

Amikor **LiveEventEncodingType** a a [videókhoz](https://docs.microsoft.com/rest/api/media/liveevents) standard (alapszintű), a Media Services automatikus leáll minden olyan videókhoz, amely továbbra is ki van állítva a **futó** állapot 12 óra Miután a bemeneti hírcsatorna elvész, és nincsenek nincs **LiveOutput**futó s. Azonban továbbra is díjköteles alkalommal volt a videókhoz a **futó** állapota.

## <a name="states"></a>Állapotok

A videókhoz a következő állapotok egyike lehet.

|Állapot|Leírás|
|---|---|
|**Leállítva**| Ez a videókhoz kezdeti állapotát a létrehozása után (kivéve, ha az autostart lett beállítva igaz.) Ebben az állapotban nem számlázási akkor fordul elő. Ebben az állapotban a videókhoz tulajdonságai frissíthetők, de a streamelés nem engedélyezett.|
|**Indítása**| A videókhoz indítás alatt van, és a lefoglalt erőforrásokat. Ebben az állapotban nem számlázási akkor fordul elő. Frissítések vagy a streamelés nem engedélyezett ebben az állapotban. Ha hiba történik, a videókhoz leállított állapotra adja vissza.|
|**Fut**| A videókhoz erőforrások van lefoglalva, betöltési és URL-címek előzetes jött létre, és élő adatfolyamok fogadni képes. Ezen a ponton a számlázási lesz aktív. Leállítás explicit módon kell meghívnia, a videókhoz további számlázási leállására erőforráson.|
|**Leállítása**| A videókhoz leállítás alatt van, és erőforrások eltávolítjuk folyamatban van. Nincs számlázási ez átmeneti állapot történik. Frissítések vagy a streamelés nem engedélyezett ebben az állapotban.|
|**Törlése**| A videókhoz törlése folyamatban van. Nincs számlázási ez átmeneti állapot történik. Frissítések vagy a streamelés nem engedélyezett ebben az állapotban.|

## <a name="next-steps"></a>További lépések

- [Élő adatfolyam – áttekintés](live-streaming-overview.md)
- [Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)

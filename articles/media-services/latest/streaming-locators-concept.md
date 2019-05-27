---
title: A streamelési Lokátorok az Azure Media Services |} A Microsoft Docs
description: Ez a cikk lehetővé teszi a Streamelési Lokátorok vannak, és hogyan használják az Azure Media Services ismertetése.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.openlocfilehash: 9a14399117971807c1d18f8eb5fab7d6e6cef2d5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120353"
---
# <a name="streaming-locators"></a>Streamelési lokátor

Ahhoz, hogy a kimeneti adategységben található videók elérhetők legyenek az ügyfelek számára lejátszásra, létre kell hozni egy [streamelési lokátort](https://docs.microsoft.com/rest/api/media/streaminglocators), majd streamelési URL-címeket. Egy .NET-példáért tekintse meg a [streamelési lokátor beszerzését](stream-files-tutorial-with-api.md#get-a-streaming-locator) bemutató cikket.

A folyamat létrehozásának egy **Streamelési lokátor** közzététel nevezzük. Alapértelmezés szerint a **Streamelési lokátor** érvényes az API-hívások végrehajtása után azonnal, és tart, amíg nem törli, ha nem konfigurál a választható kezdő és befejező időpontok. 

Létrehozásakor egy **Streamelési lokátor**, meg kell adnia egy **eszköz** nevét és a egy **Streamelési házirend** nevét. További információkért tekintse át a következők témaköröket:

* [Eszközök](assets-concept.md)
* [Streamelési szabályzatok](streaming-policy-concept.md)
* [Tartalomkulcs-szabályzatok](content-key-policy-concept.md)

> [!IMPORTANT]
> * Tulajdonságainak **Streamelési Lokátorok** a DateTime típusú állandóan UTC formátumban vannak.
> * Korlátozott számú házirendeket tervezzen a Media Services-fiók és újból felhasználja őket a Streamelési Lokátorok, amikor szükség van a beállítások. További információkért lásd: [kvóták és korlátozások](limits-quotas-constraints.md).

## <a name="associate-filters-with-streaming-locators"></a>Szűrők társítani a Streamelési Lokátorok

Lásd: [szűrők: társíthat a Streamelési Lokátorok](filters-concept.md#associate-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Szűrő, a sorrendben, a Streamelési lokátor entitások lap

Lásd: [szűrése, rendezése, a Media Services entitások lapozás](entities-overview.md).

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Videók feltöltése, kódolása és streamelése a .NET használatával](stream-files-tutorial-with-api.md)

---
title: Reagálás az Azure Media Services eseményeire | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogyan fizethet elő az Azure Event Grid del a Media Services-eseményekre.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/08/2019
ms.author: juliako
ms.openlocfilehash: e24bacb0ea7ab406442022915872fc77e9cc1a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887884"
---
# <a name="handling-event-grid-events"></a>Event Grid-események kezelése

A Media Services-események lehetővé teszik az alkalmazások számára, hogy a modern kiszolgáló nélküli architektúrák használatával reagáljanak a különböző eseményekre (például a feladat állapotának megváltoztatására irányuló eseményre). Ezt anélkül teszi, hogy bonyolult kódra vagy drága és nem hatékony közvélemény-kutatásra lenne szükség. Ehelyett az események et az [Azure Event Grid-ön](https://azure.microsoft.com/services/event-grid/) keresztül az eseménykezelők, például [az Azure Functions,](https://azure.microsoft.com/services/functions/) [az Azure Logic Apps,](https://azure.microsoft.com/services/logic-apps/)vagy akár a saját Webhook, és csak akkor kell fizetnie, amit használ. Az árképzésről az [Event Grid díjszabása](https://azure.microsoft.com/pricing/details/event-grid/)című témakörben talál további információt.

A Media Services-események elérhetősége az Eseményhálózat [elérhetőségéhez](../../event-grid/overview.md) kötődik, és az Event Grid hez képest más régiókban is elérhetővé válik.  

## <a name="media-services-events-and-schemas"></a>Media Services-események és sémák

Az eseményrács [esemény-előfizetéseket](../../event-grid/concepts.md#event-subscriptions) használ az eseményüzenetek előfizetőkhöz való irányításához. A Media Services-események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. A Media Services-eseményeket azonosítani tudja, mert az eventType tulajdonság "Microsoft.Media" néven kezdődik.

További információt a [Media Services eseménysémái című témakörben talál.](media-services-event-schemas.md)

## <a name="practices-for-consuming-events"></a>Az események fogyasztásának gyakorlata

A Media Services-eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot kell követniük:

* Mivel több előfizetés is konfigurálható úgy, hogy az eseményeket ugyanarra az eseménykezelőre irányítsa, fontos, hogy ne feltételezze, hogy az események egy adott forrásból származnak, hanem ellenőrizze az üzenet témáját, hogy megbizonyosodjon arról, hogy az a várt tárfiókból származik.
* Hasonlóképpen ellenőrizze, hogy az eventType olyan, amelyet fel kell dolgoznia, és ne feltételezze, hogy az összes kapott esemény a várt típusú lesz.
* Figyelmen kívül hagyja azolyan mezőket, amelyeket nem ért.  Ez a gyakorlat segít megőrizni a rugalmas új funkciók, amelyek a jövőben hozzáadott.
* A "tárgy" előtag és utótagegyezések használatával az eseményeket egy adott eseményre korlátozhatja.

> [!NOTE]
> Az eseményekre az Event Grid [szolgáltatásiszint-szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/)vonatkozik. Ha API-kkal szeretne eseményértesítéseket kapni, tekintse meg az események felhasználására vonatkozó példákat [a .NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) vagy [a Java SDK használatával.](https://github.com/Azure-Samples/media-services-v3-java)

## <a name="next-steps"></a>További lépések

* [Események figyelése - portál](monitor-events-portal-how-to.md)
* [Események monitorozása – Parancssori felület](job-state-events-cli-how-to.md)

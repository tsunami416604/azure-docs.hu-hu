---
title: Reagálás Azure Media Services eseményekre | Microsoft Docs
description: Media Services eseményekre való előfizetéshez használja a Azure Event Grid.
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
ms.openlocfilehash: 64bf8f5c8de5f56ee1140e91d0472a33b35570cf
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68878802"
---
# <a name="handling-event-grid-events"></a>Event Grid-események kezelése

Media Services események lehetővé teszik az alkalmazások számára a különböző eseményekre való reagálást (például a feladatok állapotának változási eseményét) a modern kiszolgáló nélküli architektúrák használatával. Ehhez nincs szükség bonyolult programkódra vagy költséges és nem hatékony lekérdezési szolgáltatásokra. Ehelyett az eseményeket [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) az eseménykezelők, például a [Azure Functions](https://azure.microsoft.com/services/functions/), a [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját webhook számára, és csak azért kell fizetnie, amit ténylegesen használ. További információ a díjszabásról: [Event Grid díjszabása](https://azure.microsoft.com/pricing/details/event-grid/).

Media Services események rendelkezésre állása Event Grid [rendelkezésre álláshoz](../../event-grid/overview.md) kötődik, és más régiókban is elérhetővé válik, mint Event Grid.  

## <a name="media-services-events-and-schemas"></a>Események és sémák Media Services

Az Event Grid [esemény](../../event-grid/concepts.md#event-subscriptions) -előfizetések használatával irányítja az esemény-üzeneteket az előfizetőknek. Media Services események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Azonosíthatja Media Services eseményt, mert a eventType tulajdonság a "Microsoft. Media" karakterlánccal kezdődik.

További információ: [Media Services esemény sémái](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Az események felhasználásának eljárásai

Az Media Services eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot követniük kell:

* Mivel több előfizetést is konfigurálhat az események ugyanahhoz az eseménykezelőhöz való átirányításához, fontos, hogy ne feltételezzük, hogy az események egy adott forrásból származnak, de az üzenet témakörének ellenőrzésével győződjön meg arról, hogy a várt Storage-fiókból származik.
* Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
* Figyelmen kívül hagyhatja a nem értelmezhető mezőket.  Ez a gyakorlat segít megőrizni a jövőben esetlegesen hozzáadott új funkciókkal való ellenálló képességet.
* Az események adott eseményre való korlátozásához használja a "tárgy" előtagot és utótagot.

## <a name="next-steps"></a>További lépések

* [Események figyelése – portál](monitor-events-portal-how-to.md)
* [Események figyelése – parancssori felület](job-state-events-cli-how-to.md)

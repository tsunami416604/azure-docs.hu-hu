---
title: Reagálás Azure Media Services eseményekre | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használható a Azure Event Grid az Media Services eseményekre való előfizetéshez.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74887884"
---
# <a name="handling-event-grid-events"></a>Event Grid-események kezelése

Media Services események lehetővé teszik az alkalmazások számára a különböző eseményekre való reagálást (például a feladatok állapotának változási eseményét) a modern kiszolgáló nélküli architektúrák használatával. Ehhez nincs szükség bonyolult programkódra vagy költséges és nem hatékony lekérdezési szolgáltatásokra. Ehelyett az eseményeket [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) az eseménykezelők, például a [Azure Functions](https://azure.microsoft.com/services/functions/), a [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját webhook számára, és csak azért kell fizetnie, amit ténylegesen használ. További információ a díjszabásról: [Event Grid díjszabása](https://azure.microsoft.com/pricing/details/event-grid/).

Media Services események rendelkezésre állása Event Grid [rendelkezésre álláshoz](../../event-grid/overview.md) kötődik, és más régiókban is elérhetővé válik, mint Event Grid.  

## <a name="media-services-events-and-schemas"></a>Események és sémák Media Services

Az Event Grid [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) használatával irányítja az esemény-üzeneteket az előfizetőknek. Media Services események tartalmazzák az adatok változásaira való válaszadáshoz szükséges összes információt. Azonosíthatja Media Services eseményt, mert a eventType tulajdonság a "Microsoft. Media" karakterlánccal kezdődik.

További információ: [Media Services esemény sémái](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Az események felhasználásának eljárásai

Az Media Services eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot követniük kell:

* Mivel több előfizetést is konfigurálhat az események ugyanahhoz az eseménykezelőhöz való átirányításához, fontos, hogy ne feltételezzük, hogy az események egy adott forrásból származnak, de az üzenet témakörének ellenőrzésével győződjön meg arról, hogy a várt Storage-fiókból származik.
* Hasonlóképpen győződjön meg arról, hogy a eventType az egyik készen áll a feldolgozásra, és nem feltételezi, hogy az összes kapott esemény lesz a várt típus.
* Figyelmen kívül hagyhatja a nem értelmezhető mezőket.  Ez a gyakorlat segít megőrizni a jövőben esetlegesen hozzáadott új funkciókkal való ellenálló képességet.
* Az események adott eseményre való korlátozásához használja a "tárgy" előtagot és utótagot.

> [!NOTE]
> Az események a Event Grid [szolgáltatói szerződés (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/)hatálya alá esnek. Ha API-kat használó esemény-értesítéseket szeretne kapni, tekintse meg a példákat az események használatáról a [.net SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) -val vagy a [Java SDK](https://github.com/Azure-Samples/media-services-v3-java)-val.

## <a name="next-steps"></a>További lépések

* [Események figyelése – portál](monitor-events-portal-how-to.md)
* [Események monitorozása – Parancssori felület](job-state-events-cli-how-to.md)

---
title: Reagálás eseményekre az Azure Media Services |} A Microsoft Docs
description: Azure Event Grid használatával előfizetni a Media Services-események.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/19/2018
ms.author: juliako
ms.openlocfilehash: 143fec2ddb168b0fff0e419fa5767e9718637241
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465537"
---
# <a name="reacting-to-media-services-events"></a>Reagálás a Media Services-eseményekre

Media Services-események lehetővé teszik a különböző események (például a feladat állapotváltozási esemény), a modern, kiszolgáló nélküli architektúra használatával reagálni alkalmazások. Így összetettebb kódja vagy költséges és hatékony lekérdezési szolgáltatások nélkül hajtja végre. Ehelyett eseményt leküld [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) eseménykezelőket, mint például a [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját Webhook, és Ön csak fizetniük a Mi használhat. Díjszabással kapcsolatos információkért lásd: [Event Grid díjszabási](https://azure.microsoft.com/pricing/details/event-grid/).

A Media Services-eseményekhez rendelkezésre állási vannak kötve, Event Grid [rendelkezésre állási](../../event-grid/overview.md) és Event Grid választókkal válnak más régiókban érhető el.  

## <a name="available-media-services-events"></a>Elérhető Media Services-események

Event grid használ [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) eseményt üzenetek továbbítását-előfizetők számára.  Media Services esemény-előfizetések jelenleg a következő események is tartalmazzák:  

|Eseménynév|Leírás|
|----------|-----------|
| Microsoft.Media.JobStateChange| Jelenik meg, ha a feladat módosítások állapotának bekéréséhez. |
| Microsoft.Media.LiveEventConnectionRejected | Kódoló kapcsolatfelvételt. |
| Microsoft.Media.LiveEventEncoderConnected | Kódoló az élő esemény kapcsolatot létesít. |
| Microsoft.Media.LiveEventEncoderDisconnected | Kódoló leválasztása. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Kiszolgáló adathalmaz csökken, mert késő vagy egy átfedő timestamp (időbélyeg az új adatok adattömbök érték kisebb, mint az előző adathalmaz befejezési időpontja). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Media kiszolgáló egyes nyomon követése az első adathalmaz kap a streamben vagy a kapcsolat. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Media-kiszolgáló észleli a hang és video-adatfolyamokat nincsenek szinkronban. Figyelmeztetés használjuk, mert előfordulhat, hogy nem változik a felhasználói élmény. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Media-kiszolgáló észleli a külső kódoló érkező két video-adatfolyamokat bármelyikét nincsenek szinkronban. Figyelmeztetés használjuk, mert előfordulhat, hogy nem változik a felhasználói élmény. |
| Microsoft.Media.LiveEventIngestHeartbeat | Közzétett 20 másodpercenként minden egyes nyomon követése, az élő esemény futtatásakor. Itt állapotösszegzése betöltését. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Kiszolgáló kihagyást észleli a bejövő nyomon követése. |

## <a name="event-schema"></a>Eseményséma

Media Services-események összes kell reagálni az igények változásaira az adatokban adatokat tartalmazzák.  A Media Services esemény is azonosítani, mert az esemény típusa tulajdonság "Microsoft.Media." karakterlánccal kezdődik.

További információkért lásd: [Media Services Eseménysémák](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Eljárások az események felhasználásához

Az alkalmazásokat, amelyek a Media Services-események kezeléséhez kövesse kell néhány ajánlott eljárást:

* Több előfizetés is beállíthatók úgy, hogy az azonos eseménykezelő események átirányítása, fontos, nem egy adott forrásból származó események feltételezik, de ellenőrzéséhez győződjön meg arról, hogy a tárfiók várt származnak üzenet a témakörben.
* Ehhez hasonlóan ellenőrizze, hogy az esemény típusa egy folyamat kész, és nem feltételezi, hogy kap az összes esemény lesz-e a várt típusú.
* Hagyja figyelmen kívül nem ismeri a mezőket.  Ez az eljárás fog megakadályozhatja, hogy rugalmas, előfordulhat, hogy a jövőben hozzáadott új funkciókhoz.
* A "tulajdonos" előtagot és utótagot egyezések segítségével korlátozhatja az egy adott eseményt eseményeket.

## <a name="next-steps"></a>További lépések

[Feladat állapota események beolvasása](job-state-events-cli-how-to.md)

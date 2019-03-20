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
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: cb5d6474a0c830933c712e1008015b5220617c96
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57850904"
---
# <a name="handling-event-grid-events"></a>Event Grid-események kezelése

Media Services-események lehetővé teszik a különböző események (például a feladat állapotváltozási esemény), a modern, kiszolgáló nélküli architektúra használatával reagálni alkalmazások. Így összetettebb kódja vagy költséges és hatékony lekérdezési szolgáltatások nélkül hajtja végre. Ehelyett eseményt leküld [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) eseménykezelőket, mint például a [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), vagy akár a saját Webhook, és Ön csak fizetniük a Mi használhat. Díjszabással kapcsolatos információkért lásd: [Event Grid díjszabási](https://azure.microsoft.com/pricing/details/event-grid/).

A Media Services-eseményekhez rendelkezésre állási vannak kötve, Event Grid [rendelkezésre állási](../../event-grid/overview.md) és Event Grid választókkal válnak más régiókban érhető el.  

## <a name="media-services-events-and-schemas"></a>Media Services-események és sémák

Event grid használ [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) eseményt üzenetek továbbítását-előfizetők számára. Media Services-események összes kell reagálni az igények változásaira az adatokban adatokat tartalmazzák. A Media Services esemény is azonosítani, mert az esemény típusa tulajdonság "Microsoft.Media." karakterlánccal kezdődik.

További információkért lásd: [Media Services Eseménysémák](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Eljárások az események felhasználásához

Az alkalmazásokat, amelyek a Media Services-események kezeléséhez kövesse kell néhány ajánlott eljárást:

* Több előfizetés is beállíthatók úgy, hogy az azonos eseménykezelő események átirányítása, fontos, nem egy adott forrásból származó események feltételezik, de ellenőrzéséhez győződjön meg arról, hogy a tárfiók várt származnak üzenet a témakörben.
* Ehhez hasonlóan ellenőrizze, hogy az esemény típusa egy folyamat kész, és nem feltételezi, hogy kap az összes esemény lesz-e a várt típusú.
* Hagyja figyelmen kívül nem ismeri a mezőket.  Ez az eljárás fog megakadályozhatja, hogy rugalmas, előfordulhat, hogy a jövőben hozzáadott új funkciókhoz.
* A "tulajdonos" előtagot és utótagot egyezések segítségével korlátozhatja az egy adott eseményt eseményeket.

## <a name="next-steps"></a>További lépések

[Feladat állapota események beolvasása](job-state-events-cli-how-to.md)

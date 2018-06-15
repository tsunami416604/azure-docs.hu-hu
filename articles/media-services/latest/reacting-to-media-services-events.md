---
title: Azure Media Services események reagálnak |} Microsoft Docs
description: Esemény rács Azure Media Services események használni.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 969957d53824bd70440e5529b83bc830bb5d9cc4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788150"
---
# <a name="reacting-to-media-services-events"></a>Reagál a Media Services-események

A Media Services események lehetővé teszik az alkalmazások különböző események (például a feladat állapotmódosítási esemény) használatával a modern kiszolgáló nélküli architektúrák reagálni. Igen, nincs szükség bonyolult kód vagy drága, és nem elég hatékony lekérdezési szolgáltatások. Ehelyett az események leküldött vannak [Azure esemény rács](https://azure.microsoft.com/services/event-grid/) eseménykezelők többek között a [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), vagy akár saját Webhook, és Ön csak fizetési mi használhat. További információk a díjszabásról: [esemény rács árképzési](https://azure.microsoft.com/pricing/details/event-grid/).

Rendelkezésre állás a Media Services események kötődik esemény rács [rendelkezésre állási](../../event-grid/overview.md) és más régiókban is elérhető lesz a esemény rács hasonlóan.  

## <a name="available-media-services-events"></a>Elérhető Media Services-események

Esemény rács által használt [esemény-előfizetések](../../event-grid/concepts.md#event-subscriptions) esemény üzenetek előfizetőknek.  Jelenleg a Media Services esemény-előfizetések is tartalmaz a következő esemény típusa:  

|Esemény neve|Leírás|
|----------|-----------|
| Microsoft.Media.JobStateChange| Jelenik meg, ha a feladat módosításokat állam. |

## <a name="event-schema"></a>Esemény séma

Media Services eseményeknek tartalmazniuk kell az adatok változásait összes információt.  Egy Media Services esemény azonosíthatja, mert a eventType tulajdonság kezdődik "Microsoft.Media.".

További információkért lásd: [Media Services esemény sémák](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Események felhasználásához tartozó eljárásokat

Alkalmazások, amelyek kezelik a Media Services események néhány javasolt eljárást kell követnie:

* Mivel több előfizetéssel beállítható úgy, hogy az azonos eseménykezelő útvonal események, fontos, nem egy adott forrásból származó események feltételezik, de az üzenet annak érdekében, hogy a tárfiók már vár származik a témakör kereséséhez.
* Ehhez hasonlóan ellenőrizze, hogy az esemény típusa egy felkészültek folyamat, és nem feltételezi, hogy kap az összes esemény lesz-e a várt típusú.
* Mezők nem világos, figyelmen kívül.  Ez az eljárás segítségével rugalmas nyomon lehet, hogy a jövőben hozzáadott új funkciók.
* Ezen a "tárgy" előtag és utótag megfelel egy adott esemény események.

## <a name="next-steps"></a>További lépések

[Feladat állapota események](job-state-events-cli-how-to.md)

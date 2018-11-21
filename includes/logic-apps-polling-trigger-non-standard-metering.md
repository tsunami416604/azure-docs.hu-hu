---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/09/2018
ms.author: estfan
ms.openlocfilehash: 3fa71085d649ace95aa24ac87c8714a7268f5386
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52270362"
---
Pontosabb használati költségeinek kiszámításához, érdemes lehet a lehető legkevesebb üzenetek és események, amely lehet bármelyik nap az ügyfélszámítógépekre érkeznek ahelyett, hogy a számítások alapján csak a lekérdezési időköz. Ha egy esemény vagy az üzenet megfelel a indítófeltételek, sok eseményindító azonnal próbálkozzon bármely és minden más várakozó eseményeket, vagy a megadott feltételeknek megfelelő üzenetek olvasásához. Ezzel a módszerrel, amely akkor is, ha kiválaszt egy hosszabb lekérdezési időközt, az eseményindító elindulása várakozó eseményeket, vagy üzeneteket, amelyek indítása a munkafolyamatok száma alapján. Eseményindítókat, amelyek kövesse ezt a viselkedést az Azure Service Bus és az Azure Event Hub tartalmazza.

Tehát tegyük fel, beállíthat egy eseményindítót, amely ellenőrzi a végpont minden nap. Ha a trigger ellenőrzi a végpont, és megkeresi a megadott feltételeket teljesítő 15 események, a akkor aktiválódik, majd futtatja a megfelelő munkafolyamatot 15 alkalommal. A Logic Apps-mérőszámai összes műveletet, hogy azok a 15 munkafolyamatok hajt végre, beleértve a kérelmeket. A lehetséges költségek kiszámítása, próbálja meg a [Azure díjkalkulátorát](https://azure.microsoft.com/pricing/calculator/).
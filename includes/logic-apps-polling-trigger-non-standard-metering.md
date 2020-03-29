---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74793442"
---
A pontosabb felhasználási költségek becsléséhez vegye figyelembe az adott napon esetlegesen érkező üzenetek vagy események lehetséges számát, ahelyett, hogy a számításokat csak a lekérdezési intervallumra alapozza. Ha egy esemény vagy üzenet megfelel az eseményindító-feltételeknek, sok eseményindító azonnal megpróbálja elolvasni a feltételeknek megfelelő várakozási eseményeket vagy üzeneteket. Ez a viselkedés azt jelenti, hogy még hosszabb lekérdezési időköz kiválasztásakor is az eseményindító a várakozási események vagy a munkafolyamatok indítására jogosult üzenetek száma alapján aktiválódik. A viselkedést követő eseményindítók közé tartozik az Azure Service Bus és az Azure Event Hub.

Tegyük fel például, hogy beállít egy olyan eseményindítót, amely minden nap ellenőrzi a végpontot. Amikor az eseményindító ellenőrzi a végpontot, és 15 olyan eseményt talál, amelyek megfelelnek a feltételeknek, az eseményindító 15-ször aktiválódik, és 15-ször futtatja a megfelelő munkafolyamatot. A Logic Apps a 15 munkafolyamat által végrehajtott összes műveletet, beleértve az eseményindító-kérelmeket is, méri. A lehetséges költségek kiszámításához próbálja ki az [Azure díjkalkulátorát.](https://azure.microsoft.com/pricing/calculator/)
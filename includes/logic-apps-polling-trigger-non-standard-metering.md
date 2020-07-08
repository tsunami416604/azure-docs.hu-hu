---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74793442"
---
A pontosabb használati költségek becsléséhez vegye figyelembe, hogy az adott napon esetlegesen megjelenő üzenetek vagy események száma nem a számítások csak a lekérdezési időintervallumban való kiosztása alapján történik. Ha egy esemény vagy üzenet megfelel az eseményindító feltételeinek, számos eseményindító azonnal megpróbálja beolvasni a feltételeknek megfelelő összes többi várakozási eseményt vagy üzenetet. Ez azt jelenti, hogy még akkor is, ha egy több lekérdezési időközt választ ki, az eseményindító a várakozó események vagy a munkafolyamatok elindítására jogosító üzenetek száma alapján indul el. A viselkedést követő triggerek közé tartozik a Azure Service Bus és az Azure Event hub.

Tegyük fel például, hogy olyan triggert állít be, amely naponta ellenőrzi a végpontot. Amikor az eseményindító ellenőrzi a végpontot, és 15 olyan eseményt talál, amely megfelel a feltételeknek, az eseményindító a megfelelő munkafolyamatot 15 alkalommal futtatja és futtatja. Logic Apps a 15 munkafolyamat által végrehajtott összes műveletet, beleértve az indító kérelmeket. A lehetséges költségek kiszámításához próbálja ki az [Azure díjszabási számológépét](https://azure.microsoft.com/pricing/calculator/).
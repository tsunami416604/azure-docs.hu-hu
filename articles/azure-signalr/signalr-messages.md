---
title: Üzenetek és kapcsolatok az Azure SignalR
description: Üzenetek és kapcsolatok az Azure SignalR Service legfontosabb fogalmak áttekintése.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 5a0430e9ad124319147342c49fc51e11472ac8ff
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811869"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Üzenet és az Azure SignalR Service-kapcsolat

Az Azure SignalR Service rendelkezik számlázási modell, a kapcsolatok száma és az üzenetek száma alapján. Hogyan az üzenetek és a kapcsolatok meghatározott és számlázási célból számítanak kifejtett alatt.

## <a name="message-formats-supported"></a>Támogatott üzenetformátumok

Az Azure SignalR Service, amely támogatja az ASP.NET Core SignalR ugyanazt a formátumot támogatja: [JSON](https://www.json.org/) és [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Üzenet mérete

Az Azure SignalR Service rendelkezik az üzenet mérete nem korlátozott.

A gyakorlatban, nagy méretű üzenetek van felosztva, amelyek kisebb üzenetekre, legfeljebb 2 KB-os, és külön üzenetekként továbbítani. SDK-k által kezelt felosztása és összeállításával készíthet átjárókat. Nincs fejlesztői erőfeszítésre van szükség.

De nagy méretű üzenetek negatív hatással van az üzenetkezelés teljesítményének. Használja a kisebb méretű üzenet mérete, amikor csak lehetséges, és ellenőrizze, hogy az egyes használati esetekhez optimális üzenet méretének kiválasztása.

## <a name="how-to-count-messages-for-billing-purpose"></a>Hogyan számlázási célú küldött üzenetek száma?

A Microsoft csak SignalR Service kimenő üzeneteinek száma, és figyelmen kívül az ügyfelek és kiszolgálók közötti ping üzenetekre.

Az üzenet 2 KB külön tranzakciónak minősül több üzeneteket az 2 KB-nál nagyobb. Üzenetek száma diagram az Azure Portalon minden 100 üzenet hub frissül.

Például a felhasználó rendelkezik 3-ügyfelek és 1 alkalmazás kiszolgáló. Egy ügyfél egy 4 KB-os üzenetet küld, hogy a kiszolgáló összes szórási. Az üzenetek száma 8 lesz: Alkalmazáskiszolgáló, 3 üzenet szolgáltatásból az ügyfeleknek és minden üzenetet szolgáltatásból 1 üzenetnek számít 2 2 KB-os üzenet.

Az Azure Portalon látható üzenetek száma továbbra is 0, amíg azt kell gyűlnek több mint 100.

## <a name="how-to-count-connections"></a>Hogyan kapcsolatok száma?

Nincsenek, kiszolgáló és az ügyfél-kapcsolatot. Alapértelmezés szerint minden kiszolgáló a SignalR Service hub 5 kapcsolata van, és minden egyes ügyfél rendelkezik SignalR Service 1 ügyfél-kapcsolattal.

Kapcsolatok száma is látható, az Azure Portalon egyaránt kiszolgáló és az ügyfél-kapcsolatot tartalmaz.

Például egy felhasználó alkalmazás két kiszolgáló található, és határozza meg, hogy 5 hubs kódokat. Az Azure Portalon látható Server-kapcsolatok száma 2 alkalmazás-kiszolgáló lesz * 5 hubs * 5 kapcsolatok/hub = 50 kiszolgálókapcsolatok.

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [ASP.NET Core SignalR-konfiguráció](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
---
title: Üzenetek és kapcsolatok az Azure SignalR
description: Üzenetek és kapcsolatok az Azure SignalR Service legfontosabb fogalmak áttekintése.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: c2348df7a1a55584807a03216e294486ddadfc52
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352597"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Üzenet és az Azure SignalR Service-kapcsolat

Az Azure SignalR Service rendelkezik számlázási modell, a kapcsolatok száma és az üzenetek száma alapján. Hogyan üzenetek és a kapcsolatok meghatározott és számlázási célból számítanak kifejtett alatt.

## <a name="message-formats-supported"></a>Támogatott üzenetformátumok

Az Azure SignalR Service, amely támogatja az ASP.NET Core SignalR ugyanazt a formátumot támogatja: [JSON](https://www.json.org/) és [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Üzenet mérete

Az Azure SignalR Service rendelkezik az üzenet mérete nem korlátozott.

A gyakorlatban, nagy méretű üzenetek van felosztva, amelyek kisebb üzenetekre, legfeljebb 2 KB-os, és külön üzenetekként továbbítani. SDK-k kezelésére felosztása és összeállításával készíthet átjárókat. Nincs fejlesztői erőfeszítésre van szükség.

De nagy méretű üzenetek negatív hatással van az üzenetkezelés teljesítményének. Használja a kisebb méretű üzenet mérete, amikor csak lehetséges, és ellenőrizze, hogy az egyes használati esetekhez optimális üzenet méretének kiválasztása.

## <a name="how-to-count-messages-for-billing-purpose"></a>Hogyan számlázási célú küldött üzenetek száma?

A Microsoft csak SignalR Service kimenő üzeneteinek száma, és figyelmen kívül az ügyfelek és kiszolgálók közötti ping üzenetekre.

Az üzenet 2 KB külön tranzakciónak minősül több üzeneteket az 2 KB-nál nagyobb. Üzenetek száma diagram az Azure Portalon minden 100 hub üzenet frissíti.

Például hogy három ügyfelek és a egy alkalmazáskiszolgáló. Egy ügyfél egy 4 KB-os üzenetet küld, hogy a kiszolgáló összes szórási. Az üzenetek száma 8: Alkalmazáskiszolgáló, az ügyfeleknek a szolgáltatás három üzeneteit és minden üzenetet a szolgáltatásból egy üzenet két 2 KB-os üzenetet fog számítani.

Az Azure Portalon látható üzenetek száma továbbra is 0, amíg azt kell gyűlnek több mint 100.

## <a name="how-to-count-connections"></a>Hogyan kapcsolatok száma?

Nincsenek, kiszolgáló és az ügyfél-kapcsolatot. Alapértelmezés szerint minden kiszolgáló a SignalR Service hub öt kapcsolata van, és minden egyes ügyfél SignalR Service egy ügyfél-kapcsolattal rendelkezik.

Kapcsolatok száma is látható, az Azure Portalon egyaránt kiszolgáló és az ügyfél-kapcsolatot tartalmaz.

Ha például két alkalmazás kiszolgáló, és meghatározza, öt hubs kódok. Kiszolgáló kapcsolati szám: 50: 2 alkalmazás-kiszolgáló * 5 hubs * 5 kapcsolatok/hub.

Az ASP.NET SignalR nem egyezik a kiszolgálói kapcsolatok kiszámítása. Felhasználó által definiált hubs mellett egy alapértelmezett hub rendelkezik. Minden egyes kiszolgáló alapértelmezés szerint 5 további kiszolgáló kapcsolatot kell. A kapcsolatok száma az alapértelmezett központ biztosítja, hogy más hubs összhangban.

## <a name="how-to-count-inbound-traffic--outbound-traffic"></a>Hogyan számlálható a forgalmat bejövő / kimenő forgalom

Bejövő / kimenő SignalR Service szemszögéből van. Az adatforgalmat számoljuk (bájt). Üzenetek száma, például a forgalmat a mintavételi ráta is tartalmaz. A bejövő / kimenő diagram az Azure Portalon minden 100 KB / hub frissíti.

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [Az Azure monitorban aggregáció típusa](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR-konfiguráció](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
---
title: Üzenetek és kapcsolatok az Azure SignalR Service-ben
description: Üzenetek és kapcsolatok az Azure SignalR Service kapcsolatos fontosabb fogalmak áttekintése.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: e82ce8f5c97aed7e2cb832d8e808ff84691f7c9e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555163"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Üzenetek és kapcsolatok az Azure SignalR Service-ben

A számlázási modellt az Azure SignalR Service kapcsolatok száma és az üzenetek száma alapján. Ez a cikk bemutatja, hogyan üzenetek és a kapcsolatok meghatározott és számlázásban is megjelenik.


## <a name="message-formats"></a>Üzenetformátumok 

Az Azure SignalR Service támogatja az ASP.NET Core SignalR megegyező formátumban: [JSON](https://www.json.org/) és [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Üzenet mérete

Az Azure SignalR Service rendelkezik üzenetek mérete nem korlátozott.

Nagy méretű üzenetek vannak felosztva, amelyek legfeljebb 2 KB kisebb méretű üzenetek és a továbbított külön-külön. SDK-k kezelésére felosztása és összeállításával készíthet átjárókat. Nincs fejlesztői erőfeszítésre van szükség.

Nagy méretű üzenetek ronthatja az üzenetkezelés teljesítményének. Kisebb üzeneteknek, amikor csak lehetséges, és ellenőrizze, hogy az optimális mérete egyes használatieset-forgatókönyvekhez.

## <a name="how-messages-are-counted-for-billing"></a>Hogyan bájtjai számítanak az üzeneteket a számlázás

A számlázás során csak az Azure SignalR Service-ből kimenő üzenetek számítanak. Ügyfelek és kiszolgálók közötti ping üzeneteket a rendszer figyelmen kívül hagyja.

2 KB-nál nagyobb üzenetek számítanak az 2 KB több üzenetet. Az Azure Portalon, az üzenet száma diagram frissül minden 100 hub üzenet.

Képzeljünk el például, hogy három ügyfelek és a egy alkalmazáskiszolgáló. Egy ügyfél, hogy a kiszolgáló összes szórási 4 KB-os üzenetet küld. Az üzenetek száma nyolc: a kiszolgáló és az ügyfelek számára a szolgáltatás három üzeneteit a szolgáltatásból egy üzenetet. Mindegyik üzenet két 2 KB-os üzenetet fog számítani.

Az Azure Portalon látható üzenetek száma 0 marad, amíg a azt a több mint 100 összegzi.

## <a name="how-connections-are-counted"></a>Hogyan számoljuk a kapcsolatok

Nincsenek, kiszolgáló és az ügyfél-kapcsolatot. Alapértelmezés szerint minden alkalmazás-kiszolgáló rendelkezik az Azure SignalR Service hub-nként öt, és minden ügyfél az Azure SignalR Service egy ügyfél-kapcsolattal rendelkezik.

A kapcsolatok száma is látható, az Azure Portalon egyaránt kiszolgáló és az ügyfél-kapcsolatot tartalmaz.

Tegyük fel, hogy rendelkezik-e két alkalmazáskiszolgálók és öt hubs meghatározhatja a kódban. A kiszolgáló csatlakozási számát 50 lesz: 2 alkalmazás-kiszolgáló * 5 hubs * 5 központ kapcsolata díjmentes.

Az ASP.NET SignalR kiszolgálókapcsolatok eltérő módon számítja ki. Ez magában foglalja az Ön által meghatározott hubs mellett egy alapértelmezett hub. Alapértelmezés szerint minden kiszolgáló öt további kiszolgálókapcsolatok van szüksége. A kapcsolatok száma alapértelmezett hub összhangban a többi hubs marad.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Hogyan számolja bejövő/kimenő forgalom

A bejövő és kimenő forgalom megkülönböztetése a terv az Azure SignalR Service alapul. Bájt adatforgalmat számoljuk. Például az üzenetek száma a forgalmat a mintavételi ráta is tartalmaz. Az Azure Portalon a bejövő/kimenő diagram frissül minden 100 KB / hub.

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [Az Azure monitorban típusú összesítést](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR-konfiguráció](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
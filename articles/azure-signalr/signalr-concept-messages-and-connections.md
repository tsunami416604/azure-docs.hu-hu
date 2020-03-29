---
title: Üzenetek és kapcsolatok az Azure SignalR szolgáltatásban
description: Az Azure SignalR-szolgáltatás üzeneteinek és kapcsolatainak legfontosabb fogalmainak áttekintése.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 5f6428231a3639738e8fb52e7dc3f2f2a3d2a26e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75392808"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Üzenetek és kapcsolatok az Azure SignalR szolgáltatásban

Az Azure SignalR Service számlázási modellje a kapcsolatok számán és az üzenetek számán alapul. Ez a cikk bemutatja, hogyan vannak definiálva és számolva az üzenetek és a kapcsolatok a számlázáshoz.


## <a name="message-formats"></a>Üzenetformátumok 

Az Azure SignalR Service ugyanazokat a formátumokat támogatja, mint ASP.NET Core SignalR: [JSON](https://www.json.org/) és [MessagePack.](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Üzenet mérete

Az Azure SignalR szolgáltatás nem rendelkezik az üzenetek méretkorlátjának.

A nagyméretű üzenetek kisebb üzenetekre vannak felosztva, amelyek egyenként legfeljebb 2 KB-osak, és külön továbbítják őket. Az SDK-k kezelik az üzenetek felosztását és összeállítását. Nincs szükség fejlesztői erőfeszítésekre.

A nagyméretű üzenetek negatívan befolyásolják az üzenetküldési teljesítményt. Amikor csak lehetséges, használjon kisebb üzeneteket, és tesztelje az egyes használati esetek optimális üzenetméretét.

## <a name="how-messages-are-counted-for-billing"></a>Az üzenetek számlázása

Számlázásesetén csak az Azure SignalR-szolgáltatásból érkező kimenő üzenetek számítanak. Az ügyfelek és a kiszolgálók közötti pingüzeneteket a rendszer figyelmen kívül hagyja.

A 2 KB-nál nagyobb üzenetek több, egyenként 2 KB-os üzenetnek számítanak. Az Azure Portalon az üzenetek száma diagram frissül minden 100 üzenet hubonként.

Tegyük fel például, hogy három ügyféllel és egy alkalmazáskiszolgálóval rendelkezik. Az egyik ügyfél 4 KB-os üzenetet küld, hogy a kiszolgáló minden ügyfélnek sugározzon. Az üzenetek száma nyolc: egy üzenet a szolgáltatástól az alkalmazáskiszolgálónak és három üzenet a szolgáltatástól az ügyfeleknek. Minden üzenet két 2 KB-os üzenetnek számít.

## <a name="how-connections-are-counted"></a>A kapcsolatok számlálása

Vannak kiszolgálókapcsolatok és ügyfélkapcsolatok az Azure SignalR szolgáltatással. Alapértelmezés szerint minden alkalmazáskiszolgáló hubonként öt kezdeti kapcsolattal kezdődik, és minden ügyfél egy ügyfélkapcsolattal rendelkezik.

Az Azure Portalon látható kapcsolatok száma kiszolgálói kapcsolatokat és ügyfélkapcsolatokat is tartalmaz.

Tegyük fel például, hogy két alkalmazáskiszolgálóval rendelkezik, és öt elosztót határoz meg a kódban. A kiszolgálói kapcsolatok száma 50 lesz: 2 alkalmazáskiszolgáló * 5 hub * 5 kapcsolat hubonként.

ASP.NET SignalR más módon számítja ki a kiszolgálói kapcsolatokat. A megadott hubok mellett egy alapértelmezett hubot is tartalmaz. Alapértelmezés szerint minden alkalmazáskiszolgálónak további öt kezdeti kiszolgálókapcsolatra van szüksége. Az alapértelmezett hub kezdeti kapcsolatszáma konzisztens marad a többi hubmal.

Az alkalmazáskiszolgáló élettartama alatt a szolgáltatás és az alkalmazáskiszolgáló megőrzi a szinkronizálási kapcsolat állapotát, és a jobb teljesítmény és a szolgáltatás stabilitása érdekében módosítja a kiszolgálókapcsolatokat. Így előfordulhat, hogy a kiszolgáló kapcsolatának száma időről időre megváltozik.

## <a name="how-inboundoutbound-traffic-is-counted"></a>A bejövő/kimenő forgalom számlálásának

A bejövő és a kimenő forgalom közötti különbségtétel az Azure SignalR-szolgáltatás perspektíváján alapul. A forgalom számítása bájtban történik.

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [Összesítési típusok az Azure Monitorban](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core SignalR konfiguráció](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack csomag](/aspnet/core/signalr/messagepackhubprotocol)

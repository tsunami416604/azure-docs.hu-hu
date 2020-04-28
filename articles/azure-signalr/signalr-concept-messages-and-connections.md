---
title: Üzenetek és kapcsolatok az Azure Signaler szolgáltatásban
description: Az Azure Signaler szolgáltatás üzeneteivel és kapcsolataival kapcsolatos főbb fogalmak áttekintése.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 5f6428231a3639738e8fb52e7dc3f2f2a3d2a26e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75392808"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Üzenetek és kapcsolatok az Azure Signaler szolgáltatásban

Az Azure Signaler szolgáltatás számlázási modellje a kapcsolatok számától és az üzenetek számától függ. Ez a cikk azt ismerteti, hogyan történik az üzenetek és a kapcsolatok meghatározása, és hogyan számítható ki a számlázás.


## <a name="message-formats"></a>Üzenetek formátuma 

Az Azure Signaler szolgáltatás ugyanazokat a formátumokat támogatja, mint a ASP.NET Core-jelző: [JSON](https://www.json.org/) és a [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Üzenet mérete

Az Azure Signaler szolgáltatáshoz nem tartozik méretkorlát az üzeneteknél.

A nagyméretű üzenetek felosztása kisebb üzenetekre történik, amelyek nem több mint 2 KB-ot kapnak, és külön továbbításra kerülnek. Az SDK-k az üzenetek felosztását és összeállítását kezelik. Nincs szükség fejlesztői erőfeszítésekre.

A nagyméretű üzenetek negatív hatással vannak az üzenetkezelés teljesítményére. Ha lehetséges, használjon kisebb üzeneteket, és tesztelje, hogy az egyes használati esetek optimális mérete milyen.

## <a name="how-messages-are-counted-for-billing"></a>Az üzenetek számlázásának számítása

A számlázáshoz csak az Azure Signaler szolgáltatás kimenő üzenetei számítanak. Az ügyfelek és a kiszolgálók közötti ping üzenetek figyelmen kívül lesznek hagyva.

A 2 KB-nál nagyobb üzenetek több, egyenként 2 KB-os üzenetnek számítanak. A Azure Portal üzenet száma diagramja minden egyes 100 üzenettel frissül.

Tegyük fel például, hogy három ügyfél és egy alkalmazáskiszolgáló van. Egy ügyfél 4 KB-os üzenetet küld, amely lehetővé teszi a kiszolgáló számára az összes ügyfél számára történő szórást. Az üzenetek száma nyolc: egy üzenet a szolgáltatásból az alkalmazáskiszolgáló és három üzenet a szolgáltatástól az ügyfeleknek. Minden üzenet két 2 KB-os üzenetnek számít.

## <a name="how-connections-are-counted"></a>A kapcsolatok számlálása

Kiszolgálói kapcsolatok és ügyfélkapcsolatok érhetők el az Azure Signaler szolgáltatással. Alapértelmezés szerint minden alkalmazáskiszolgáló öt kezdeti kapcsolattal kezdődik, és minden ügyfélnek van egy ügyfélkapcsolata.

A Azure Portalban látható kapcsolatok száma magában foglalja a kiszolgálói kapcsolatokat és az ügyfélkapcsolatokat is.

Tegyük fel például, hogy két alkalmazás-kiszolgálója van, és a kódban öt hub van definiálva. A kiszolgáló kapcsolatának száma 50:2 alkalmazás-kiszolgáló * 5 hub * 5 kapcsolat/központ.

A ASP.NET-jelző eltérő módon számítja ki a kiszolgáló kapcsolatait. Az Ön által meghatározott hubok mellett egy alapértelmezett hubot is tartalmaz. Alapértelmezés szerint minden alkalmazás-kiszolgálónak öt további kezdeti kiszolgálói kapcsolatra van szüksége. Az alapértelmezett hub kezdeti kapcsolati száma konzisztens marad a többi hubhoz.

Az alkalmazáskiszolgáló élettartama során a szolgáltatás és az alkalmazáskiszolgáló megtartja a szinkronizálási kapcsolat állapotát, és a jobb teljesítmény és a szolgáltatás stabilitása érdekében módosítja a kiszolgálói kapcsolatokat. Így előfordulhat, hogy a kiszolgáló-kapcsolatok száma időről időre változik.

## <a name="how-inboundoutbound-traffic-is-counted"></a>A bejövő/kimenő forgalom számításának módja

A bejövő forgalom és a kimenő forgalom közötti különbségtétel az Azure Signaler szolgáltatás perspektíváján alapul. A forgalmat bájtban számítjuk ki.

## <a name="related-resources"></a>Kapcsolódó források (lehet, hogy a cikkek angol nyelvűek)

- [Összesítési típusok a Azure Monitorban](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [ASP.NET Core jelző konfigurálása](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

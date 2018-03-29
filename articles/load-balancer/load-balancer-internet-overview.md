---
title: Internetre irányuló load balancer áttekintése |} Microsoft Docs
description: Az internetre irányuló terheléselosztót és a szolgáltatások áttekintése. A terheléselosztó működéséről az Azure virtuális gépek és felhőszolgáltatások használatával.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 529b37aa-a45c-41d1-8877-fee8cc1fa375
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 33a63538e3b96c5a37ddda6be31a0d972884b9b7
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="internet-facing-load-balancer-overview"></a>Az Internet felé néző terhelés terheléselosztó áttekintése


Az Azure terheléselosztó van leképezve a nyilvános IP-cím és port száma bejövő forgalmat a magánhálózati IP-cím és port számát a virtuális gép, és ez fordítva is igaz a válasz forgalmat a virtuális gépről. Terheléselosztási szabályok lehetővé teszik több virtuális gépek vagy szolgáltatások közötti forgalom adott típusú terjesztése. Például a webes kérelem forgalom terhelését elosztva is több webkiszolgálók vagy webes szerepkörök.

Egy felhőalapú szolgáltatás, amely tartalmazza a webes szerepkörök vagy feldolgozói szerepkörök példányai egy nyilvános végpontot meghatározhatja a szolgáltatás definíciós (.csdef) fájlban.

A *servicedefinition.csdef* fájl tartalmazza a végpont-konfiguráció, és ha több szerepkör példányát egy webes vagy feldolgozói szerepkör üzembe helyezése, a terheléselosztó lesz-e állítva. Példányok hozzáadása a felhő üzembe helyezése úgy módosítja a példányok száma a szolgáltatás konfigurációs fájljában (.csfg).

## <a name="example-of-an-internet-facing-load-balancer"></a>Internet felé néző terheléselosztó – példa

Az alábbi ábrán egy elosztott terhelésű végpont a webes forgalomban, amely a nyilvános és titkos TCP port a 80-as három virtuális gép között meg van osztva. A három virtuális gépek vannak egy elosztott terhelésű készlet.

![Példa nyilvános terheléselosztóra](./media/load-balancer-internet-overview/IC727496.png)

1. ábra – elosztott terhelésű végpont a webes forgalom

Amikor internetes ügyfelek weblap kérést küld a nyilvános IP-cím 80-as TCP-porton a felhőalapú szolgáltatás, az Azure terheléselosztó a kérelmeket az elosztott terhelésű készlet a három virtuális gépek között osztja el. A load balancer algoritmusok kapcsolatos további információkért lásd: a [alapvető load balancer – áttekintés oldalra](load-balancer-overview.md#fundamental-load-balancer-features).

Alapértelmezés szerint az Azure Load Balancer osztja el a hálózati forgalom több virtuálisgép-példánya között. Beállíthatja úgy is munkamenet-kapcsolatot, további információkért lásd: [terheléselosztó terheléselosztási mód](load-balancer-distribution-mode.md).

## <a name="next-steps"></a>További lépések

További tudnivalók [belső terheléselosztó](load-balancer-internal-overview.md) jobb megértése érdekében melyik terheléselosztó a felhő üzembe helyezése jobban megfelelnek.

Is [Internet felé néző terheléselosztó létrehozásához](load-balancer-get-started-internet-arm-ps.md) és konfigurálása, hogy milyen típusú [mód](load-balancer-distribution-mode.md) egy meghatározott típusú terheléselosztó hálózati forgalom viselkedését.

Ha az alkalmazásának fenn kell tartania a kapcsolatot a terheléselosztó mögött lévő kiszolgálókkal, többet megtudhat a [terheléselosztó üresjárati TCP-időkorlátjának beállításairól](load-balancer-tcp-idle-timeout.md). A segítségével jobban megismerheti az üresjárati kapcsolatok viselkedését az Azure Load Balancer használata során.

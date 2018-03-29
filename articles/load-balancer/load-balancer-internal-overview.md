---
title: Az Azure belső terheléselosztó áttekintése |} Microsoft Docs
description: A belső terheléselosztók működéséről az Azure és a belső végpontok konfigurálásakor forgatókönyvek.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0511165225f5a336291e86e0c504e60989933f3c
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="overview-of-azure-internal-load-balancer"></a>Az Azure belső terheléselosztó áttekintése



Az Azure belső Load Balancer (ILB) csak arra utasítja a erőforrásokat, amelyek belül egy felhőalapú szolgáltatás, vagy Azure-infrastruktúra eléréséhez használja, amely a VPN-forgalom. Ebben a tekintetben ILB eltér az internetre irányuló terheléselosztót. Azure-infrastruktúra korlátozza a hozzáférést, az elosztott terhelésű virtuális IP-cím (VIP) címét egy felhőalapú szolgáltatás vagy egy virtuális hálózathoz. Virtuális IP-címeket és a virtuális hálózatok soha nem érhetők el közvetlenül egy internet-végponthoz. Belső üzleti alkalmazások futtatása az Azure-ban, és Azure-ban vagy a helyszíni erőforrások érhetők el.

## <a name="why-you-might-need-an-internal-load-balancer"></a>Miért szükség lehet egy belső terheléselosztó

Belső terheléselosztó egy felhőalapú szolgáltatás, vagy a virtuális hálózat regionális hatókörbe belül található virtuális gépek (VM) közötti terheléselosztást biztosít. További információ a virtuális hálózatokat és a regionális hatókörbe: [regionális virtuális hálózatokba](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) az Azure blogján. Az affinitáscsoportokban beállított meglévő virtuális hálózatok Példánynak nem használható.

ILB lehetővé teszi, hogy a következő típusú terheléselosztási:

* A felhőszolgáltatásban: terheléselosztás virtuális gépek ugyanazt a felhőalapú szolgáltatás található virtuális gépek csoportjaira. Ez <a href="#figure1">példa</a>.
* A virtuális hálózaton belül: terheléselosztás virtuális gépek virtuális hálózatban lévő virtuális gépek ugyanazt a felhőalapú szolgáltatás, a virtuális hálózat található. Ez <a href="#figure2">példa</a>.
* A létesítmények közötti virtuális hálózat: terheléselosztás a helyszíni számítógépek lévő virtuális gépek ugyanazt a felhőalapú szolgáltatás, a virtuális hálózat található. Ez <a href="#figure3">példa</a>.
* A többrétegű alkalmazások: az internetre irányuló Többrétegű alkalmazások, ahol a háttér-rétegek nincsenek internetre terheléselosztás. A háttér-rétegek forgalom terheléselosztási az internetre irányuló réteg alapján szükséges.
* Az üzletági alkalmazások: az üzletági alkalmazásokhoz, amelyek az Azure-ban nélkül további load balancer hardver- vagy a terheléselosztás. Ez az eset tartalmazza a helyszíni kiszolgálók, amelyek a számítógépet, amelynek a forgalmát terhelésű készletében.

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>Az internet felé néző Többrétegű alkalmazások terheléselosztás

A webes réteg internet felé néző végpontok van az internetes ügyfeleket, és egy elosztott terhelésű készlet része. ILB osztja el a webkiszolgálók TCP port 443-as (HTTPS) webalkalmazás-ügyfelekről érkező bejövő forgalmat.

Az adatbázis-kiszolgálók, amelyek a webkiszolgálók használjanak tárhelyként ILB végpont mögött találhatók. A ILB végpont egy adatbázis-az elosztott terhelésű végpont esetében. Forgalom az elosztott terhelésű ILB készletében az adatbázis-kiszolgáló között.

A következő kép bemutatja, az internetre irányuló többrétegű alkalmazást belül az azonos felhőszolgáltatás belső terheléselosztást.

<a name="figure1"></a>
![Az Internet felé néző többrétegű alkalmazást](./media/load-balancer-internal-overview/IC736321.png)

Egy másik helyzet Többrétegű alkalmazások érhető el. Másik felhőalapú szolgáltatást a terheléselosztó, amely a Példánynak a szolgáltatás akkor azzal van telepítve.

Ugyanazt a virtuális hálózatot használó felhőszolgáltatások férhetnek hozzá a ILB végpont. A következő kép bemutatja az előtér-webkiszolgálók esetében, amelyek szerepelnek az adatbázis-háttér másik felhőalapú szolgáltatást. Az előtér-kiszolgálókat a ILB végpontot a virtuális hálózaton belül használják a háttérben.

<a name="figure2"></a>
![Másik felhőalapú szolgáltatást az előtér-kiszolgálók](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>Terheléselosztás intranet-üzletági alkalmazások

A helyszíni hálózaton-ügyfelektől érkező forgalom nem elosztott terhelésű az Azure-hálózatot VPN-kapcsolatot használó üzleti kiszolgálók készlete.

Az ügyfélszámítógépen. egy pont – hely VPN IP-cím el az Azure VPN szolgáltatást. A sor üzleti alkalmazás mögött a ILB végpont lehet üzemeltetni.

<a name="figure3"></a>
![Az üzletági alkalmazás ILB végpont mögött található](./media/load-balancer-internal-overview/IC744148.png)

Az üzletági alkalmazások egy másik helyzet lehet egy--webhelyek közötti VPN a virtuális hálózathoz, ahol a ILB végpont van konfigurálva. A helyszíni hálózati továbbítódik a Példánynak a végponthoz.

<a name="figure4"></a>
![A helyszíni hálózati forgalom ILB végpont irányítása](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>Korlátozások

Belső terheléselosztó nem támogatja a SNAT. Ebben a cikkben SNAT port színleg forrás hálózati címfordítás forgatókönyveit hivatkozik. A load balancer készlet egy virtuális Gépet el kell érnie a megfelelő belső terheléselosztó előtér-IP-címét. Kapcsolódási hibák akkor történik, ha a folyamat az elosztott terhelésű a virtuális géphez, amelyből származik a folyamatot. Ezek a forgatókönyvek nem támogatottak a Példánynak. A proxy-stílusú terheléselosztó használandók.

## <a name="next-steps"></a>További lépések

* [Azure Load Balancer Azure Resource Manager támogatása](load-balancer-arm.md)
* [Első lépések egy internetre irányuló terheléselosztót konfigurálása](load-balancer-get-started-internet-arm-ps.md)
* [Ismerkedjen meg a belső terheléselosztók konfigurálása](load-balancer-get-started-ilb-arm-ps.md)
* [A terheléselosztó elosztási módjának konfigurálása](load-balancer-distribution-mode.md)
* [A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)

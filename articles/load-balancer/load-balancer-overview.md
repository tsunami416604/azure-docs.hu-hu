---
title: Mi az az Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Az Azure Load Balancer funkciói, architektúrája és implementálása. Ismerje meg, hogyan működik a terheléselosztó, és hogyan használhatja azt a felhőben.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: allensu
ms.openlocfilehash: 2853b1567618127866a4e9c61d81e599d3100823
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064264"
---
# <a name="what-is-azure-load-balancer"></a>Mi az az Azure Load Balancer?

*A terheléselosztás* a terhelés (bejövő hálózati forgalom) egyenletes elosztását nevezi a háttérerőforrások vagy kiszolgálók egy csoportja között. 

Az Azure Load Balancer az Open Systems Interconnection (OSI) modell negyedik rétegében működik. Ez az ügyfelek egyetlen kapcsolattartási pontja. A terheléselosztó olyan bejövő folyamatokat oszt ki, amelyek a terheléselosztó előtér-háttérkészlet-példányaihoz érkeznek. Ezek a folyamatok a konfigurált terheléselosztási szabályok és az állapotminták szerint vannak. A háttérkészlet-példányok lehetnek Azure virtuális gépek vagy egy virtuálisgép-méretezési csoport példányai.

A nyilvános terheléselosztó kimenő kapcsolatokat biztosíthat a virtuális hálózaton belüli virtuális gépekhez.A **[public load balancer](./concepts-limitations.md#publicloadbalancer)** can provide outbound connections for virtual machines (VM) inside your virtual network. Ezek a kapcsolatok úgy valósulnak meg, hogy a privát IP-címeket nyilvános IP-címekre fordítják. Nyilvános terheléselosztók a virtuális gépek internetes forgalmának terheléselosztási szolgálnak.

Egy **[belső (vagy privát) terheléselosztó](./concepts-limitations.md#internalloadbalancer)** t használ, ahol a privát IP-k csak az előtérben van szükség. A belső terheléselosztók a virtuális hálózaton belüli terheléselosztási forgalomhoz használatosak. A terheléselosztó előtér érhető el egy helyszíni hálózatról egy hibrid forgatókönyvben érhető el.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="512" title="Azure Load Balancer">
</p>

*Ábra: A többrétegű alkalmazások kiegyensúlyozása nyilvános és belső terheléselosztó használatával*

Az egyes terheléselosztó-összetevőkről az [Azure Load Balancer összetevőiről és korlátairól](./concepts-limitations.md) talál további információt.

>[!NOTE]
> Az Azure teljeskörűen felügyelt terheléselosztási megoldások együttesét biztosítja a különböző forgatókönyvekre. Ha nagy teljesítményű, alacsony késleltetésű, Layer-7-es terheléselosztásra van szüksége, olvassa el [a Mi az Azure Application Gateway?](../application-gateway/overview.md) Ha globális DNS-terheléselosztást keres, olvassa el [a Mi a Forgalomkezelő?](../traffic-manager/traffic-manager-overview.md) A végpontok között forgatókönyvek hasznos lehet ezek a megoldások kombinálása.
>
> Az Azure terheléselosztási beállítások összehasonlításáról [az Azure terheléselosztási lehetőségeinek áttekintése](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)című témakörben olvashat.

## <a name="why-use-azure-load-balancer"></a>Miért érdemes használni az Azure Load Balancer-t?
A Standard Load Balancer segítségével méretezheti alkalmazásait, és magas rendelkezésre állású szolgáltatásokat hozhat létre. A terheléselosztó támogatja a bejövő és a kimenő forgatókönyveket is. A terheléselosztó alacsony késleltetést és nagy átviteli feszültséget biztosít, és akár több millió folyamatot is felskáláz minden TCP- és UDP-alkalmazáshoz.

A standard terheléselosztóval elvégezhető főbb forgatókönyvek a következők:

- Terheléselosztás **[belső](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** és **[külső](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** forgalmat az Azure virtuális gépekre.

- Növelje a rendelkezésre állást az erőforrások **[zónákon belüli](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** és **[zónák közötti](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** elosztásával.

- Konfigurálja **[a kimenő kapcsolatot](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** az Azure virtuális gépekhez.

- Az **[állapotminta segítségével](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** figyelheti a terheléselosztásos erőforrásokat.

- Porttovábbítás **[alkalmazásával](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** nyilvános IP-cím és port segítségével érheti el a virtuális hálózatban lévő virtuális gépeket.

- Az **[IPv6](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** **[terheléselosztástámogatásának](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** engedélyezése.

- A Standard Load Balancer többdimenziós metrikákat biztosít az [Azure Monitoron](https://docs.microsoft.com/azure/azure-monitor/overview)keresztül.  Ezek a metrikák szűrhetők, csoportosíthatók és bonthatók egy adott dimenzióra.  Aktuális és történelmi betekintést nyújtanak a szolgáltatás teljesítményébe és állapotába.  Az erőforrás-állapot is támogatott. További részletekért tekintse át a **[Standard Load Balancer Diagnostics című](load-balancer-standard-diagnostics.md)** adatokat.

- Terheléselosztási szolgáltatások **[több porton, több IP-címen vagy mindkettőn.](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)**

- Belső **[internal](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** és **[külső](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** terheléselosztó-erőforrások áthelyezése az Azure-régiók között.

- Terheléselosztás TCP és UDP flow minden porton egyszerre ha **[portok](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** használatával .

### <a name="secure-by-default"></a><a name="securebydefault"></a>Alapértelmezés szerint biztonságos

A standard terheléselosztó a nulla megbízhatóságú hálózati biztonsági modellre épül. A standard terheléselosztó alapértelmezés szerint biztonságos, és a virtuális hálózat része. A virtuális hálózat egy privát és elszigetelt hálózat.  Ez azt jelenti, hogy a standard terheléselosztók és a szabványos nyilvános IP-címek a bejövő folyamatok számára vannak bezárva, kivéve, ha a hálózati biztonsági csoportok megnyitják őket. Az NSG-k kifejezetten lehetővé teszik az engedélyezett forgalmat.  Ha nem rendelkezik NSG-vel a virtuálisgép-erőforrás alhálózatán vagy hálózati adapterén, a forgalom nem érheti el ezt az erőforrást. Ha többet szeretne tudni az NSG-kről és arról, hogyan alkalmazhatja őket a forgatókönyvhöz, olvassa el a Hálózati biztonsági csoportok ( Hálózati biztonsági csoportok ) [(Hálózati biztonsági csoportok) témakört.](../virtual-network/security-overview.md)
Az alapszintű terheléselosztó alapértelmezés szerint nyitva áll az internet számára.


## <a name="pricing-and-sla"></a>Árképzés és SLA

A standard terheléselosztó árképzési információiról a [Terheléselosztó díjszabása](https://azure.microsoft.com/pricing/details/load-balancer/)című témakörben talál.
Az Alapszintű Load Balancer használata ingyenes.
Lásd: [SLA a terheléselosztó](https://aka.ms/lbsla). Az egyszerű terheléselosztó nem rendelkezik SLA-val.

## <a name="next-steps"></a>További lépések

Lásd: [Nyilvános standard terheléselosztó létrehozása](quickstart-load-balancer-standard-public-portal.md) a terheléselosztó használatának megkezdéséhez.

Az Azure Load Balancer korlátairól és összetevőiről az [Azure Load Balancer fogalmai és korlátai](./concepts-limitations.md) című témakörben talál további információt.

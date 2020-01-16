---
title: Mi az az Azure Load Balancer?
titleSuffix: Azure Load Balancer
description: Az Azure Load Balancer funkciói, architektúrája és implementálása. Ismerje meg, hogyan működik a Load Balancer és hogyan használható a felhőben.
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
ms.date: 1/14/2019
ms.author: allensu
ms.openlocfilehash: dc986d40d50b93720c87ba36d265ed3044b0abc9
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045395"
---
# <a name="what-is-azure-load-balancer"></a>Mi az az Azure Load Balancer?

A *terheléselosztás* a betöltés (bejövő hálózati forgalom) egyenletes elosztását jelenti a háttérbeli erőforrások vagy kiszolgálók egy csoportján belül. Az Azure [számos terheléselosztási lehetőséget](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) kínál, amelyeket igény szerint választhat. Ez a dokumentum ismerteti a Azure Load Balancer.

Azure Load Balancer a nyílt rendszerek közötti összekapcsolási (OSI) modell négy rétegében működik. Ez az egyetlen kapcsolódási pont az ügyfelek számára. A Load Balancer az új bejövő folyamatokat osztja el, amelyek a terheléselosztó előtér-példányaira érkeznek. Ezek a folyamatok a konfigurált terheléselosztási szabályok és az állapot-mintavételek szerint vannak megadva. A háttérbeli készlet példányai lehetnek Azure-Virtual Machines vagy-példányok egy virtuálisgép-méretezési csoportokban.


A **[nyilvános terheléselosztó](./concepts-limitations.md#publicloadbalancer)** a virtuális hálózatban lévő virtuális gépek (VM-EK) kimenő kapcsolatait is biztosítja. Ezek a kapcsolatok a magánhálózati IP-címek nyilvános IP-címekre való fordításával valósíthatók meg. A nyilvános terheléselosztó a virtuális gépek internetes forgalmának elosztására szolgál.

A **[belső (vagy magánhálózati)](./concepts-limitations.md#internalloadbalancer)** terheléselosztó akkor használatos, ha a magánhálózati IP-címekre csak a frontenden van szükség. A belső terheléselosztó a virtuális hálózaton belüli forgalom elosztására szolgál. A terheléselosztó felülete hibrid forgatókönyv esetén a helyszíni hálózatról is elérhető.

<div align="center">
  <img src='./media/load-balancer-overview/IC744147.png'>
</div>

*Ábra: többrétegű alkalmazások terheléselosztása nyilvános és belső Load Balancer használatával*

További információ az egyes terheléselosztó-összetevőkről: [Azure Load Balancer összetevők és korlátozások](./concepts-limitations.md)

>[!NOTE]
> A Microsoft javasolja [standard Load Balancer](./load-balancer-standard-overview.md).
Az önálló virtuális gépeket, a rendelkezésre állási csoportokat és a Virtual Machine Scale-készleteket csak egy termékváltozathoz csatlakoztathatja, sosem kettőhöz egyszerre. A Load Balancer és a nyilvános IP-cím SKU-nak egyeznie kell, ha nyilvános IP-címekkel használja őket. A Load Balancer és a nyilvános IP-címek nem változtathatók meg.

## <a name="why-use-azure-load-balancer"></a>Miért érdemes Azure Load Balancer?
A Azure Load Balancer segítségével méretezheti az alkalmazásokat, és létrehozhat olyan, magasan elérhető szolgáltatásokat. A Load Balancer mind a bejövő, mind a kimenő forgatókönyveket támogatja. A Load Balancer alacsony késést és nagy átviteli sebességet biztosít, és akár több millió folyamatra is méretezhető az összes TCP-és UDP-alkalmazás esetében.

A Azure Load Balancer használatával végrehajtható főbb forgatókönyvek a következők:

- A **[belső](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-manage-portal)** és a **[külső](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-internal-portal)** forgalom terheléselosztása az Azure Virtual Machines szolgáltatásba.

- Növelje a rendelkezésre állást az erőforrások zónán **[belüli](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zonal-portal)** és **[közötti](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal)** elosztásával.

- Az Azure-beli virtuális gépek **[kimenő kapcsolatának](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)** konfigurálása.

- Az **[állapot](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** -mintavétel segítségével figyelheti a terheléselosztási erőforrásokat.

- A **[port továbbításával](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** a virtuális gépek nyilvános IP-cím és port használatával férhetnek hozzá a virtuális hálózatokhoz.

- Engedélyezze az IPv6 **[terheléselosztásának](https://docs.microsoft.com/azure/virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell)** támogatását **[](https://docs.microsoft.com/azure/virtual-network/ipv6-overview)** .

- Kihasználhatja a **[metrikák és a diagnosztika](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)** előnyeit a **[Azure monitorokkal](https://docs.microsoft.com/azure/azure-monitor/overview)** Azure Load Balancer.

- Terheléselosztási szolgáltatásokat **[több porton, több IP-címen vagy mindkettőn](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview)** .

- **[Belső](https://docs.microsoft.com/azure/load-balancer/move-across-regions-internal-load-balancer-portal)** és **[külső](https://docs.microsoft.com/azure/load-balancer/move-across-regions-external-load-balancer-portal)** terheléselosztó erőforrásainak áthelyezése az Azure-régiók között.

- A TCP-és UDP-forgalom terheléselosztása minden porton egyidejűleg **[Ha portokat](https://docs.microsoft.com/azure/load-balancer/load-balancer-ha-ports-overview)** használ.

## <a name="pricing"></a>Díjszabás

Standard Load Balancer használat díjat számítunk fel.

* A konfigurált terheléselosztási és kimenő szabályok száma. A bejövő NAT-szabályok nem számítanak bele a szabályok teljes számára.
* A feldolgozott és a kimenő adatforgalom mennyisége a szabályoktól függetlenül.

Standard Load Balancer díjszabási információkért lásd: [Load Balancer díjszabása](https://azure.microsoft.com/pricing/details/load-balancer/).

Az Alapszintű Load Balancer használata ingyenes.

## <a name="sla"></a>Szolgáltatási szerződés

További információ a standard Load Balancer SLA-ról: [Load Balancer SLA](https://aka.ms/lbsla)-ja.

## <a name="next-steps"></a>Következő lépések

A Load Balancer használatának első lépéseiért tekintse meg [a nyilvános standard Load Balancer létrehozását](quickstart-load-balancer-standard-public-portal.md) ismertető témakört.

A Azure Load Balancer korlátozásokkal és összetevőkkel kapcsolatos további információkért lásd: [Azure Load Balancer fogalmak és korlátozások](./concepts-limitations.md)

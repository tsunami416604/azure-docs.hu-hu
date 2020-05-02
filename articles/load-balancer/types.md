---
title: Azure Load Balancer típusok
description: Azure Load Balancer típusok áttekintése
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 31e2bf19967bb8870ee6ab75687bb3fcc37373f7
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629972"
---
# <a name="azure-load-balancer-types"></a>Azure Load Balancer típusok

Azure Load Balancer két típussal és két SKU-val rendelkezik.

## <a name="public-load-balancer"></a><a name = "publicloadbalancer"></a>Nyilvános Load Balancer

A nyilvános terheléselosztó a nyilvános IP-címet és a bejövő forgalom portját a virtuális gép magánhálózati IP-címére és portjára képezi le. A Load Balancer a virtuális gépről érkező válasz felé irányuló forgalmat is leképezi. A terheléselosztási szabályok alkalmazásával adott típusú adatforgalom több virtuális gépre vagy szolgáltatásba is terjeszthető. A webkérések adatforgalmát például eloszthatja több webkiszolgáló között.

>[!NOTE]
>Rendelkezésre állási csoporton belül csak egy nyilvános terheléselosztó és egy belső terheléselosztó valósítható meg.

Az alábbi ábra egy elosztott terhelésű végpontot mutat be a webes forgalomhoz, amely a nyilvános és a 80-as TCP-porton három virtuális gép között van megosztva. Ez a három virtuális gép egy elosztott terhelésű készlet részeit képezi.

![Nyilvános terheléselosztó – példa](./media/load-balancer-overview/load-balancer.png)

*Ábra: webes forgalom kiegyensúlyozása nyilvános terheléselosztó használatával*

Az internetes ügyfelek a 80-as TCP-porton lévő webalkalmazás nyilvános IP-címére küldenek webszolgáltatási kéréseket. Azure Load Balancer elosztja a kérelmeket a három virtuális gép között a terheléselosztási készletben. További információ a terheléselosztó algoritmusokról: [Load Balancer – fogalmak](concepts.md).

A Azure Load Balancer alapértelmezés szerint több virtuálisgép-példány között egyenlően osztja el a hálózati forgalmat. A munkamenet-affinitást is beállíthatja. További információ: [Azure Load Balancer elosztási módjának konfigurálása](load-balancer-distribution-mode.md).

## <a name="internal-load-balancer"></a><a name = "internalloadbalancer"></a>Belső terheléselosztó

A belső terheléselosztó a virtuális hálózaton belüli erőforrásokra osztja el a forgalmat. Az Azure korlátozza a terheléselosztásban lévő virtuális hálózatok előtér-IP-címeinek elérését. 

Az előtér-IP-címeket és a virtuális hálózatokat a rendszer soha nem teszi elérhetővé közvetlenül az internetes végpontok számára. A belső üzletági alkalmazások az Azure-ban futnak, és csak az Azure-ból vagy a helyszíni erőforrásokból érhetők el.

A belső terheléselosztó a következő típusú terheléselosztást teszi lehetővé:

* **Virtuális hálózaton belül**: terheléselosztás a virtuális hálózatban lévő virtuális gépekről az azonos virtuális hálózatban lévő virtuális gépek készletére.
* **Létesítmények közötti virtuális hálózat esetén**: terheléselosztás a helyszíni számítógépekről az azonos virtuális hálózatban lévő virtuális gépekre.
* **Többrétegű alkalmazások esetén**: terheléselosztás az internetre irányuló, többrétegű alkalmazásokhoz, ahol a háttérbeli rétegek nem internetre néznek. A háttérrendszer a forgalmi terheléselosztást igényli az internetre irányuló rétegben. Lásd a következő ábrát.
* **Üzletági alkalmazásoknál**: Terheléselosztás üzletági alkalmazásokhoz, amelyek további terheléselosztó hardverek vagy szoftverek nélkül üzemelnek az Azure-ban. Ez a forgatókönyv olyan helyszíni kiszolgálókat tartalmaz, amelyek azon számítógépek készletében találhatók, amelyeknek a forgalma elosztott.

![Belső Load Balancer-példa](./media/load-balancer-overview/load-balancer.png)

*Ábra: többrétegű alkalmazások terheléselosztása nyilvános és belső terheléselosztó használatával*

## <a name="load-balancer-sku-comparison"></a><a name="skus"></a> Load Balancer-termékváltozatok összehasonlítása

A Load Balancer az alapszintű és a standard SKU-t is támogatja. Ezek az SKU-ket a forgatókönyvek skálázása, a funkciók és a díjszabás különbözik. Az alapszintű Load balancerrel lehetséges forgatókönyvek a standard Load Balancer használatával hozhatók létre.

A különbségek összehasonlításához és megértéséhez lásd az alábbi táblázatot. További információ: az [Azure standard Load Balancer áttekintése](load-balancer-standard-overview.md).

>[!NOTE]
> A Microsoft a standard Load balancert javasolja.
Az önálló virtuális gépeket, a rendelkezésre állási csoportokat és a Virtual Machine Scale-készleteket csak egy termékváltozathoz csatlakoztathatja, sosem kettőhöz egyszerre. A terheléselosztó és a nyilvános IP-cím SKU egyeznie kell, ha nyilvános IP-címekkel használja őket. A Load Balancer és a nyilvános IP-címek nem változtathatók meg.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

További információ: [Load Balancer korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). A Standard Load Balancerről további részleteket az [áttekintés](load-balancer-standard-overview.md), a [díjszabás](https://aka.ms/lbpricing) és az [SLA](https://aka.ms/lbsla) szakaszban talál.

## <a name="next-steps"></a>További lépések

- A Load Balancer használatának első lépéseiért tekintse meg [a nyilvános standard Load Balancer létrehozását](quickstart-load-balancer-standard-public-portal.md) ismertető témakört.
- Tudnivalók a [standard Load Balancer és a Availability Zones](load-balancer-standard-availability-zones.md)használatáról.
- Tudnivalók az [állapot](load-balancer-custom-probe-overview.md)-mintavételekről.
- Tudnivalók a [Kimenő kapcsolatok Load Balancer](load-balancer-outbound-connections.md)használatáról.
- További tudnivalók a HA-portok terheléselosztási [szabályaival standard Load Balancer](load-balancer-ha-ports-overview.md).
- További információ a [hálózati biztonsági csoportokról](../virtual-network/security-overview.md).
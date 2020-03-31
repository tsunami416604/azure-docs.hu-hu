---
title: Azure VMware-megoldás a CloudSimple-től – Válasszon terheléselosztási megoldást a CloudSimple privát felhőihez
description: Bemutatja, hogy milyen terheléselosztási lehetőségek központi döngesztése egy alkalmazást magánfelhőben
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c98b699b1d3aba15ce69c519d35d7ce3e90d123
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014878"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Válasszon terheléselosztási megoldást a CloudSimple privát felhőihez

Amikor egy alkalmazást egy CloudSimple private cloud ban telepít, számos lehetőség közül választhat a terheléselosztáshoz.

Választhat egy virtuális vagy szoftveralapú terheléselosztót a CloudSimple magánfelhőben, vagy akár azure L7-es terheléselosztót is használhat az Azure-előfizetésében a CloudSimple private cloudban futó webes csomagvirtuális gépek frontális működtetéséhez. Itt felsorolunk néhány lehetőséget:

## <a name="virtual-load-balancers"></a>Virtuális terheléselosztók

Virtuális terheléselosztó készülékeket a VMware-környezetben a vCenter-felületen keresztül telepíthet, és konfigurálhatja őket az alkalmazásforgalom előtér-előtérre.

Néhány népszerű gyártók: NginX: http://nginx.org/en/docs/http/load_balancing.html F5- BigIP https://www.f5.com/products/big-ip-services/virtual-editions - Traffic Manager: Citrix ADC:https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Az Azure L7 terheléselosztó

Ha az Azure Application Gateway-t L7-es terheléselosztóként használja a magánfelhőben futó alkalmazáshoz, nem kell kezelnie a terheléselosztó szoftvert. A terheléselosztó szoftvert az Azure kezeli. A privát felhőben lévő összes webes csomag virtuális gépe magáncélú IP-címeket használ, és a nevek feloldásához nincs szükség további NAT-szabályokra vagy nyilvános IP-címekre. A webes szintű virtuális gépek privát, alacsony késésű, nagy sávszélességű kapcsolaton keresztül kommunikálnak az Azure Application Gateway-rel.

Ha többet szeretne megtudni arról, hogyan konfigurálhatja ezt a megoldást, olvassa el a megoldás útmutató az Azure Application Gateway használata l7-es terheléselosztóként című útmutatót.

## <a name="azure-internal-load-balancer"></a>Az Azure belső terheléselosztója

Ha úgy dönt, hogy az alkalmazást egy hibrid környezetben futtatja, ahol a webes előtér-réteg egy Azure-beli virtuális hálózaton belül fut az Azure-előfizetésben, és az alkalmazás DB-rétege vmware virtuális gépeken fut a CloudSimple private cloudban, használhatja az Azure belső terhelését kiegyensúlyozó (L4 terheléselosztó) előtt a DB tier virtuális gépek forgalomkezelés.

További információ: Azure [Belső terheléselosztó](../load-balancer/concepts-limitations.md#internalloadbalancer) dokumentáció.

## <a name="global-server-load-balancer"></a>Globális kiszolgálóterhelés-elosztó

Ha DNS-alapú terheléselosztót keres, akkor használhatja az Azure Marketplace-en elérhető harmadik féltől származó megoldásokat, vagy a natív Azure-megoldással.

Az Azure Traffic Manager egy DNS-alapú terheléselosztó, amely lehetővé teszi a forgalom optimális elosztását a szolgáltatások között a globális Azure-régiókban és a helyszínen, miközben magas rendelkezésre állást és válaszidőt biztosít. További információ: Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) dokumentáció.

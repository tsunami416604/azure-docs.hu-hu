---
title: Azure VMware-megoldások (AVS) – Válasszon egy terheléselosztási megoldást az AVS privát Felhőkhöz
description: Az alkalmazás egy AVS Private-felhőben való üzembe helyezésének terheléselosztási beállításait ismerteti
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 405bc9d95b8d82e2181e2fb828d6bcc00c8c4639
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014878"
---
# <a name="choose-a-load-balancing-solution-for-avs-private-clouds"></a>Terheléselosztási megoldás kiválasztása az AVS privát Felhőkhöz

Egy alkalmazás egy AVS Private-felhőben való telepítésekor a terheléselosztás számos lehetőség közül választhat.

Az AVS Private Cloud-ban virtuális vagy szoftveres terheléselosztó közül választhat, vagy akár az Azure-előfizetésben futtatott Azure L7 Load balancert is használhatja az AVS Private Cloud-on futó webes rétegbeli virtuális gépek előfizetéséhez. Itt felsorolunk néhány lehetőséget:

## <a name="virtual-load-balancers"></a>Virtuális Load Balancer

A vCenter felületen keresztül telepítheti a virtuális Load Balancer-berendezéseket a VMware-környezetbe, és beállíthatja őket az alkalmazás forgalmának befejezéséhez.

Néhány népszerű gyártó: NginX: http://nginx.org/en/docs/http/load_balancing.html F5-BigIP-Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC: https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Azure L7 Load Balancer

Ha az Azure Application Gatewayt használja L7 terheléselosztóként az alkalmazáshoz, amely egy AVS Private-felhőben fut, nem kell kezelnie a terheléselosztó szoftverét. A terheléselosztó szoftverét az Azure felügyeli. Az AVS Private-felhőben lévő összes webszintű virtuális gép magánhálózati IP-címeket használ, és nincs szükség további NAT-szabályokra vagy nyilvános IP-címekre a nevek feloldásához. A webes rétegbeli virtuális gépek privát, kis késleltetésű, nagy sávszélességű kapcsolaton keresztül kommunikálnak az Azure Application Gateway.

Ha többet szeretne megtudni a megoldás konfigurálásáról, tekintse meg a megoldási útmutatót az Azure Application Gateway L7 Load Balancer használatával történő használatáról.

## <a name="azure-internal-load-balancer"></a>Azure belső terheléselosztó

Ha úgy dönt, hogy az alkalmazást egy hibrid telepítésben futtatja, ahol a webes előtér-réteg az Azure-előfizetésben található Azure-vNet belül fut, és az alkalmazás adatbázis-szintje VMware virtuális gépeken fut az AVS Private Cloud szolgáltatásban, használhatja az Azure belső Load balancert (L 4 Load Balancer – a forgalom kezeléséhez használt adatbázis-szintű virtuális gépek előtt.

További információt az Azure [belső Load Balancer](../load-balancer/concepts-limitations.md#internalloadbalancer) dokumentációjában talál.

## <a name="global-server-load-balancer"></a>Globális kiszolgáló Load Balancer

Ha DNS-alapú Load balancert keres, akkor használhatja az Azure Marketplace-en elérhető, harmadik féltől származó megoldásokat vagy a natív Azure-megoldást is.

Az Azure Traffic Manager egy DNS-alapú forgalom-terheléselosztó, amely lehetővé teszi a forgalom optimális elosztását a globális Azure-régiók és a helyszíni szolgáltatások között, miközben magas rendelkezésre állást és válaszadást biztosít. További információt az Azure [Traffic Manager](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) dokumentációjában talál.

---
title: Azure-szolgáltatások a virtuális hálózati |} Microsoft Docs
description: További tudnivalók a erőforrásokat üzembe helyezi a virtuális hálózatba előnyeit. A virtuális hálózatokon, erőforrások kommunikálhatnak egymással, és a helyszíni erőforrások, az interneten áthaladó forgalom nélkül.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: ecfe3fb6db6b0fb0561e31b3c8aa70b74785b807
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="virtual-network-integration-for-azure-services"></a>Virtuális hálózati integráció az Azure-szolgáltatások

Integrálása az Azure-szolgáltatások egy Azure virtuális hálózatra lehetővé teszi, hogy a szolgáltatás része a virtuális hálózatnak példányokból privát hozzáférést.

Azure-szolgáltatásokkal integrálható a virtuális hálózat, a következő beállításokkal:
- Közvetlenül a virtuális hálózatba a szolgáltatás dedikált példányának telepítését. Ezek a szolgáltatások dedikált példányát közvetlenül elérhetők a virtuális hálózaton belül, és a helyszíni hálózatokhoz.
- A szolgáltatás Szolgáltatásvégpontok keresztül virtuális hálózat kiterjesztésével. Szolgáltatás végpontokkal szolgáltatás erőforrások védeni a virtuális hálózathoz.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Azure-szolgáltatások üzembe helyezés virtuális hálózatok

Az interneten keresztül nyilvános IP-címeket tud kommunikálni az Azure-erőforrások. Az Azure-szolgáltatások telepítésekor a [virtuális hálózati](virtual-networks-overview.md), kommunikálhat a service erőforrások közvetlenül a Microsoftnak, magánhálózati IP-címek használatával.

![Része virtuális hálózatnak szolgáltatások](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

A virtuális hálózaton belül szolgáltatások telepítése a következő lehetőségeket biztosítja:

- A virtuális hálózaton lévő erőforrások kommunikálhatnak egymással közvetlenül a Microsoftnak, magánhálózati IP-címek használatával. Példa, közvetlenül az adatok átvitele HDInsight és az SQL Server rendszert futtató virtuális gépen, a virtuális hálózat között.
- A helyszíni erőforrásokat érhetik el a virtuális hálózat privát IP-címek keresztül erőforrásokat egy [pont-pont virtuális Magánhálózati (VPN-átjáró)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) vagy [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuális hálózatok lehetnek [társviszonyban](virtual-network-peering-overview.md) ahhoz, hogy az erőforrások a virtuális hálózatok kommunikálnak egymással, privát IP-címek használata.
- A szolgáltatáspéldány virtuális hálózatban teljesen kezeli az Azure szolgáltatás, figyelheti a példányok állapotát, és adja meg a szükséges méretezési terhelés alapján.
- Szolgáltatáspéldány virtuális hálózatban egy dedikált alhálózatba vannak telepítve. Bejövő és kimenő hálózati hozzáférési keresztül kell megnyitni [hálózati biztonsági csoportok](security-overview.md#network-security-groups) az alhálózat, a szolgáltatás által biztosított útmutatásának.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Virtuális hálózatba telepített szolgáltatások

Minden szolgáltatás közvetlenül helyezett virtuális hálózati rendelkezik konkrét követelmények útválasztást és a típusú forgalom,-alhálózatok engedélyezni kell. További információkért lásd: 
 
- Virtuális gépek: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [A Service fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Virtuálisgép-méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Az Alkalmazásátjáró (belső)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Az Azure Kubernetes szolgáltatás (AKS)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Az Azure tároló szolgáltatás vezérlőprogramja](https://github.com/Azure/acs-engine) együtt az Azure virtuális hálózat CNI [beépülő modul](https://github.com/Azure/acs-engine/tree/master/examples/vnet)
- [Az Azure Active Directory tartományi szolgáltatások](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json): virtuális hálózat (klasszikus) csak
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [A felhőalapú szolgáltatások](https://msdn.microsoft.com/library/azure/jj156091): virtuális hálózat (klasszikus) csak

Telepíthet egy [belső Azure terheléselosztó](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) betöltése elosztása a fenti listán szereplő erőforrások közül. Bizonyos esetekben a a szolgáltatás automatikusan létrehozza, és telepíti a terheléselosztót, erőforrás létrehozásakor.

## <a name="service-endpoints-for-azure-services"></a>Az Azure szolgáltatások végpontok

Egyes Azure-szolgáltatások nem állítható rendszerbe a virtuális hálózatokon. Korlátozhatja hozzáférés a szolgáltatás erőforrások csak adott virtuális hálózati alhálózathoz, ha úgy dönt, azáltal, hogy egy virtuális hálózati végpontot. További információ [virtuális hálózati Szolgáltatásvégpontok](virtual-network-service-endpoints-overview.md).

Jelenleg a végpontok támogatott a következő szolgáltatásokhoz: 
- **Az Azure Storage**: [virtuális hálózatok fiókok Azure Storage biztonságossá tétele](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- **Az Azure SQL Database**: [biztonságossá tétele az Azure SQL Database virtuális hálózatok](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Virtuális hálózati integráció több Azure szolgáltatásban

A virtuális hálózat és alhálózat biztonságos kritikus szolgáltatás erőforrásokat egy alhálózatba telepítheti is az Azure-szolgáltatások. Például HDInsight üzembe helyezés a virtuális hálózat és a HDInsight alhálózathoz tárfiók biztonságos.






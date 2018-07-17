---
title: Virtuális hálózatot az Azure-szolgáltatások |} A Microsoft Docs
description: Ismerje meg a virtuális hálózatban üzembe helyezni erőforrásokat előnyeit. A virtuális hálózatok erőforrások kommunikálhatnak egymással, és a helyszíni erőforrásokat, anélkül, hogy az interneten áthaladó forgalom lesz.
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
ms.openlocfilehash: c92a986d06deb9f7de10f0682fe46804e6ebb6e7
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069876"
---
# <a name="virtual-network-integration-for-azure-services"></a>Virtuális hálózat integrációja Azure-szolgáltatások

Integrálása az Azure-szolgáltatások az Azure virtuális hálózat lehetővé teszi, hogy a virtuális hálózaton üzembe helyezett szolgáltatás példányai a privát hozzáférést.

Azure-szolgáltatások is integrálhatja a virtuális hálózat a következő beállításokkal:
- Közvetlenül üzembe helyezése egy virtuális hálózatban a szolgáltatás dedikált példányát. A dedikált példányok ezen szolgáltatások közvetlenül a Microsoftnak érhetők el a virtuális hálózaton belül, és a helyszíni hálózatokról.
- Egy virtuális hálózatot a szolgáltatás a szolgáltatásvégpontokon keresztül az kiterjesztésével. A Szolgáltatásvégpontok biztosítani, hogy a virtuális hálózat egyes szolgáltatások erőforrásait.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Azure-szolgáltatások virtuális hálózatokon üzembe helyezhető

Az interneten nyilvános IP-címekkel kommunikálhatnak a legtöbb Azure-erőforrásokkal. Azure-szolgáltatások üzembe helyezésekor a [virtuális hálózat](virtual-networks-overview.md), kommunikálhat a szolgáltatási erőforrások közvetlenül, magánhálózati IP-címeken keresztül.

![Egy virtuális hálózaton telepített szolgáltatásokat](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Egy virtuális hálózaton belüli szolgáltatások telepítése az alábbi képességeket biztosítja:

- A virtuális hálózatban lévő erőforrások is kommunikálnak egymással közvetlenül, magánhálózati IP-címeken keresztül. Például közvetlenül adatátvitel a HDInsight és a egy virtuális gépen a virtuális hálózatban futó SQL Server között.
- Helyszíni erőforrások keresztül magánhálózati IP-címek használata virtuális hálózatban lévő erőforrások eléréséhez egy [helyek közötti virtuális Magánhálózati (VPN-átjáró)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) vagy [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuális hálózatok lehetnek [társviszonyba](virtual-network-peering-overview.md) lehetővé teszik az erőforrások a virtuális hálózatok egymással kommunikálni, privát IP-címek.
- Szolgáltatáspéldányok a virtuális hálózatban az Azure-szolgáltatás, figyelheti a példányok állapotát, és adja meg a szükséges méretezés, a betöltés teljes körűen felügyeli.
- Szolgáltatáspéldányok a rendszer üzembe helyezi a kijelölt alhálózatot a virtuális hálózatban. Bejövő és kimenő hálózati hozzáférés keresztül kell megnyitni [hálózati biztonsági csoportok](security-overview.md#network-security-groups) az alhálózat, a szolgáltatások által biztosított útmutatásának.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Egy virtuális hálózatban a telepített szolgáltatások

Mindegyikük közvetlenül telepített virtuális hálózatban az Útválasztás konkrét követelmények, és engedélyezni kell, és alhálózatok kimenő forgalom típusú rendelkezik. További információkért lásd: 
 
- Virtuális gépek: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [A Service fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [A Virtual machine scale sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Az Application Gateway (belső)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Az Azure Kubernetes Service (AKS)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Az Azure Container Service-motor](https://github.com/Azure/acs-engine) együtt az Azure virtuális hálózat CNI [beépülő modul](https://github.com/Azure/acs-engine/tree/master/examples/vnet)
- [Az Azure Active Directory Domain Servicesben](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)
- [Az Azure SQL Database felügyelt példány](../sql-database/sql-database-managed-instance-vnet-configuration.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Felhőszolgáltatások](https://msdn.microsoft.com/library/azure/jj156091): virtuális hálózat (klasszikus) csak

Telepíthet egy [belső Azure load balancer](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) betöltése elosztása a fenti listán szereplő erőforrásokra. Bizonyos esetekben a szolgáltatás automatikusan létrehozza és telepíti a terheléselosztó erőforrás létrehozásakor.

## <a name="service-endpoints-for-azure-services"></a>A Szolgáltatásvégpontok az Azure-szolgáltatásokhoz

Bizonyos Azure-szolgáltatások nem állítható rendszerbe a virtuális hálózatok. Egyes, a szolgáltatási erőforrások csak adott virtuális hálózati alhálózatokkal, hozzáférés korlátozhatja, ha úgy dönt, a virtuális hálózati szolgáltatásvégpont engedélyezése. Tudjon meg többet [virtuális hálózati Szolgáltatásvégpontok](virtual-network-service-endpoints-overview.md), és a szolgáltatásokat, amelyek végpontok esetén is engedélyezhető.

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Virtuális hálózati integráció több Azure-szolgáltatás

Az Azure-szolgáltatások üzembe helyezhető egy alhálózatnak egy virtuális hálózat és alhálózat biztonságos kritikus szolgáltatások erőforrásait. Például a virtuális hálózat üzembe helyezése a HDInsight, és a egy tárfiókot, a HDInsight-alhálózathoz.






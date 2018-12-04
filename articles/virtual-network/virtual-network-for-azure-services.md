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
ms.openlocfilehash: c580066032bf6a16dfbde876ec9843d8900d0057
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52844413"
---
# <a name="virtual-network-integration-for-azure-services"></a>Virtuális hálózat integrációja Azure-szolgáltatások

A szolgáltatás integrálása az Azure virtuális hálózat Azure-szolgáltatások lehetővé privát hozzáférést a virtuális gépek vagy a számítási erőforrások a virtuális hálózatban.
Azure-szolgáltatások integrálva a virtuális hálózaton a következő beállításokkal: közvetlenül üzembe helyezése egy virtuális hálózatban a szolgáltatás dedikált példányát. A szolgáltatások majd nem nyilvánosan elérhetők a virtuális hálózaton belül, és a helyszíni hálózatok.
Egy virtuális hálózatot a szolgáltatás a szolgáltatásvégpontokon keresztül az kiterjesztésével. A Szolgáltatásvégpontok biztosítani, hogy a virtuális hálózat egyes szolgáltatások erőforrásait.

Integráció több Azure-szolgáltatások a virtuális hálózathoz, legalább egy, a fenti minták kombinálhatók. Például a virtuális hálózat üzembe helyezése a HDInsight, és a egy tárfiókot, a HDInsight-alhálózatnak a szolgáltatásvégpontokon keresztül.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Azure-szolgáltatások virtuális hálózatokon üzembe helyezhető

Ha a dedikált Azure-szolgáltatások üzembe helyezését egy [virtuális hálózat](virtual-networks-overview.md), kommunikálhat a szolgáltatási erőforrások közvetlenül, magánhálózati IP-címeken keresztül.

![Egy virtuális hálózaton telepített szolgáltatásokat](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Egy virtuális hálózaton belüli szolgáltatások telepítése az alábbi képességeket biztosítja:

- A virtuális hálózatban lévő erőforrások is kommunikálnak egymással közvetlenül, magánhálózati IP-címeken keresztül. Például közvetlenül adatátvitel a HDInsight és a egy virtuális gépen a virtuális hálózatban futó SQL Server között.
- Helyszíni erőforrások keresztül magánhálózati IP-címek használata virtuális hálózatban lévő erőforrások eléréséhez egy [helyek közötti virtuális Magánhálózati (VPN-átjáró)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) vagy [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuális hálózatok lehetnek [társviszonyba](virtual-network-peering-overview.md) lehetővé teszik az erőforrások a virtuális hálózatok egymással kommunikálni, privát IP-címek.
- Szolgáltatáspéldányok a virtuális hálózatban az Azure-szolgáltatás, figyelheti a példányok állapotát, és adja meg a szükséges méretezés, a betöltés teljes körűen felügyeli.
- Szolgáltatás-példányok üzembe helyezése egy virtuális hálózat egy alhálózatában. Bejövő és kimenő hálózati hozzáférés keresztül kell megnyitni [hálózati biztonsági csoportok](security-overview.md#network-security-groups) az alhálózat, a szolgáltatások által biztosított útmutatásának.
- Szükség esetén szükség lehet a szolgáltatások egy [alhálózati delegált](virtual-network-manage-subnet.md#add-a-subnet) explicit azonosítóként, hogy egy alhálózat üzemeltethető egy adott szolgáltatáshoz. Alhálózat delegálás révén az alhálózat szolgáltatásspecifikus-erőforrások létrehozása a szolgáltatáshoz a kifejezett engedélyek.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Egy virtuális hálózatban a telepített szolgáltatások

|Kategória|Szolgáltatás|
|-|-|
| Compute | Virtuális gépek: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[A Virtual machine scale sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloud Service](https://msdn.microsoft.com/library/azure/jj156091): virtuális hálózat (klasszikus) csak<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)  |
| Network (Hálózat) | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Az Azure-tűzfal](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Hálózati virtuális készülékeket](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) 
|Adatok|[RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Az Azure SQL Database felügyelt példány](../sql-database/sql-database-managed-instance-vnet-configuration.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
Elemzés | [Az Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
| Identitás | [Az Azure Active Directory Domain Servicesben](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
| Containers | [Az Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Az Azure Container instance-példány (aci Szolgáltatásban)](https://www.aka.ms/acivnet)<br/>[Az Azure Container Service-motor](https://github.com/Azure/acs-engine) az Azure virtuális hálózat CNI [beépülő modul](https://github.com/Azure/acs-engine/tree/master/examples/vnet)||
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service-környezet](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>
<br/>


## <a name="service-endpoints-for-azure-services"></a>A Szolgáltatásvégpontok az Azure-szolgáltatásokhoz

Bizonyos Azure-szolgáltatások nem állítható rendszerbe a virtuális hálózatok. Egyes, a szolgáltatási erőforrások csak adott virtuális hálózati alhálózatokkal, hozzáférés korlátozhatja, ha úgy dönt, a virtuális hálózati szolgáltatásvégpont engedélyezése.  Tudjon meg többet [virtuális hálózati Szolgáltatásvégpontok](virtual-network-service-endpoints-overview.md), és a szolgáltatásokat, amelyek végpontok esetén is engedélyezhető.
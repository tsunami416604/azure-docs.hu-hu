---
title: Virtuális hálózat az Azure-szolgáltatásokhoz
titlesuffix: Azure Virtual Network
description: Ismerje meg az erőforrások virtuális hálózatba való üzembe helyezésének előnyeit. A virtuális hálózatok erőforrásai kommunikálhatnak egymással és a helyszíni erőforrásokkal anélkül, hogy az interneten áthaladó forgalom áthaladna.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 24bcc7e698527cd39958c53b48a0b36404c36bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279662"
---
# <a name="virtual-network-integration-for-azure-services"></a>Virtuális hálózat integrációja Azure-szolgáltatásokhoz

Az Azure-szolgáltatások integrálása egy Azure virtuális hálózatba lehetővé teszi a szolgáltatás hoz való privát hozzáférést a virtuális gépekről vagy a virtuális hálózat számítási erőforrásairól.
Az Azure-szolgáltatásokat a következő lehetőségekkel integrálhatja a virtuális hálózatba:
- A szolgáltatás dedikált példányainak üzembe helyezése egy virtuális hálózatba. A szolgáltatások ezután privát módon érhetők el a virtuális hálózaton belül és a helyszíni hálózatokon.
- Privát [kapcsolat](../private-link/private-link-overview.md) használata a szolgáltatás egy adott példányának privát eléréséhez a virtuális hálózatról és a helyszíni hálózatokból.

A szolgáltatás nyilvános végpontok használatával is elérhető, ha egy virtuális hálózatot terjeszt a szolgáltatásvégpontokon keresztül a [szolgáltatásra.](virtual-network-service-endpoints-overview.md) A szolgáltatásvégpontok lehetővé teszik a szolgáltatáserőforrások virtuális hálózatra való védelmét.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Azure-szolgáltatások üzembe helyezése virtuális hálózatokban

Ha dedikált Azure-szolgáltatásokat telepít egy [virtuális hálózatban,](virtual-networks-overview.md)privát IP-címeken keresztül privát IP-címeken keresztül kommunikálhat a szolgáltatás erőforrásaival.

![Virtuális hálózatban telepített szolgáltatások](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

A virtuális hálózaton belüli szolgáltatások telepítése a következő lehetőségeket biztosítja:

- A virtuális hálózaton belüli erőforrások privát IP-címeken keresztül kommunikálhatnak egymással. Például az adatok közvetlen átvitele a HDInsight és a virtuális gépen futó SQL Server között a virtuális hálózatban.
- A helyszíni erőforrások a virtuális hálózat erőforrásaihoz férnek hozzá a helyek közötti [VPN-en (VPN-átjáró)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) vagy [az ExpressRoute-on](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)keresztül.
- A virtuális hálózatok [társviszonyba vihetők,](virtual-network-peering-overview.md) hogy a virtuális hálózatok erőforrásai privát IP-címeket használva kommunikálhassanak egymással.
- A virtuális hálózat szolgáltatáspéldányait általában teljes körűen kezeli az Azure szolgáltatás. Ez magában foglalja az erőforrások állapotának figyelése és a méretezés terheléssel.
- A szolgáltatáspéldányok egy virtuális hálózat alhálózatába vannak telepítve. Az alhálózat bejövő és kimenő hálózati hozzáférését a szolgáltatás által biztosított útmutatás szerint [hálózati biztonsági csoportokon](security-overview.md#network-security-groups)keresztül kell megnyitni.
- Bizonyos szolgáltatások is korlátozásokat írnak elő az alhálózat, amelyben üzembe helyezett, korlátozza a házirendek, útvonalak vagy virtuális gépek és szolgáltatás erőforrások kombinálása ugyanazon alhálózaton belül. Ellenőrizze az egyes szolgáltatások az adott korlátozásokat, mivel azok idővel változhatnak. Ilyen szolgáltatások például az Azure NetApp-fájlok, dedikált HSM, Azure Container Instances, App Service. 
- Szükség esetén a szolgáltatásokhoz explicit azonosítóként [delegált alhálózatra](virtual-network-manage-subnet.md#add-a-subnet) lehet szükség, amelyet egy alhálózat egy adott szolgáltatás üzemeltethet. Delegálásával a szolgáltatások explicit engedélyeket kapnak a delegált alhálózat szolgáltatásspecifikus erőforrásainak létrehozásához.
- Tekintsen meg egy példát egy REST API-válaszra egy [delegált alhálózattal rendelkező virtuális hálózaton.](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet) A delegált alhálózati modellt használó szolgáltatások átfogó listája az [Elérhető delegálások](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API-n keresztül szerezhető be.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>A virtuális hálózatokban üzembe helyezhető szolgáltatások

|Kategória|Szolgáltatás| Dedikált¹ alhálózat
|-|-|-|
| Compute | Virtuális gépek: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Virtuálisgép-méretezési készletek](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Felhőszolgáltatás](https://msdn.microsoft.com/library/azure/jj156091): Csak virtuális hálózat (klasszikus)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nem <br/> Nem <br/> Nem <br/> No²
| Network (Hálózat) | [Alkalmazásátjáró - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN-átjáró](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Hálózati virtuális készülékek](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Igen <br/> Igen <br/> Igen <br/> Nem
|Adatok|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Felügyelt Azure SQL Database-példány](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Igen <br/> Igen <br/> 
|Elemzés | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| Identitás | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nem <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instance (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service Engine](https://github.com/Azure/acs-engine) az Azure virtuális hálózati CNI [beépülő modullal](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|No²<br/> Igen <br/><br/> Nem
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service-környezet](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Igen <br/> Igen <br/> Igen
| Szálló | [Azure Dedicated HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Igen <br/> Igen <br/>
| | |

¹ A "dedikált" azt jelenti, hogy csak szolgáltatásspecifikus erőforrások telepíthetők ebben az alhálózaton, és nem kombinálhatók az ügyfél virtuális gépekkel/vMSS-ekkel. <br/> ² Ajánlott, ha ezeket a szolgáltatásokat egy dedikált alhálózatban, de nem kötelező követelmény a szolgáltatás által előírt.

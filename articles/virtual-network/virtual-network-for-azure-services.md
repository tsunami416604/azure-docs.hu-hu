---
title: Virtuális hálózat az Azure-szolgáltatásokhoz
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogy milyen előnyökkel jár az erőforrások virtuális hálózatra történő telepítése. A virtuális hálózatokban lévő erőforrások kommunikálhatnak egymással és helyszíni erőforrásokkal anélkül, hogy a forgalom az interneten halad át.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279662"
---
# <a name="virtual-network-integration-for-azure-services"></a>Virtuális hálózati integráció az Azure-szolgáltatásokhoz

Az Azure-szolgáltatások Azure-beli virtuális hálózatba való integrálása lehetővé teszi a szolgáltatáshoz való privát hozzáférést a virtuális gépekről vagy a virtuális hálózatban lévő számítási erőforrásokról.
A virtuális hálózatban az alábbi beállításokkal integrálhatja az Azure-szolgáltatásokat:
- A szolgáltatás dedikált példányainak üzembe helyezése virtuális hálózatban. A szolgáltatások ezután a virtuális hálózaton és a helyszíni hálózatokon is elérhetők.
- [Privát hivatkozás](../private-link/private-link-overview.md) használatával a szolgáltatás egy adott példányát a virtuális hálózatról és a helyszíni hálózatokról is elérheti.

A szolgáltatást nyilvános végpontok használatával is elérheti, ha a virtuális hálózatot kiterjeszti a szolgáltatásra a szolgáltatási [végpontokon](virtual-network-service-endpoints-overview.md)keresztül. A szolgáltatási végpontok lehetővé teszik a szolgáltatási erőforrások védelmét a virtuális hálózattal.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Azure-szolgáltatások üzembe helyezése virtuális hálózatokban

Amikor dedikált Azure-szolgáltatásokat helyez üzembe egy [virtuális hálózaton](virtual-networks-overview.md), privát IP-címeken keresztül kommunikálhat a szolgáltatási erőforrásokkal.

![Virtuális hálózatban üzembe helyezett szolgáltatások](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

A szolgáltatások virtuális hálózaton belüli üzembe helyezése a következő képességeket biztosítja:

- A virtuális hálózaton belül található erőforrások privát IP-címeken keresztül kommunikálhatnak egymással. Például közvetlenül az HDInsight és a virtuális gépen futó SQL Server közötti adatátvitel a virtuális hálózaton.
- A helyszíni erőforrások a virtuális hálózatban lévő erőforrásokhoz magánhálózati IP-címek használatával férhetnek hozzá a [helyek közötti VPN (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) vagy a [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)számára.
- A virtuális hálózatok lehetővé teszik [, hogy a](virtual-network-peering-overview.md) virtuális hálózatok erőforrásai a magánhálózati IP-címek használatával kommunikáljanak egymással.
- A virtuális hálózatban lévő szolgáltatási példányokat általában teljes mértékben az Azure-szolgáltatás kezeli. Ide tartozik az erőforrások állapotának figyelése és a terheléssel való skálázás.
- A szolgáltatási példányok egy virtuális hálózatban lévő alhálózatba vannak telepítve. Az alhálózat bejövő és kimenő hálózati hozzáférését [hálózati biztonsági csoportokon](security-overview.md#network-security-groups)keresztül kell megnyitni a szolgáltatás által biztosított útmutatás alapján.
- Bizonyos szolgáltatások korlátozásokat is bevezetnek az általuk üzembe helyezett alhálózatra, a házirendek alkalmazásának korlátozására, a virtuális gépek és a szolgáltatások erőforrásainak egyazon alhálózaton belüli kikapcsolására. Az egyes szolgáltatásokra vonatkozó korlátozásokat az idő múlásával változhat. Ilyen szolgáltatás például Azure NetApp Files, dedikált HSM, Azure Container Instances, App Service. 
- Opcionálisan előfordulhat, hogy a szolgáltatások [delegált alhálózatot](virtual-network-manage-subnet.md#add-a-subnet) igényelnek explicit azonosítóként, amelyet egy alhálózat egy adott szolgáltatás üzemeltetésére használhat. A delegálással a szolgáltatások explicit engedélyeket kapnak a szolgáltatásra jellemző erőforrások létrehozásához a delegált alhálózatban.
- Tekintse meg REST API választ egy [virtuális hálózaton delegált alhálózattal](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). A delegált alhálózati modellt használó szolgáltatások átfogó listája az [elérhető delegálások](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API-n keresztül szerezhető be.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Virtuális hálózatban üzembe helyezhető szolgáltatások

|Kategória|Szolgáltatás| Dedikált ¹ alhálózat
|-|-|-|
| Számítás | Virtual Machines: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Virtuálisgép-méretezési csoportok](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloud Service](https://msdn.microsoft.com/library/azure/jj156091): csak virtuális hálózat (klasszikus)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nem <br/> Nem <br/> Nem <br/> Nem ²
| Hálózat | [Application Gateway – WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Hálózati virtuális berendezések](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Igen <br/> Igen <br/> Igen <br/> Nem
|Adatok|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Felügyelt Azure SQL Database-példány](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Igen <br/> Igen <br/> 
|Elemzés | [Azure-HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nem ² <br/> Nem ² <br/> 
| Identitás | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nem <br/>
| Tárolók | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container instance (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service motor](https://github.com/Azure/acs-engine) az Azure Virtual Network CNI [beépülő modullal](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|Nem ²<br/> Igen <br/><br/> Nem
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Igen <br/> Igen <br/> Igen
| Házigazdája | [Azure dedikált HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Igen <br/> Igen <br/>
| | |

¹ a "dedikált" kifejezés azt jelenti, hogy csak az adott alhálózaton üzemelő szolgáltatás-erőforrások helyezhetők üzembe, és nem kombinálhatók az ügyfél virtuális géppel/VMSSs <br/> ² ajánlott eljárás arra, hogy ezeket a szolgáltatásokat dedikált alhálózatban lehessen használni, de a szolgáltatás nem kötelező követelménye.

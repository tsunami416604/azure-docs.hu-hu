---
title: Virtuális hálózat az Azure-szolgáltatásokhoz
titlesuffix: Azure Virtual Network
description: Ismerje meg a virtuális hálózatban üzembe helyezni erőforrásokat előnyeit. A virtuális hálózatok erőforrások kommunikálhatnak egymással, és a helyszíni erőforrásokat, anélkül, hogy az interneten áthaladó forgalom lesz.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop;kumud
ms.openlocfilehash: e5481b0e262021e28a398b72b5ad022673947609
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409508"
---
# <a name="virtual-network-integration-for-azure-services"></a>Virtuális hálózat integrációja Azure-szolgáltatások

A szolgáltatás integrálása az Azure virtuális hálózat Azure-szolgáltatások lehetővé privát hozzáférést a virtuális gépek vagy a számítási erőforrások a virtuális hálózatban.
Azure-szolgáltatások integrálva a virtuális hálózaton a következő beállításokkal:
- Üzembe helyezése egy virtuális hálózatban a szolgáltatás dedikált példányát. A szolgáltatások majd nem nyilvánosan elérhetők a virtuális hálózaton belül, és a helyszíni hálózatok.
- Kiterjeszti a virtuális hálózat a szolgáltatásba, a szolgáltatásvégpontokon keresztül. A Szolgáltatásvégpontok biztosítani, hogy a virtuális hálózat egyes szolgáltatások erőforrásait.

Integráció több Azure-szolgáltatások a virtuális hálózathoz, legalább egy, a fenti minták kombinálhatók. Például a virtuális hálózat üzembe helyezése a HDInsight, és a egy tárfiókot, a HDInsight-alhálózatnak a szolgáltatásvégpontokon keresztül.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Azure-szolgáltatások virtuális hálózatokon üzembe helyezhető

Ha a dedikált Azure-szolgáltatások üzembe helyezését egy [virtuális hálózat](virtual-networks-overview.md), kommunikálhat a szolgáltatási erőforrások közvetlenül, magánhálózati IP-címeken keresztül.

![Egy virtuális hálózaton telepített szolgáltatásokat](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Egy virtuális hálózaton belüli szolgáltatások telepítése az alábbi képességeket biztosítja:

- A virtuális hálózatban lévő erőforrások is kommunikálnak egymással közvetlenül, magánhálózati IP-címeken keresztül. Például közvetlenül adatátvitel a HDInsight és a egy virtuális gépen a virtuális hálózatban futó SQL Server között.
- Helyszíni erőforrások keresztül magánhálózati IP-címek használata virtuális hálózatban lévő erőforrások eléréséhez egy [helyek közötti virtuális Magánhálózati (VPN-átjáró)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) vagy [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuális hálózatok lehetnek [társviszonyba](virtual-network-peering-overview.md) lehetővé teszik az erőforrások a virtuális hálózatok egymással kommunikálni, privát IP-címek.
- A virtuális hálózati szolgáltatási példányai általában teljes mértékben az Azure-szolgáltatás kezeli. Ez magában foglalja az erőforrások állapotának figyelése és méretezése a terhelés.
- Szolgáltatás-példányok üzembe helyezése egy virtuális hálózat egy alhálózatában. Bejövő és kimenő hálózati hozzáférés az alhálózat keresztül kell megnyitni [hálózati biztonsági csoportok](security-overview.md#network-security-groups), a szolgáltatás által biztosított útmutatásának.
- Bizonyos is kivetett korlátozásokat az alhálózaton, melyekre telepítve vannak, korlátozza az alkalmazás házirendjeit, útvonalak és kombinálása a virtuális gépek és a szolgáltatási erőforrások ugyanabban az alhálózatban. Ellenőrizze és minden szolgáltatás az adott korlátozásokkal, idővel változhat. Az ilyen szolgáltatások például a Azure NetApp fájlok, a dedikált HSM-be, az Azure Container Instances, App Service-ben. 
- Szükség esetén szükség lehet a szolgáltatások egy [alhálózati delegált](virtual-network-manage-subnet.md#add-a-subnet) explicit azonosítóként, hogy egy alhálózat üzemeltethető egy adott szolgáltatáshoz. Szolgáltatások delegálása, csatlakozva a kifejezett engedélyek szolgáltatásspecifikus-erőforrások létrehozása a delegált alhálózatban.
- Tekintse meg példaként a REST API-válasz egy [virtuális alhálózattal rendelkező hálózatot, a meghatalmazott](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get_virtual_network_with_a_delegated_subnet). A delegált alhálózati modell által használt szolgáltatások átfogó listáját keresztül szerezhetők be a [elérhető delegálásokat](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API-t.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Egy virtuális hálózatban a telepített szolgáltatások

|Category|Szolgáltatás| Dedicated¹ alhálózat
|-|-|-|
| Compute | Virtuális gépek: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[A Virtual machine scale sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloud Service](https://msdn.microsoft.com/library/azure/jj156091): Virtuális hálózat (klasszikus) csak<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nem <br/> Nem <br/> Nem <br/> No²
| Network (Hálózat) | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Az Azure-tűzfal](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Hálózati virtuális berendezések](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Igen <br/> Igen <br/> Igen <br/> Nem
|Adatok|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Az Azure SQL Database felügyelt példány](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Igen <br/> Igen <br/> 
|Elemzés | [Az Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| Identitás | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nem <br/>
| Containers | [Az Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Az Azure Container instance-példány (aci Szolgáltatásban)](https://www.aka.ms/acivnet)<br/>[Az Azure Container Service-motor](https://github.com/Azure/acs-engine) az Azure virtuális hálózat CNI [beépülő modul](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|No²<br/> Igen <br/><br/> Nem
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service-környezet](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Igen <br/> Igen <br/> Igen
| Üzemeltetett | [Az Azure dedikált HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp-fájlok](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Igen <br/> Igen <br/>
| | |

¹ "Dedikált" azt jelenti, hogy csak a szolgáltatás adott erőforrásokat az alhálózat telepíthetők, és nem kombinálható ügyfél virtuális gép/VMSSs <br/> ² ajánlott, de nem egy kötelező a szolgáltatás által megszabott követelmény.


## <a name="service-endpoints-for-azure-services"></a>A Szolgáltatásvégpontok az Azure-szolgáltatásokhoz

Bizonyos Azure-szolgáltatások nem állítható rendszerbe a virtuális hálózatok. Egyes, a szolgáltatási erőforrások csak adott virtuális hálózati alhálózatokkal, hozzáférés korlátozhatja, ha úgy dönt, a virtuális hálózati szolgáltatásvégpont engedélyezése.  Tudjon meg többet [virtuális hálózati Szolgáltatásvégpontok](virtual-network-service-endpoints-overview.md), és a szolgáltatásokat, amelyek végpontok esetén is engedélyezhető.

---
title: Az Azure Application Gateway használata VMware virtuális gépekkel
description: Ez a témakör azt ismerteti, hogy az Azure-alkalmazásátjáró használatával hogyan kezelheti a VMware virtuális gépeken futó webkiszolgálók bejövő webes forgalmát, hogy megnyerje a CloudSimple private cloud környezetet
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015456"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Az Azure Application Gateway használata VMware virtuális gépekkel a CloudSimple private cloud környezetben

Az Azure Application Gateway segítségével kezelheti a bejövő webes forgalmat a Virtuálisgép-virtuális gépeken futó webkiszolgálók számára a CloudSimple private cloud környezetben.

Az Azure Application Gateway nyilvános és privát hibrid telepítésben való kihasználásával kezelheti az alkalmazások webes forgalmát, biztonságos előtér-feldolgozást biztosíthat, és a VMware-környezetben futó szolgáltatásaik ssl-feldolgozását is kiszervezheti. Az Azure Application Gateway a bejövő webes forgalmat a VMware-környezetekben található háttérkészlet-példányok felé irányítja a konfigurált szabályok és állapotmintak szerint.

Ez az Azure Application Gateway megoldás a következőket igényli:

* Azure-előfizetés.
* Hozzon létre és konfiguráljon egy Azure virtuális hálózatot, és egy alhálózatot a virtuális hálózaton belül.
* Hozzon létre és konfiguráljon NSG-szabályokat, és társviszonyt létesítsen a virtuális hálózat használatával ExpressRoute a CloudSimple private cloud használatával.
* Hozzon létre & konfigurálja a magánfelhőt.
* Hozzon létre & konfigurálja az Azure Application Gateway.Create & Configure your Azure Application Gateway.

## <a name="azure-application-gateway-deployment-scenario"></a>Az Azure Application Gateway telepítési forgatókönyve

Ebben a forgatókönyvben az Azure Application Gateway fut az Azure virtuális hálózatban. A virtuális hálózat expressroute-kapcsolaton keresztül csatlakozik a magánfelhőhöz. A magánfelhő összes alhálózata elérhető a virtuális hálózati alhálózatokról.

![Azure terheléselosztó az Azure virtuális hálózatában](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>A megoldás telepítése

A telepítési folyamat a következő feladatokból áll:

1. [Az előfeltételek teljesülésének ellenőrzése](#1-verify-prerequisites)
2. [Az Azure virtuális kapcsolatának csatlakoztatása a magánfelhőhöz](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Azure-alkalmazásátjáró üzembe helyezése](#3-deploy-an-azure-application-gateway)
4. [Webkiszolgálói virtuálisgép-készlet létrehozása és konfigurálása a magánfelhőben](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Előfeltételek ellenőrzése

Ellenőrizze, hogy ezek az előfeltételek teljesülnek-e:

* Már létre van hozva egy Azure Resource Manager és egy virtuális hálózat.
* Az Azure virtuális hálózatán belül már létre jön egy dedikált alhálózat (az Application Gateway számára).
* A CloudSimple privát felhő már létre.
* Nincs IP-ütközés a virtuális hálózat IP-alhálózatai és a magánfelhő alhálózatai között.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Csatlakoztassa az Azure virtuális hálózatát a magánfelhőhöz

Az Azure virtuális hálózatának a magánfelhőhöz való csatlakoztatásához kövesse ezt a folyamatot.

1. [A CloudSimple portálon másolja az ExpressRoute-társviszony-létesítési adatokat.](virtual-network-connection.md)

2. [Konfiguráljon egy virtuális hálózati átjárót az Azure virtuális hálózatához.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

3. [Kapcsolja össze a virtuális hálózatot a CloudSimple ExpressRoute-kapcsolattal.](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)

4. [A másolt társviszony-létesítési adatok segítségével kapcsolja össze a virtuális hálózatot az ExpressRoute-kapcsolattal.](virtual-network-connection.md)

## <a name="3-deploy-an-azure-application-gateway"></a>3. Azure-alkalmazásátjáró üzembe helyezése

Az ehhez részletes utasítások az [Azure Portalon útvonalalapú útválasztási szabályokkal rendelkező alkalmazásátjáró létrehozása című részben](../application-gateway/create-url-route-portal.md)találhatók. Az alábbiakban összefoglaljuk a szükséges lépéseket:

1. Hozzon létre egy virtuális hálózatot az előfizetésben és az erőforráscsoportban.
2. Hozzon létre egy alhálózatot (amelydedikált alhálózatként használható) a virtuális hálózaton belül.
3. Hozzon létre egy szabványos alkalmazásátjárót (a WAF szükség esetén engedélyezése): Az Azure Portal kezdőlapján kattintson az > **Erőforrás-hálózati** > **alkalmazásátjáró** elemre a lap bal felső részén. **Resource** Válassza ki a szabványos termékváltozatot és méretet, és adja meg az Azure-előfizetés, erőforráscsoport és helyadatok. Ha szükséges, hozzon létre egy új nyilvános IP-címet ehhez az alkalmazásátjáróhoz, és adja meg a részleteket a virtuális hálózatról és az alkalmazásátjáró dedikált alhálózatáról.
4. Adjon hozzá egy háttérkészletet virtuális gépekkel, és adja hozzá az alkalmazásátjáróhoz.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Webkiszolgáló virtuálisgép-készlet létrehozása és konfigurálása a magánfelhőben

A vCenterben hozzon létre virtuális gépeket az Ön által választott operációs rendszerrel és webkiszolgálóval (például Windows/IIS vagy Linux/Apache). Válasszon egy alhálózatot/VLAN-t, amely a webszinthez van kijelölve a privát felhőben. Ellenőrizze, hogy a webkiszolgáló virtuális gépeinek legalább egy virtuális hálózati adaptere a webes réteg alhálózatán van-e.

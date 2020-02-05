---
title: Az Azure Application Gateway használata VMware virtuális gépekkel
description: Leírja, hogyan használhatja az Azure Application Gatewayt a VMware virtuális gépeken futó webkiszolgálók bejövő webes forgalmának kezeléséhez, és megnyerheti az AVS Private Cloud Environment szolgáltatást
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94cc6e40b88fe631d525f41001034f5dada05397
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015456"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-avs-private-cloud-environment"></a>Az Azure Application Gateway használata VMware virtuális gépekkel az AVS Private Cloud Environment-ban

Az Azure Application Gateway használatával felügyelheti a VMware virtuális gépeken futó webkiszolgálók bejövő webes forgalmát az AVS Private Cloud-környezetben.

Azáltal, hogy az Azure-Application Gateway nyilvános hibrid környezetben használja, felügyelheti az alkalmazásokra irányuló webes forgalmat, biztonságos kezelőfelületet biztosíthat, és kioszthatja a VMware-környezetben futó szolgáltatások SSL-feldolgozását. Az Azure Application Gateway a konfigurált szabályok és az állapot-mintavételek szerint a VMware-környezetekben található, háttérrendszer-készlet példányaira irányítja a bejövő webes forgalmat.

Ehhez az Azure Application Gateway-megoldáshoz a következőket kell tennie:

* Azure-előfizetés.
* Hozzon létre és konfiguráljon egy Azure-beli virtuális hálózatot és egy alhálózatot a virtuális hálózaton belül.
* Hozzon létre és konfiguráljon NSG-szabályokat és a vNet a ExpressRoute használatával az AVS Private Cloud-ra.
* Hozzon létre & konfigurálja az AVS saját Felhőjét.
* Hozzon létre & konfigurálja az Azure-Application Gateway.

## <a name="azure-application-gateway-deployment-scenario"></a>Azure Application Gateway üzembe helyezési forgatókönyv

Ebben az esetben az Azure Application Gateway az Azure Virtual Networkben fut. A virtuális hálózat egy ExpressRoute áramkörön keresztül csatlakozik az AVS Private-felhőhöz. Az AVS Private Cloud összes alhálózata IP-címről elérhető a virtuális hálózat alhálózatai között.

![Azure Load Balancer az Azure Virtual Networkben](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>A megoldás üzembe helyezése

Az üzembe helyezési folyamat a következő feladatokből áll:

1. [Ellenőrizze, hogy teljesülnek-e az Előfeltételek](#1-verify-prerequisites)
2. [Azure-beli virtuális kapcsolat csatlakoztatása az AVS Private Cloud-hoz](#2-connect-your-azure-virtual-network-to-your-avs-private-cloud)
3. [Azure Application Gateway üzembe helyezése](#3-deploy-an-azure-application-gateway)
4. [Webkiszolgáló virtuális gép készletének létrehozása és konfigurálása az AVS Private Cloud-ban](#4-create-and-configure-a-web-server-vm-pool-in-your-avs-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Előfeltételek ellenőrzése

Ellenőrizze, hogy teljesülnek-e az előfeltételek:

* Egy Azure Resource Manager és egy virtuális hálózat már létre van hozva.
* Az Azure-beli virtuális hálózaton belül egy dedikált alhálózat (Application Gateway) már létre van hozva.
* Már létrejött egy AVS Private Cloud.
* A virtuális hálózat és a privát felhőben lévő alhálózatok IP-alhálózatai között nincs IP-ütközés.

## <a name="2-connect-your-azure-virtual-network-to-your-avs-private-cloud"></a>2. az Azure-beli virtuális hálózat összekötése az AVS Private Cloud szolgáltatással

Az Azure-beli virtuális hálózat az AVS Private Cloud-hoz való összekapcsolásához kövesse ezt a folyamatot.

1. Az [AVS-portálon másolja a ExpressRoute-](virtual-network-connection.md)társítási adatokat.

2. [Konfiguráljon egy virtuális hálózati átjárót az Azure-beli virtuális hálózathoz](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Csatolja a virtuális hálózatot az AVS ExpressRoute áramkörhöz](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [A virtuális hálózat ExpressRoute-áramkörhöz való kapcsolásához használja a vágólapra másolt egyenrangú adatokat](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Azure Application Gateway üzembe helyezése

Az erre vonatkozó részletes utasítások az [Application Gateway létrehozása elérésiút-alapú útválasztási szabályokkal a Azure Portal használatával](../application-gateway/create-url-route-portal.md)érhetők el. Itt látható a szükséges lépések összegzése:

1. Hozzon létre egy virtuális hálózatot az előfizetésben és az erőforráscsoporthoz.
2. Hozzon létre egy alhálózatot (amelyet dedikált alhálózatként kíván használni) a virtuális hálózaton belül.
3. Hozzon létre egy szabványos Application Gateway (opcionálisan engedélyezze a WAF): a Azure Portal kezdőlapján kattintson az **erőforrás** > **hálózatkezelés** > **Application Gateway** lehetőségre a lap bal felső részén. Válassza ki a szabványos SKU-t, a méretet, és adja meg az Azure-előfizetést, az erőforráscsoport és a hely adatait. Ha szükséges, hozzon létre egy új nyilvános IP-címet az Application Gateway számára, és adja meg a virtuális hálózat és az Application Gateway dedikált alhálózata adatait.
4. Vegyen fel egy háttér-készletet a virtuális gépekkel, és adja hozzá az Application gatewayhez.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-avs-private-cloud"></a>4. webkiszolgáló virtuálisgép-készlet létrehozása és konfigurálása az AVS Private Cloud-ban

A vCenter-ben hozzon létre virtuális gépeket az Ön által választott operációs rendszerrel és webkiszolgálóval (például Windows/IIS vagy Linux/Apache). Válasszon egy alhálózatot/VLAN-t, amelyet az AVS Private Cloud webes szintjéhez jelöltek ki. Ellenőrizze, hogy a webkiszolgáló virtuális gépei legalább egy vNIC szerepelnek-e a webes réteg alhálózatán.

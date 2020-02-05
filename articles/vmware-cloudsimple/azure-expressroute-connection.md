---
title: Azure VMware Solutions (AVS) – a saját AVS-felhő összekötése az Azure-hálózattal az ExpressRoute használatával
description: Útmutató az AVS Private Cloud Environment Azure-beli virtuális hálózathoz való összekapcsolásához a ExpressRoute használatával
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d487794e219f63150142db8df4b0c1abf112947
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015235"
---
# <a name="connect-your-avs-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Az AVS Private Cloud Environment összekötése az Azure Virtual Network ExpressRoute használatával

Az AVS Private Cloud az Azure ExpressRoute használatával csatlakoztatható az Azure-beli virtuális hálózathoz. Ez a nagy sávszélességű, kis késleltetésű kapcsolat lehetővé teszi az Azure-előfizetésében futó szolgáltatások elérését az AVS Private Cloud Environment használatával.

A virtuális hálózati kapcsolatok a következőket teszik lehetővé:

* Az Azure-t biztonsági mentési célként használhatja a virtuális gépekhez az AVS Private Cloud-on.
* KMS-kiszolgálók üzembe helyezése az Azure-előfizetésben az AVS Private Cloud vSAN adattárának titkosításához.
* Használjon olyan hibrid alkalmazásokat, amelyekben az alkalmazás webes szintje a nyilvános felhőben fut, miközben az alkalmazás-és adatbázis-rétegek a saját AVS-felhőben futnak.

![Azure ExpressRoute-kapcsolódás virtuális hálózathoz](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Virtuális hálózati kapcsolatok beállítása

Az AVS Private Cloud-hoz való virtuális hálózati kapcsolat beállításához szüksége lesz az engedélyezési kulcsra, a társ áramköri URI-ra és az Azure-előfizetéséhez való hozzáférésre. Ezek az információk az AVS-portál Virtual Network csatlakozás lapján érhetők el. Útmutatásért lásd: az [Azure-beli virtuális hálózathoz kapcsolódó információk beszerzése az AVS-kapcsolatok eléréséhez](virtual-network-connection.md). Ha bármilyen problémája van az információ beszerzésével, küldjön egy <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">támogatási kérést</a>.

> [!TIP]
> Ha már rendelkezik Azure-beli virtuális hálózattal, átjáró-alhálózattal és virtuális hálózati átjáróval, ugorjon a 4. lépésre.

1. Hozzon létre egy virtuális hálózatot az Azure-előfizetésében, és ellenőrizze, hogy a kiválasztott címterület különbözik-e az AVS Private-felhő címterület-területtől. Ha már rendelkezik Azure-beli virtuális hálózattal, használhatja a meglévőt. Részletekért lásd: [virtuális hálózat létrehozása a Azure Portal használatával](../virtual-network/quick-create-portal.md).
2. Hozza létre az átjáró-alhálózatot az Azure-beli virtuális hálózaton. Ha már rendelkezik átjáró-alhálózattal az Azure-beli virtuális hálózatban, használhatja a meglévőt. Részletekért lásd: [átjáró-alhálózat létrehozása](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Hozza létre a virtuális hálózati átjárót a virtuális hálózaton. Ha meglévő virtuális hálózati átjáróval rendelkezik, használhatja a meglévőt. Részletekért lásd: [a virtuális hálózati átjáró létrehozása](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Hozza létre a kapcsolatot a virtuális hálózat és az AVS Private Cloud között úgy, hogy beváltja az engedélyezési kulcsot a [virtuális hálózat csatlakoztatása egy áramkörhöz – eltérő előfizetéshez](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)című részben leírtak szerint.

> [!WARNING]
> Ha meglévő virtuális hálózati átjárót használ, és egy ExpressRoute-kapcsolattal rendelkezik az AVS ExpressRoute áramkörével megegyező helyen, a kapcsolat nem lesz létrehozva. Hozzon létre egy új virtuális hálózatot, és kövesse az előző lépéseket.

## <a name="test-the-virtual-network-connection"></a>A virtuális hálózati kapcsolatok tesztelése

A létrehozást követően a **Tulajdonságok** elemre kattintva megtekintheti a kapcsolatok **állapotát.** Az állapot és a kiépítési állapot megjelenítésének **sikeresnek**kell lennie.

![A kapcsolatok állapota](media/azure-expressroute-connection.png)

A virtuális hálózati kapcsolatok tesztelése:

1. Hozzon létre egy virtuális gépet az Azure-előfizetésében.
2. Az AVS Private Cloud vCenter IP-címének megkeresése (tekintse meg az üdvözlő e-mailt).
3. Pingelje Felhőbeli vCenter az Azure Virtual Networkben létrehozott virtuális gépről.
4. Az Azure-beli virtuális gép pingelése az AVS Private Cloud vCenter futó virtuális gépről.

Ha problémák merülnek fel a kapcsolat létrehozásakor, küldjön egy <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">támogatási kérést</a>.

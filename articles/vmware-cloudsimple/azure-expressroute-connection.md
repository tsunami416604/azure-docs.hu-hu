---
title: Azure VMware-megoldás a CloudSimple által – Privát felhő csatlakoztatása az Azure-hálózathoz az ExpressRoute használatával
description: A CloudSimple private cloud környezet csatlakoztatása az Azure virtuális hálózatához az ExpressRoute használatával
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015235"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>A CloudSimple privátfelhő-környezet csatlakoztatása az Azure virtuális hálózatához az ExpressRoute használatával

A CloudSimple private cloud az Azure ExpressRoute használatával is csatlakoztatható az Azure virtuális hálózatához.  Ez a nagy sávszélességű, alacsony késésű kapcsolat lehetővé teszi, hogy az Azure-előfizetésében futó szolgáltatásokat a privát felhőkörnyezetből érje el.

A virtuális hálózati kapcsolat lehetővé teszi a következőket:

* Az Azure-t biztonsági mentési célként használhatja a privát felhőben lévő virtuális gépekhez.
* KmS-kiszolgálók üzembe helyezése az Azure-előfizetésben a private cloud vSAN-adattár titkosításához.
* Olyan hibrid alkalmazásokat használjon, amelyeken az alkalmazás webes szintje a nyilvános felhőben fut, míg az alkalmazás- és adatbázisszintek a privát felhőben futnak.

![Azure ExpressRoute-kapcsolat a virtuális hálózattal](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Virtuális hálózati kapcsolat beállítása

A virtuális hálózati kapcsolat beállítása a magánfelhőhöz, szüksége van az engedélyezési kulcs, a társ-áramkörURI-k és az Azure-előfizetéshez való hozzáférés. Ez az információ a CloudSimple portál virtuális hálózati kapcsolatlapján érhető el. További információt az [Azure virtuális hálózat és a CloudSimple-kapcsolat társviszony-létesítési adatainak beszerzése](virtual-network-connection.md)című témakörben talál. Ha bármilyen problémája van az információ megszerzésével, nyújtson be <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">támogatási kérelmet.</a>

> [!TIP]
> Ha már rendelkezik egy Azure virtuális hálózat, átjáró alhálózat, és a virtuális hálózati átjáró, akkor ugorjon a 4.

1. Hozzon létre egy virtuális hálózatot az Azure-előfizetésében, és ellenőrizze, hogy a kiválasztott címterület eltér-e a magánfelhő címterétől.  Ha már rendelkezik egy Azure virtuális hálózat, használhatja a meglévőt.  További információt a [Virtuális hálózat létrehozása az Azure Portal használatával című témakörben talál.](../virtual-network/quick-create-portal.md)
2. Hozza létre az átjáró alhálózatot az Azure virtuális hálózaton.  Ha már rendelkezik egy átjáró alhálózat az Azure virtuális hálózat, használhatja a meglévőt. További információt [az Átjáró alhálózat létrehozása](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)című témakörben talál.
3. Hozza létre a virtuális hálózati átjárót a virtuális hálózaton.  Ha meglévő virtuális hálózati átjáróval rendelkezik, használhatja a meglévőt. További információt a Virtuális hálózati átjáró létrehozása című [témakörben talál.](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)
4. Hozza létre a kapcsolatot a virtuális hálózat és a magánfelhő között az engedélyezési kulcs beváltásával a [Virtuális hálózat csatlakoztatása egy áramkörhöz - más előfizetéshez](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Ha egy meglévő virtuális hálózati átjárót használ, és expressroute-kapcsolattal rendelkezik a CloudSimple ExpressRoute-kapcsolattal azonos helyen, a kapcsolat nem jön létre.  Hozzon létre egy új virtuális hálózatot, és kövesse az előző lépéseket.

## <a name="test-the-virtual-network-connection"></a>A virtuális hálózati kapcsolat tesztelése

A kapcsolat létrehozása után a Beállítások csoport **tulajdonságok** parancsára kattintva ellenőrizheti a kapcsolat **állapotát.**  A jogállás és a kiépítési állapot nak sikeresnek kell **lennie.**

![Kapcsolat állapota](media/azure-expressroute-connection.png)

A virtuális hálózati kapcsolat tesztelése:

1. Hozzon létre egy virtuális gépet az Azure-előfizetésében.
2. Keresse meg a Private Cloud vCenter IP-címét (olvassa el az üdvözlő e-mailt).
3. Pingelheti a Cloud vCenter-t az Azure virtuális hálózatában létrehozott virtuális gépről.
4. Pingelheti az Azure virtuális gépét a privát felhőbeli vCenterben futó virtuális gépről.

Ha bármilyen probléma merül fel a kapcsolat létesítése során, nyújtson be <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">támogatási kérelmet.</a>

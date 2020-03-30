---
title: 'Azure VPN-átjáró: Átjáró törlése: portál'
description: Virtuális hálózati átjáró törlése az Azure Portal használatával az Erőforrás-kezelő telepítési modellben.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: fba213be2de3b747d3ba962674a03c0bc7df3ed5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863665"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Virtuális hálózati átjáró törlése a portál használatával

> [!div class="op_single_selector"]
> * [Azure-portál](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Powershell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasszikus)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Ez a cikk az Erőforrás-kezelő telepítési modell használatával telepített Azure VPN-átjárók törlésének útmutatóját tartalmazza. Van néhány különböző megközelítések, amit megtehetsz, ha törölni szeretné a virtuális hálózati átjáró egy VPN-átjáró konfiguráció.

- Ha mindent törölni szeretne, és újra szeretné kezdeni, mint egy tesztkörnyezet esetében, törölheti az erőforráscsoportot. Erőforráscsoport törlésekor a csoporton belüli összes erőforrás törlődik. Ez a módszer csak akkor ajánlott, ha nem szeretné megtartani az erőforráscsoport egyetlen erőforrását sem. Ezzel a módszerrel nem törölhet szelektíven csak néhány erőforrást.

- Ha meg szeretné tartani az erőforrások egy részét az erőforráscsoportban, a virtuális hálózati átjáró törlése kissé bonyolultabbá válik. A virtuális hálózati átjáró törlése előtt először törölnie kell az átjárótól függő erőforrásokat. Az alábbi lépések a létrehozott kapcsolatok típusától és az egyes kapcsolatok függő erőforrásaitól függenek.

> [!IMPORTANT]
> Az alábbi utasítások ismertetik, hogyan törölheti az Azure VPN-átjárók üzembe helyezett az Erőforrás-kezelő telepítési modell használatával. A klasszikus üzembe helyezési modell használatával telepített VPN-átjáró törléséhez használja az Azure PowerShellt az [itt](vpn-gateway-delete-vnet-gateway-classic-powershell.md)leírtak szerint.


## <a name="delete-a-vpn-gateway"></a>VPN Gateway törlése

Virtuális hálózati átjáró törléséhez először törölnie kell minden olyan erőforrást, amely a virtuális hálózati átjáróhoz kapcsolódik. Az erőforrásokat függőségek miatt bizonyos sorrendben törölni kell.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Ezen a ponton a virtuális hálózati átjáró törlődik. A következő lépésekkel törölheti a már nem használt erőforrásokat.

### <a name="to-delete-the-local-network-gateway"></a>A helyi hálózati átjáró törlése

1. A **Minden erőforrás területen**keresse meg az egyes kapcsolatokhoz társított helyi hálózati átjárókat.
2. A helyi hálózati átjáró **Áttekintés** paneljén kattintson a **Törlés gombra.**

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Az átjáró nyilvános IP-cím erőforrásának törlése

1. A **Minden erőforrás területen**keresse meg az átjáróhoz társított nyilvános IP-cím erőforrást. Ha a virtuális hálózati átjáró aktív-aktív volt, két nyilvános IP-cím jelenik meg. 
2. A Nyilvános IP-cím **Áttekintés** lapján kattintson a **Törlés**gombra, majd a **Megerősítéshez az Igen** gombra.

### <a name="to-delete-the-gateway-subnet"></a>Az átjáró alhálózatának törlése

1. A **Minden erőforrás területen**keresse meg a virtuális hálózatot. 
2. Az **Alhálózatok** panelen kattintson a **GatewaySubnet**menügombra, majd a **Törlés gombra.** 
3. Kattintson az **Igen** gombra az átjáró alhálózatának törlésének megerősítéséhez.

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="deleterg"></a>VPN-átjáró törlése az erőforráscsoport törlésével

Ha nem aggódik amiatt, hogy az erőforrások at az erőforráscsoportban tartsa, és csak újra szeretné kezdeni, törölhet i a teljes erőforráscsoportot. Ez egy gyors módja annak, hogy távolítsa el mindent. A következő lépések csak az Erőforrás-kezelő telepítési modelljére vonatkoznak.

1. A **Minden erőforrás területen**keresse meg az erőforráscsoportot, és kattintson a panel megnyitásához.
2. Kattintson a **Törlés** gombra. A Delete panelen tekintse meg az érintett erőforrásokat. Győződjön meg arról, hogy törölni szeretné ezeket az erőforrásokat. Ha nem, kövesse a VPN-átjáró törlése című, a cikk tetején található lépéseket.
3. A folytatáshoz írja be a törölni kívánt erőforráscsoport nevét, majd kattintson a **Törlés gombra.**

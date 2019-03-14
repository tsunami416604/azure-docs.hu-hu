---
title: 'Virtuális hálózati átjáró törlése: Az Azure Portalon: Erőforrás-kezelő |} A Microsoft Docs'
description: Az Azure portal használatával a Resource Manager-alapú üzemi modellben virtuális hálózati átjáró törlése.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.date: 10/23/2018
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: 387b4e982772f22453876e1ea8b9e7c4039601c4
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790577"
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>A portál használatával virtuális hálózati átjáró törlése

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasszikus)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

Ez a cikk bemutatja az egy a Resource Manager üzemi modell használatával üzembe helyezett Azure VPN-átjárók törlése. Van néhány, különböző megközelítési módot használ, akkor is igénybe vehet, ha meg szeretné a VPN-átjáró konfigurálása a virtuális hálózati átjáró törlése.

- Ha azt szeretné, törölnie kell minden, és kezdje újra a folyamatot, hasonlóan a tesztkörnyezetben, törölheti az erőforráscsoportot. Amikor töröl egy erőforráscsoportot, a csoportban lévő összes erőforrást törli. Ez a módszer csak akkor ajánlott, ha nem szeretné megőrizni a az erőforrások az erőforráscsoportban. Ez a megközelítés csak néhány erőforrásokat külön-külön nem törölhető.

- Ha azt szeretné, hogy egyes erőforrások az erőforráscsoportban, némileg összetettebb egy virtuális hálózati átjáró törlése folyamatban válik. A virtuális hálózati átjáró törlése előtt törölnie kell a olyan erőforrások, amelyek az átjáró függ. A lépéseket követheti az Ön által létrehozott kapcsolatok típusát, és minden kapcsolat esetén a tőle függő erőforrások függenek.

> [!IMPORTANT]
> Az alábbi utasítások a Resource Manager üzemi modell használatával üzembe helyezett Azure VPN-átjárók törlése ismertetik. A klasszikus üzemi modellel üzembe helyezett VPN gateway törléséhez használja az Azure PowerShell leírtak szerint [Itt](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>VPN Gateway törlése

A virtuális hálózati átjáró törléséhez először törölnie kell az egyes erőforrások a virtuális hálózati átjáróhoz tartozó. Erőforrások függőségek miatt bizonyos sorrendben kell törölni.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Ezen a ponton a virtuális hálózati átjáró törlése. A következő lépések segítenek inaktiválja azokat az erőforrásokat, amelyek már nem használ.

### <a name="to-delete-the-local-network-gateway"></a>A helyi hálózati átjáró törlése

1. A **összes erőforrás**, keresse meg a helyi hálózati átjárók társított minden egyes kapcsolat.
2. Az a **áttekintése** a helyi hálózati átjáró paneljén kattintson **törlése**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>Az átjáró nyilvános IP-cím erőforrás törlése

1. A **összes erőforrás**, keresse meg a nyilvános IP-cím erőforrás, amely az átjáró társítva lett. Ha a virtuális hálózati átjáró aktív-aktív volt, látni fogja a két nyilvános IP-címet. 
2. Az a **áttekintése** a nyilvános IP-cím oldalára, kattintson **törlése**, majd **Igen** megerősítéséhez.

### <a name="to-delete-the-gateway-subnet"></a>Az átjáró-alhálózat törlése

1. A **összes erőforrás**, keresse meg a virtuális hálózat. 
2. Az a **alhálózatok** panelen kattintson a **GatewaySubnet**, majd kattintson a **törlése**. 
3. Kattintson a **Igen** annak ellenőrzéséhez, hogy szeretné-e az átjáró-alhálózat törlése.

## <a name="deleterg"></a>Az erőforráscsoport törlésével a VPN gateway törlése

Ha csak át szeretné újrakezdeni, amelyek nem azok tartja az erőforrásokat az erőforráscsoport, egy egész erőforráscsoportot törölheti. Ez az, hogy gyorsan távolítson el minden. Az alábbi lépéseket csak a Resource Manager-alapú üzemi modellre vonatkoznak.

1. A **összes erőforrás**, keresse meg az erőforráscsoportot, és kattintson a panel megnyitásához.
2. Kattintson a **Törlés** gombra. A Delete panelen megtekintheti az érintett erőforrások. Győződjön meg arról, hogy szeretné-e törölni az összes ezeket az erőforrásokat. Ha nem, kövesse a lépéseket törlése a VPN-átjáró Ez a cikk elején.
3. A folytatáshoz adja meg a az erőforráscsoportot törölje, majd kattintson a kívánt **törlése**.

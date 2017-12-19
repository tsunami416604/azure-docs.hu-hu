---
title: "A virtuális hálózati átjáró törlése: Azure-portálon: erőforrás-kezelő |} Microsoft Docs"
description: "Törölje a virtuális hálózati átjáró, az Azure portál használatával a Resource Manager üzembe helyezési modellben."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: cherylmc
ms.openlocfilehash: b67fdfc82bbc132772186e3500079cfcfdafe02b
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Törölje a virtuális hálózati átjáró, a portál használatával

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (klasszikus)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)

A cikkben az Azure VPN gatewayek a Resource Manager üzembe helyezési modellel használatával telepített törlésére vonatkozó utasítások. Többféle különböző megközelítés közül választhat, ha törli a virtuális hálózati átjáró VPN gateway-konfigurációt.

- Ha teljes tartalmának törlése, és kezdje újra a folyamatot, ahogy a gyorsítás esetében is egy tesztkörnyezetben, törölheti az erőforráscsoportot. Ha töröl egy erőforráscsoport, a csoportban lévő összes erőforrást törli. Ez a módszer csak akkor javasolt, ha nem szeretné megtartani az erőforrások az erőforráscsoportban. Ezzel a megközelítéssel csak néhány erőforrásokat külön-külön nem törölhető.

- Ha meg szeretné tartani a erőforrások az erőforráscsoportban, a virtuális hálózati átjáró törlése válik kicsit bonyolultabb. A virtuális hálózati átjáró törlése előtt először törölnie kell az átjáró függő erőforrásokat. A szükséges lépések attól függ, a létrehozott kapcsolatok és a tőle függő erőforrások, minden egyes kapcsolathoz.

> [!IMPORTANT]
> Az alábbi utasítások azt ismertetik, hogyan törlése az Azure VPN gatewayek a Resource Manager üzembe helyezési modellel használatával telepíthetők. A klasszikus telepítési modell használatával telepített VPN-átjáró törléséhez használja az Azure PowerShell leírtak [Itt](vpn-gateway-delete-vnet-gateway-classic-powershell.md).


## <a name="delete-a-vpn-gateway"></a>VPN Gateway törlése

A virtuális hálózati átjáró törlése, először törölnie kell az egyes erőforrások, amelyek a virtuális hálózati átjáró vonatkozik. Erőforrások miatt függőségek meghatározott sorrendben kell törölni.

[!INCLUDE [delete gateway](../../includes/vpn-gateway-delete-vnet-gateway-portal-include.md)]

Ezen a ponton a virtuális hálózati átjáró törlése. A következő lépések segítenek a már nem használt erőforrások törlése.

### <a name="to-delete-the-local-network-gateway"></a>A helyi hálózati átjáró törlése

1. A **összes erőforrás**, keresse meg a helyi hálózati átjáró társított minden egyes kapcsolathoz.
2. Az a **áttekintése** a helyi hálózati átjáró paneljén kattintson **törlése**.

### <a name="to-delete-the-public-ip-address-resource-for-the-gateway"></a>A nyilvános IP-cím erőforrás az átjáró törlése

1. A **összes erőforrás**, keresse meg a kapcsolódó az átjáró nyilvános IP-cím erőforrás. Ha a virtuális hálózati átjáró aktív-aktív volt, két nyilvános IP-cím jelenik meg. 
2. Az a **áttekintése** a nyilvános IP-cím lapján kattintson **törlése**, majd **Igen** megerősítéséhez.

### <a name="to-delete-the-gateway-subnet"></a>Az átjáró-alhálózat törléséhez

1. A **összes erőforrás**, keresse meg a virtuális hálózat. 
2. A a **alhálózatok** panelen kattintson a **GatewaySubnet**, majd kattintson a **törlése**. 
3. Kattintson a **Igen** annak ellenőrzéséhez, hogy szeretné-e az átjáró-alhálózat törléséhez.

## <a name="deleterg"></a>Az erőforráscsoport törlésével VPN-átjáró törlése

Ha nem aggódik megőrzi az erőforrásokat az erőforráscsoport található, és szeretné elölről, törölheti a teljes erőforráscsoport. Ez az gyorsan távolítson el minden. Az alábbi lépéseket csak a Resource Manager üzembe helyezési modellel vonatkoznak.

1. A **összes erőforrás**, keresse meg az erőforráscsoportot, és kattintson a panel megnyitásához.
2. Kattintson a **Törlés** gombra. A Delete panelen megtekintheti a fertőzött erőforrásokat. Győződjön meg arról, hogy valóban törli az összes ilyen erőforrásról. Ha nem, hajtsa végre a lépéseket a [törli a VPN-átjáró](#deletegw) Ez a cikk tetején.
3. A folytatáshoz adja meg az erőforráscsoportot, törölje, majd kattintson a kívánt nevét **törlése**.

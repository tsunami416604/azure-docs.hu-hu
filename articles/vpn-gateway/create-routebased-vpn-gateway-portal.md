---
title: 'Útvonalalapú VPN-átjáró létrehozása: portál'
titleSuffix: Azure VPN Gateway
description: Útvonalalapú VPN-átjáró létrehozása az Azure Portal használatával
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/03/2020
ms.author: cherylmc
ms.openlocfilehash: 6156d689a29ee348f9b1974d1520eb7d186a8d8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331349"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Útvonalalapú VPN-átjáró létrehozása az Azure Portal használatával

Ez a cikk segít gyorsan létrehozni egy útvonal-alapú Azure VPN-átjáró az Azure Portalon keresztül.  A VPN-átjáró a helyszíni hálózattal létesített VPN-kapcsolat létrehozásakor használatos. Vpn-átjárót is használhat a virtuális hálózatok csatlakoztatásához. 

A cikkben ismertetett lépések virtuális hálózatot, alhálózatot, átjáró-alhálózatot és útvonalalapú VPN-átjárót (virtuális hálózati átjárót) hoznak létre. Miután az átjáró létrehozása befejeződött, ezután kapcsolatokat hozhat létre. Ezek a lépések Azure-előfizetést igényelnek. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Virtuális hálózat létrehozása

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>Az átjáró konfigurálása és létrehozása

Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>Az alapszintű átjáró termékváltozat nem támogatja az IKEv2 vagy RADIUS-hitelesítést. Ha azt tervezi, hogy a Mac-ügyfelek csatlakoznak a virtuális hálózathoz, ne használja az alaptermékváltozatot.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>A VPN-átjáró megtekintése

1. Az átjáró létrehozása után keresse meg a virtuális hálózat1 a portálon. A VPN-átjáró csatlakoztatott eszközként jelenik meg az Áttekintés lapon.

   ![Csatlakoztatott eszközök](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Csatlakoztatott eszközök")

2. Az eszközlistában kattintson a **VNet1GW** elemre további információk megtekintéséhez.

   ![VPN-átjáró megtekintése](./media/create-routebased-vpn-gateway-portal/view-gateway.png "VPN-átjáró megtekintése")

## <a name="next-steps"></a>További lépések

Miután az átjáró létrejötte befejeződött, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik virtuális hálózat között. Vagy hozzon létre egy kapcsolatot a virtuális hálózat és a helyszíni hely között.

> [!div class="nextstepaction"]
> [Helyek közötti kapcsolat létrehozása](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Pont–hely kapcsolat létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Kapcsolat létrehozása másik virtuális hálózattal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)

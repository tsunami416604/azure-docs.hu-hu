---
title: 'Route-alapú VPN-átjáró létrehozása: Azure Portal | Microsoft Docs'
description: Route-alapú VPN Gateway létrehozása a Azure Portal használatával
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: cherylmc
ms.openlocfilehash: 2a04c0fa2d92514103377c2aef420290d1bdd057
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781174"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Route-alapú VPN-átjáró létrehozása a Azure Portal használatával

Ez a cikk segítséget nyújt egy Route-alapú Azure VPN-átjáró gyors létrehozásához a Azure Portal használatával.  VPN-átjárót a helyszíni hálózathoz való VPN-kapcsolat létrehozásakor használ a rendszer. VPN-átjárót is használhat a virtuális hálózatok összekapcsolásához. 

A cikkben szereplő lépések egy VNet, egy alhálózatot, egy átjáró-alhálózatot és egy Route-alapú VPN-átjárót (virtuális hálózati átjárót) hoznak létre. Miután az átjáró létrehozása befejeződött, létrehozhat kapcsolatokat. Ezeknek a lépéseknek Azure-előfizetésre van szükségük. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="vnet"></a>Virtuális hálózat létrehozása

[!INCLUDE [create-gateway](../../includes/vpn-gateway-create-virtual-network-portal-include.md)]

## <a name="gwsubnet"></a>Átjáró-alhálózat hozzáadása

[!INCLUDE [gateway subnet](../../includes/vpn-gateway-add-gateway-subnet-portal-include.md)]

## <a name="gwvalues"></a>Az átjáró konfigurálása és létrehozása

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>Az alapszintű átjáró SKU nem támogatja a IKEv2 vagy a RADIUS-hitelesítést. Ha azt tervezi, hogy a Mac-ügyfelek csatlakoznak a virtuális hálózathoz, ne használja az alapszintű SKU-t.

## <a name="viewgw"></a>A VPN-átjáró megtekintése

1. Az átjáró létrehozása után navigáljon a VNet1 a portálon. A VPN-átjáró csatlakoztatott eszközként jelenik meg az Áttekintés oldalon.

   ![Csatlakoztatott eszközök](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Csatlakoztatott eszközök")

2. További információk megtekintéséhez az eszközök listájában kattintson a **VNet1GW** elemre.

   ![VPN-átjáró megtekintése](./media/create-routebased-vpn-gateway-portal/view-gateway.png "VPN-átjáró megtekintése")

## <a name="next-steps"></a>További lépések

Miután az átjáró elkészült, létrehozhat egy kapcsolatot a virtuális hálózat és egy másik VNet között. Vagy hozzon létre kapcsolatot a virtuális hálózat és a helyszíni hely között.

> [!div class="nextstepaction"]
> [Helyek közötti kapcsolat létrehozása](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Pont – hely kapcsolat létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Kapcsolatok létrehozása másik VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)

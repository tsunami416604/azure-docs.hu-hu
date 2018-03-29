---
title: 'Útvonalalapú VPN-átjáró létrehozása: Azure-portál |} Microsoft Docs'
description: Gyors létrehozása az Azure portál használatával útvonalalapú VPN-átjáró
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: cherylmc
ms.openlocfilehash: 2d6133e974e24c8c4f769995d8245b30a29a3983
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Az Azure portál használatával útvonalalapú VPN-átjáró létrehozása

Ez a cikk segít gyorsan létrehozhat egy útválasztó-alapú Azure VPN gateway az Azure portál használatával.  VPN-átjáró a helyszíni hálózathoz egy VPN-kapcsolat létrehozása során használatos. VPN-átjáró a Vnetek csatlakozhatnak is használhatja. 

A cikkben leírt lépéseket hoz létre egy virtuális hálózatot, alhálózatot, egy átjáró-alhálózatot és útválasztó-alapú VPN-átjáró (virtuális hálózati átjáró). Miután befejezte az átjáró létrehozása, majd létrehozhat kapcsolatokat. Ezeket a lépéseket az Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="vnet"></a>Virtuális hálózat létrehozása

1. Egy böngészőből lépjen az [Azure Portalra](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.
2. Kattintson az **Erőforrás létrehozása** gombra. A **Keresés a piactéren** mezőbe írja be a „Virtuális hálózat” kifejezést. A visszaadott listában keresse meg a **Virtuális hálózat** elemet, és arra kattintva nyissa meg a **Virtuális hálózat** lapot.
3. A virtuális hálózat lap alján a közelében a **telepítési modell kiválasztása** listában ellenőrizze, hogy **erőforrás-kezelő** elemet a legördülő listából, és kattintson a **létrehozása**. Ekkor megnyílik a **virtuális hálózat létrehozása** lap.
4. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. A mezők kitöltése közben a vörös felkiáltójelből zöld pipa lesz, ha a mezőbe beírt karakterek érvényesek. Használja a következő értékeket:

  - **Név**: TestVNet1
  - **Címtér**: 10.1.0.0/16
  - **Előfizetés**: Győződjön meg róla, hogy a felsorolt előfizetések szeretne használni. Az előfizetéseket a legördülő menüben módosíthatja.
  - **Erőforráscsoport**: TestRG1
  - **Hely**: USA keleti régiója
  - **Alhálózati**: előtér
  - **Címtartomány**: 10.1.0.0/24

  ![Virtuális hálózat létrehozása lap](./media/create-routebased-vpn-gateway-portal/vnet1.png "Virtuális hálózat létrehozása lap")
5. Után írja be az értékeket, válassza ki a **rögzítés az irányítópulton** könnyen megtalálni a Vnetet az irányítópulton, és kattintson a **létrehozása**. Miután rákattintott **létrehozása**, megjelenik egy csempe az irányítópulton, amely tükrözi a a VNet állapotát mutatja. A virtuális hálózat létrejöttével a csempe is módosul.

## <a name="gwsubnet"></a>Egy átjáró alhálózatának hozzáadása

Az átjáró alhálózatának tartalmazza a fenntartott IP-címek, amelyek a virtuális hálózati átjáró szolgáltatások használják. Hozzon létre egy átjáró-alhálózatot.

1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. Kattintson a virtuális hálózat lap **alhálózatok** kibontásához **VNet1 - alhálózatok** lap.
3. Kattintson a **+ átjáróalhálózatot** megnyitásához tetején a **alhálózat hozzáadása** lap.

  ![Az átjáró alhálózatának hozzáadása](./media/create-routebased-vpn-gateway-portal/gateway_subnet.png "Az átjáró alhálózatának hozzáadása")
4. A **neve** az az alhálózat automatikusan kitölti a szükséges érték "GatewaySubnet". Módosítsa a automatikusan kitöltött **-címtartományt** értékeket a következő értékeket:

  **Címtartomány (CIDR-blokkja)**: 10.1.255.0/27

  ![Az átjáró alhálózatának hozzáadása](./media/create-routebased-vpn-gateway-portal/add_gw_subnet.png "Az átjáró alhálózatának hozzáadása")
5. Az átjáró alhálózatának létrehozásához kattintson a **OK** az oldal alján.

## <a name="gwvalues"></a>Átjáró beállításainak konfigurálása

1. A portál lap bal oldalán kattintson **+ hozzon létre egy erőforrást** be a keresőmezőbe írja be a "Virtuális hálózati átjáró". Az **Eredmények** között keresse meg a **Virtuális hálózati átjáró** elemet, és kattintson rá.
2. A "Virtuális hálózati átjáró" lap alján kattintson **létrehozása** megnyitásához a **virtuális hálózati átjáró létrehozása** lap.
3. A **Virtuális hálózati átjáró létrehozása** lapon adja meg a virtuális hálózati átjáró értékeit.

  - **Név**: Vnet1GW
  - **Átjáró típusa**: VPN 
  - **VPN-típus**: útválasztó-alapú
  - **SKU**: VpnGw1
  - **Hely**: USA keleti régiója
  - **Virtuális hálózati**: kattintson a **virtuális hálózati/válasszon a virtuális hálózati** megnyitásához a **virtuális hálózatot választ** lap. Válassza ki **VNet1**.

  ![Átjáró beállításainak](./media/create-routebased-vpn-gateway-portal/configure_gw.png "-átjáró beállításainak konfigurálása")

## <a name="pip"></a>A nyilvános IP-cím létrehozása

VPN-átjáró rendelkeznie kell egy dinamikusan kiosztott nyilvános IP-címet. A VPN-átjáró kapcsolatot hoz létre, ha azt az IP-cím, amely a helyszíni eszköz csatlakozik.

1. Válassza ki **első IP-konfigurációs létrehozása átjáró IP-konfiguráció** egy nyilvános IP-cím kéréséhez.

  ![Első IP-konfiguráció](./media/create-routebased-vpn-gateway-portal/ip.png "első IP-konfiguráció")
2. Az a **válasszon nyilvános IP-lapon**, kattintson a **+ új létrehozása** megnyitásához a **nyilvános IP-cím létrehozása** lap.
3. Adja meg a beállításokat a következő értékekkel:

  - **Név**: **VNet1GWPIP**
  - **SKU**: **alapvető**

  ![Nyilvános IP-cím létrehozása](./media/create-routebased-vpn-gateway-portal/gw_ip.png "PIP létrehozása")
4. Kattintson a **OK** menti a módosításokat a lap alján.

## <a name="creategw"></a>A VPN-átjáró létrehozása

1. Ellenőrizze, hogy a beállítások a **virtuális hálózati átjáró létrehozása** lap. Ha szükséges, állítsa be értékeket.

  ![VPN-átjáró létrehozása](./media/create-routebased-vpn-gateway-portal/create_gw.png "hozzon létre VPN-átjáró")
2. Kattintson a **létrehozása** az oldal alján.

Miután rákattintott **létrehozása**, a beállítások érvényesítése és a **telepítése virtuális hálózati átjáró** csempe jelenik meg az irányítópulton. VPN-átjáró akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.

## <a name="viewgw"></a>A VPN-átjáró megtekintése

1. Az átjáró létrehozása után nyissa meg VNet1 a portálon. A VPN-átjáró egy csatlakoztatott eszközön Áttekintés lap jelenik meg.

  ![Csatlakoztatott eszközök](./media/create-routebased-vpn-gateway-portal/connected_devices.png "csatlakoztatott eszközök")

2. Eszköz, kattintson a **VNet1GW** további információk megjelenítéséhez.

  ![Nézet VPN-átjáró](./media/create-routebased-vpn-gateway-portal/view_gw2.png "nézet VPN-átjáró")

## <a name="next-steps"></a>További lépések

Miután az átjáró létrehozása befejeződött, a kapcsolat a virtuális hálózat és egy másik virtuális hálózat között is létrehozhat. Vagy a virtuális hálózat és egy helyszíni hely közötti kapcsolatot.

> [!div class="nextstepaction"]
> [Pont-pont kapcsolatot](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> [hozzon létre egy pont – hely kapcsolat](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> [kapcsolatot létesíthet egy másik virtuális hálózatot](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
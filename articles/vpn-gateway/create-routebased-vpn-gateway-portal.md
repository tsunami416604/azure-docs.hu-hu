---
title: 'Útvonalalapú VPN-átjáró létrehozása: az Azure portal |} A Microsoft Docs'
description: Gyorsan létrehozhat egy útvonalalapú VPN-átjárót az Azure portal használatával
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: fe05ab36f971105cf72342b8df5e2a82de7fc2b8
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391793"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Az Azure portal használatával útvonalalapú VPN-átjáró létrehozása

Ez a cikk segít gyorsan létrehozhat egy útválasztó-alapú Azure VPN gateway az Azure portal használatával.  VPN-átjáró a helyszíni hálózathoz egy VPN-kapcsolat létrehozása során használatos. Virtuális hálózatok csatlakoztatása VPN gateway használhatja. 

A jelen cikkben ismertetett lépések hoz létre virtuális hálózat, alhálózat, egy átjáró-alhálózatot és egy útvonalalapú VPN-átjáró (virtuális hálózati átjáró). Miután az átjáró létrehozása befejeződött, majd létrehozhat kapcsolatokat. Ezeket a lépéseket az Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="vnet"></a>Virtuális hálózat létrehozása

1. Egy böngészőből lépjen az [Azure Portalra](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.
2. Kattintson az **Erőforrás létrehozása** gombra. A **Keresés a piactéren** mezőbe írja be a „Virtuális hálózat” kifejezést. A visszaadott listában keresse meg a **Virtuális hálózat** elemet, és arra kattintva nyissa meg a **Virtuális hálózat** lapot.
3. A virtuális hálózat lap aljának közelében található az a **telepítési modell kiválasztása** listában ellenőrizze, hogy **Resource Manager** kiválasztott elemet a legördülő listára, és kattintson **létrehozás**. Ekkor megnyílik a **virtuális hálózat létrehozása** lapot.
4. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. A mezők kitöltése közben a vörös felkiáltójelből zöld pipa lesz, ha a mezőbe beírt karakterek érvényesek. Használja a következő értékeket:

  - **Név**: TestVNet1
  - **Címtér**: 10.1.0.0/16
  - **Előfizetés**: Győződjön meg róla, hogy a felsorolt előfizetések létrehozásához használni szeretne. Az előfizetéseket a legördülő menüben módosíthatja.
  - **Erőforráscsoport**: TestRG1
  - **Hely**: USA keleti RÉGIÓJA
  - **Alhálózat**: Frontend
  - **Címtartomány**: 10.1.0.0/24

  ![Virtuális hálózat létrehozása lap](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "Virtuális hálózat létrehozása lap")
5. Miután megadta az értékeket, válassza ki a **rögzítés az irányítópulton** könnyen megtalálni a Vnetet az irányítópulton, és kattintson a **létrehozás**. Kattintás után **létrehozás**, láthatja a csempét az irányítópulton, amely a virtuális hálózat állapotát mutatja. A virtuális hálózat létrejöttével a csempe is módosul.

## <a name="gwsubnet"></a>Átjáró-alhálózat hozzáadása

Az átjáróalhálózat tartalmazza a fenntartott IP-címek, amelyek a virtuális hálózati átjáró-szolgáltatások használják. Hozzon létre egy átjáró-alhálózatot.

1. Keresse meg a portálon azt a virtuális hálózatot, amelyhez létre kíván hozni egy virtuális hálózati átjárót.
2. Kattintson a virtuális hálózat lap **alhálózatok** kibontásához **VNet1 - alhálózatok** lap.
3. Kattintson a **+ átjáró-alhálózat** megnyitásához tetején a **alhálózat hozzáadása** lapot.

  ![Az átjáró alhálózatának hozzáadása](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "Az átjáró alhálózatának hozzáadása")
4. A **neve** a rendszer automatikusan kitölti az alhálózat szükséges "GatewaySubnet" érték. Módosíthatja az automatikusan kitöltött **címtartomány** értékeit, hogy megfeleljenek a következő értékeket:

  **Címtartomány (CIDR-blokk)**: 10.1.255.0/27

  ![Az átjáró alhálózatának hozzáadása](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "Az átjáró alhálózatának hozzáadása")
5. Az átjáró-alhálózat létrehozásához kattintson a **OK** az oldal alján.

## <a name="gwvalues"></a>Átjáró beállításainak konfigurálása

1. A portáloldal bal oldalán kattintson a **+ erőforrás létrehozása** , és írja be "Virtuális hálózati átjáró" kifejezést a keresőmezőbe, majd nyomja le az **Enter**. Az **Eredmények** között keresse meg a **Virtuális hálózati átjáró** elemet, és kattintson rá.
2. A "Virtuális hálózati átjáró" lap alján kattintson **létrehozás** megnyitásához a **virtuális hálózati átjáró létrehozása** lapot.
3. A **Virtuális hálózati átjáró létrehozása** lapon adja meg a virtuális hálózati átjáró értékeit.

  - **Név**: Vnet1GW
  - **Átjáró típusa**: VPN 
  - **VPN-típust**: útvonalalapú
  - **Termékváltozat**: VpnGw1
  - **Hely**: USA keleti RÉGIÓJA
  - **Virtuális hálózat**: kattintson a **virtuális hálózat/válasszon egy virtuális hálózatot** megnyitásához a **virtuális hálózat választása** lapot. Válassza ki **VNet1**.

  ![Átjáró beállításainak](./media/create-routebased-vpn-gateway-portal/configure-gateway.png "-átjáró beállításainak konfigurálása")

## <a name="pip"></a>Hozzon létre egy nyilvános IP-cím

VPN-átjáró dinamikusan lefoglalt nyilvános IP-címmel kell rendelkeznie. VPN-átjáró kapcsolatot hoz létre, ha ez az az IP-címet, amely a helyszíni eszközhöz csatlakozik.

1. Válassza ki **első IP-cím konfigurációs átjáró IP-konfigurációjának létrehozása** kérjen egy nyilvános IP-címet.

  ![Első IP-konfiguráció](./media/create-routebased-vpn-gateway-portal/add-public-ip-address.png "első IP-konfiguráció")
2. Az a **válasszon nyilvános IP-lapon**, kattintson a **+ új létrehozása** megnyitásához a **nyilvános IP-cím létrehozása** lapot.
3. Adja meg a beállításokat a következő értékeket:

  - **Név**: **VNet1GWIP**
  - **Termékváltozat**: **alapszintű**

  ![Nyilvános IP-cím létrehozása](./media/create-routebased-vpn-gateway-portal/public-ip-address-name.png "PIP létrehozása")
4. Kattintson a **OK** alján, ezen a lapon a módosítások mentéséhez.

## <a name="creategw"></a>A VPN-átjáró létrehozása

1. Ellenőrizze a beállításokat a **virtuális hálózati átjáró létrehozása** lapot. Szükség esetén módosítsa az értékeket.

  ![VPN-átjáró létrehozása](./media/create-routebased-vpn-gateway-portal/create-vpn-gateway.png "hozzon létre VPN-átjáró")
2. Kattintson a **létrehozás** az oldal alján.

Miután rákattintott **létrehozás**, a rendszer érvényesíti a beállításokat és a **üzembe helyezése virtuális hálózati átjáró** csempe az irányítópulton. VPN-átjáró akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.

## <a name="viewgw"></a>A VPN-átjáró megtekintése

1. Az átjáró létrehozása után lépjen a vnet1 felől a portálon. A VPN-átjáró csatlakoztatott eszközként az Áttekintés lap jelenik meg.

  ![Csatlakoztatott eszközök](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "csatlakoztatott eszközök")

2. Kattintson az eszközlistában **VNet1GW** további információk megtekintéséhez.

  ![VPN-átjáró nézet](./media/create-routebased-vpn-gateway-portal/view-gateway.png "nézet VPN-átjáró")

## <a name="next-steps"></a>További lépések

Miután az átjáró létrehozása befejeződött, létrehozhat egy kapcsolatot a virtuális hálózat és a egy másik virtuális hálózat között. Vagy hozzon létre egy kapcsolatot a virtuális hálózat és a egy helyszíni hely között.

> [!div class="nextstepaction"]
> [Hozzon létre egy helyek közötti kapcsolat](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Pont – hely kapcsolat létrehozása](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Hozzon létre egy kapcsolatot egy másik virtuális hálózathoz](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)

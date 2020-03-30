---
title: 'Helyszíni hálózat csatlakoztatása az Azure virtuális hálózatához: Helyek közötti VPN: Portal'
description: A helyszíni hálózatot az Azure-beli virtuális hálózattal a nyilvános interneten keresztül összekötő IPsec-kapcsolat létrehozásának lépései. Ezen lépéseket követve létrehozhat egy helyek közötti VPN-átjáró kapcsolatot a portál segítségével.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: cherylmc
ms.openlocfilehash: 857b50a04466f43a25cf80d7930cfb4639dc9d65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244432"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Helyek közötti kapcsolat létrehozása az Azure Portalon

Ez a cikk bemutatja, hogyan használhatja az Azure Portalt egy helyek közötti VPN-átjárókapcsolat létrehozására egy helyszíni hálózat és a Vnet között. A cikkben ismertetett lépések a Resource Manager-alapú üzemi modellre vonatkoznak. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Azure-portál](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Powershell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [parancssori felület](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [(Klasszikus) Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. További információk a VPN-átjárókról: [Információk a VPN Gatewayről](vpn-gateway-about-vpngateways.md).

![Helyek közötti VPN Gateway létesítmények közötti kapcsolathoz – diagram](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Győződjön meg arról, hogy rendelkezésre áll egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. További információk a kompatibilis VPN-eszközökről és az eszközkonfigurációról: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).
* Győződjön meg arról, hogy rendelkezik egy kifelé irányuló, nyilvános IPv4-címmel a VPN-eszköz számára.
* Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek. Amikor létrehozza ezt a konfigurációt, meg kell határoznia az IP-címtartományok előtagjait, amelyeket az Azure majd a helyszínre irányít. A helyszíni hálózat egyik alhálózata sem lehet átfedésben azokkal a virtuális alhálózatokkal, amelyekhez csatlakozni kíván. 

### <a name="example-values"></a><a name="values"></a>Példaértékek

A cikkben szereplő példák a következő értékeket használják. Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat. További információkat a VPN Gateway beállításairól általánosságban [a VPN Gateway beállításaival kapcsolatos](vpn-gateway-about-vpn-gateway-settings.md) cikkben találhat.

* **Virtuális hálózat neve:** Virtuális hálózat1
* **Címtér:** 10.1.0.0/16
* **Előfizetés:** A használni kívánt előfizetés
* **Erőforráscsoport:** TestRG1
* **Régió:** USA keleti része
* **Alhálózat:** Előtér: 10.1.0.0/24, Háttér: 10.1.1.0/24 (nem kötelező ehhez a gyakorlathoz)
* **Átjáró alhálózati címtartománya:** 10.1.255.0/27
* **Virtuális hálózati átjáró neve:** VNet1GW
* **Nyilvános IP-cím neve:** VNet1GWpip
* **VPN típusa:** útvonalalapú
* **Kapcsolat típusa:** Helyek közötti (IPsec)
* **Átjáró típusa:** VPN
* **Helyi hálózati átjáró neve:** 1. hely
* **Kapcsolat neve:** VNet1toSite1
* **Megosztott kulcs:** Ebben a példában az abc123 kulcsot használjuk. Használhat azonban bármi mást is, ami kompatibilis a VPN-hardverrel. A lényeg az, hogy az értékek azonosak legyenek a kapcsolat két oldalán.

## <a name="1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. Hozzon létre egy virtuális hálózat

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-the-vpn-gateway"></a><a name="VNetGateway"></a>2. A VPN-átjáró létrehozása

Ebben a lépésben a virtuális hálózat virtuális hálózati átjáróját fogja létrehozni. Az átjáró létrehozása akár 45 percet vagy hosszabb időt is igénybe vehet a választott átjáró-termékváltozattól függően.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="example-settings"></a>Példabeállítások

* **Példány részletei > régió:** USA keleti része
* **Virtuális hálózat > virtuális hálózat:** Virtuális hálózat1
* **A példány adatai > név:** VNet1GW
* **A példány részletei > átjáró típusa:** Vpn
* **A példány részletei > VPN-típus:** Útvonal-alapú
* **Virtuális hálózat > átjáró alhálózati címtartománya:** 10.1.255.0/27
* **Nyilvános IP-cím > nyilvános IP-cím neve:** VNet1GWpip

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]


## <a name="3-create-the-local-network-gateway"></a><a name="LocalNetworkGateway"></a>3. A helyi hálózati átjáró létrehozása

A helyi hálózati átjáró általában a helyszínt jelenti. Olyan nevet adjon a helynek, amellyel az Azure hivatkozhat rá, majd határozza meg annak a helyszíni VPN-eszköznek az IP-címét, amellyel létre kívánja hozni a kapcsolatot. Emellett megadhatja azokat az IP-címelőtagokat, amelyek a VPN-átjárón keresztül a VPN-eszközre lesznek irányítva. Az Ön által meghatározott címelőtagok a helyszíni hálózatán található előtagok. Ha a helyszíni hálózat megváltozik, vagy módosítania kell a VPN-eszköz nyilvános IP-címét, könnyen frissítheti az értékeket később.

**Példaértékek**

* **Név:** 1. hely
* **Erőforráscsoport:** TestRG1
* **Helyszín:** USA keleti része


[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="4-configure-your-vpn-device"></a><a name="VPNDevice"></a>4. A VPN-eszköz konfigurálása

A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Ebben a lépésben a VPN-eszköz konfigurálása következik. A VPN-eszköz konfigurálásakor a következőkre van szüksége:

- Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.
- A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. A VPN-átjáró nyilvános IP-címének Azure Portal használatával történő megkereséséhez válassza a **Virtual network gateways** (Virtuális hálózati átjárók) elemet, majd kattintson az átjárója nevére.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="5-create-the-vpn-connection"></a><a name="CreateConnection"></a>5. A VPN-kapcsolat létrehozása

Hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a helyszíni VPN-eszköz között.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="6-verify-the-vpn-connection"></a><a name="VerifyConnection"></a>6. A VPN-kapcsolat ellenőrzése

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Csatlakozás virtuális géphez

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>VPN-átjáró alaphelyzetbe állítása

Az Azure VPN Gateway alaphelyzetbe állítása akkor hasznos, ha egy vagy több helyek közötti VPN-alagúton elveszíti a létesítmények közötti VPN-kapcsolatot. Ebben az esetben a helyszíni VPN-eszközei megfelelően működnek, de nem tudnak Ipsec-alagutakat létesíteni az Azure VPN Gateway átjárókkal. A lépéseket lásd: [VPN Gateway alaphelyzetbe állítása](vpn-gateway-resetgw-classic.md).

## <a name="how-to-change-a-gateway-sku-resize-a-gateway"></a><a name="resize"></a>Az átjárók termékváltozatainak módosítása (átjáró átméretezése)

Az átjárók termékváltozatainak módosításához szükséges lépéseket lásd: [Átjáró-termékváltozatok](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="how-to-add-an-additional-connection-to-a-vpn-gateway"></a><a name="addconnect"></a>További kapcsolat hozzáadása VPN-átjáróhoz

Hozzáadhat további kapcsolatokat, feltéve, hogy a címterek nincsenek egymással átfedésben a kapcsolatok között.

1. További kapcsolat hozzáadásához lépjen a VPN-átjáróra, majd a **Kapcsolatok** elemre kattintva nyissa meg a Kapcsolatok lapot.
2. A kapcsolata hozzáadásához kattintson a **+Hozzáadás** elemre. Adja a kapcsolat típusát virtuális hálózatok közötti kapcsolatként (ha egy másik VNet-átjáróhoz kapcsolódik) vagy helyek közötti kapcsolatként.
3. Ha a helyek közötti beállítással kapcsolódik, és még nem hozott létre helyi hálózati átjárót ahhoz a helyhez, amelyhez csatlakozni szeretne, létrehozhat egy újat.
4. Adja meg a használni kívánt megosztott kulcsot, majd kattintson az **OK** gombra a kapcsolat létrehozásához.

## <a name="next-steps"></a>További lépések

* Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).
* A kényszerített bújtatásról a [Kényszerített bújtatás – további információt a Kényszerített bújtatás ról című témakörben talál.](vpn-gateway-forced-tunneling-rm.md)
* Információk a magas rendelkezésre állású aktív-aktív kapcsolatokról: [Magas rendelkezésre állású kapcsolatok létesítmények, illetve virtuális hálózatok között](vpn-gateway-highlyavailable.md).
* Információ egy virtuális hálózatban lévő erőforrásokra irányuló hálózati forgalom korlátozásáról: [Hálózati biztonság](../virtual-network/security-overview.md).
* Információ arról, hogyan irányítja az Azure az Azure, a helyszíni és az internetes erőforrások közötti forgalom útválasztását: [Virtuális hálózat forgalmának útválasztása](../virtual-network/virtual-networks-udr-overview.md).
* A helyek közötti VPN-kapcsolat Azure Resource Manager sablonnal történő létrehozásáról a [Helyek közötti VPN-kapcsolat létrehozása című](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/)témakörben olvashat.
* A Vnet-vnet VPN-kapcsolat Azure Resource Manager-sablonhasználatával történő létrehozásáról a [HBase georeplikáció telepítése című](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/)témakörben olvashat.

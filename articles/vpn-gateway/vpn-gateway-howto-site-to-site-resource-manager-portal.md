---
title: "A helyszíni hálózat csatlakoztatása egy Azure-beli virtuális hálózathoz: Helyek közötti VPN: Portal | Microsoft Docs"
description: "A helyszíni hálózatot az Azure-beli virtuális hálózattal a nyilvános interneten keresztül összekötő IPsec-kapcsolat létrehozásának lépései. Ezen lépéseket követve létrehozhat egy helyek közötti VPN-átjáró kapcsolatot a portál segítségével."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: fea9796d8ca03351573f091dce41689743821cad
ms.contentlocale: hu-hu
ms.lasthandoff: 05/08/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Helyek közötti kapcsolat létrehozása az Azure Portalon

Ez a cikk bemutatja, hogyan használhatja az Azure Portalt egy helyek közötti VPN-átjárókapcsolat létrehozására egy helyszíni hálózat és a Vnet között. A cikkben ismertetett lépések a Resource Manager-alapú üzemi modellre vonatkoznak. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager – parancssori felület](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klasszikus – Klasszikus portál](vpn-gateway-site-to-site-create.md)
> 
>


A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. További információk a VPN-átjárókról: [Információk a VPN Gatewayről](vpn-gateway-about-vpngateways.md).

![Helyek közötti VPN Gateway létesítmények közötti kapcsolathoz – diagram](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Erősítse meg, hogy a Resource Manager-alapú üzemi modellt kívánja használni. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* Egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. További információk a kompatibilis VPN-eszközökről és az eszközkonfigurációról: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).
* Egy kifelé irányuló, nyilvános IPv4 IP-cím a VPN-eszközhöz. Ez az IP-cím nem lehet NAT mögötti.
* Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek. Amikor létrehozza ezt a konfigurációt, meg kell határoznia az IP-címtartományok előtagjait, amelyeket az Azure majd a helyszínre irányít. A helyszíni hálózat egyik alhálózata sem lehet átfedésben azokkal a virtuális alhálózatokkal, amelyekhez csatlakozni kíván. 

### <a name="values"></a>Példaértékek
Ha gyakorlatként használja ezeket a lépéseket, használhatja a következő példaértékeket:

* **Virtuális hálózat neve:** TestVNet1
* **Címtér:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (nem kötelező ehhez a gyakorlathoz)
* **Alhálózatok:**
  * Előtér: 10.11.0.0/24
  * Háttér: 10.12.0.0/24 (nem kötelező ehhez a gyakorlathoz)
  * Átjáró-alhálózat: 10.11.255.0/27
* **Erőforráscsoport:** TestRG1
* **Hely:** az USA keleti régiója
* **DNS-kiszolgáló:** A saját DNS-kiszolgálójának IP-címe
* **Virtuális hálózati átjáró neve:** VNet1GW
* **Nyilvános IP-cím:** VNet1GWIP
* **VPN típusa:** útvonalalapú
* **Kapcsolat típusa:** helyek közötti kapcsolat (IPsec)
* **Átjáró típusa:** VPN
* **Helyi hálózati átjáró neve:** Site2
* **Kapcsolat neve:** VNet1toSite2

## <a name="CreatVNet"></a>1. Virtuális hálózat létrehozása

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2. DNS-kiszolgáló megadása
A helyek közötti kapcsolatokhoz nincs szükség DNS-re. Ha azonban azt szeretné, hogy a virtuális hálózatokon üzembe helyezett erőforrásokon működjön a névfeloldás, adjon meg egy DNS-kiszolgálót. Ezzel a beállítással megadhatja azt a DNS-kiszolgálót, amelyet névfeloldásra kíván használni ennél a virtuális hálózatnál. A beállítás nem hoz létre új DNS-kiszolgálót.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3. Az átjáróalhálózat létrehozása

A virtuális hálózati átjáró átjáróalhálózatot használ, amely a VPN Gateway szolgáltatások által használt IP-címeket tartalmazza. A létrehozott átjáróalhálózatnak a „GatewaySubnet” nevet kell adnia. Ha más nevet ad meg, a kapcsolat konfigurálása sikertelen lesz.

Az Ön által megadott átjáróalhálózat mérete a létrehozni kívánt VPN-átjárókonfigurációtól függ. Bár akár /29-es átjáróalhálózatot is létrehozhat, javasolt egy ennél nagyobb, több címmel rendelkező alhálózatot létrehozni: /27-eset vagy /28-asat. Nagyobb átjáróalhálózat használatával elegendő IP-cím áll rendelkezésre az esetleges jövőbeni konfigurációk megvalósításához.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]


## <a name="VNetGateway"></a>4. A VPN-átjáró létrehozása

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. A helyi hálózati átjáró létrehozása

A helyi hálózati átjáró általában a helyszínt jelenti. Olyan nevet adjon a helynek, amellyel az Azure hivatkozhat rá, majd határozza meg annak a helyszíni VPN-eszköznek az IP-címét, amellyel létre kívánja hozni a kapcsolatot. Emellett megadhatja azokat az IP-címelőtagokat, amelyek a VPN-átjárón keresztül a VPN-eszközre lesznek irányítva. Az Ön által meghatározott címelőtagok a helyszíni hálózatán található előtagok. A helyszíni hálózat módosításakor az előtagok egyszerűen frissíthetők.

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6. VPN-eszköz konfigurálása

A helyszíni hálózaton a helyek közötti kapcsolatok létesítéséhez VPN-eszközre van szükség. Ebben a lépésben a VPN-eszköz konfigurálása következik. A VPN-eszköz konfigurálásakor a következőkre van szüksége:

- Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor ad meg. A példákban alapvető megosztott kulcsot használunk. Javasoljuk egy ennél összetettebb kulcs létrehozását.
- A virtuális hálózati átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. A VPN-átjáró nyilvános IP-címének Azure Portal használatával történő megkereséséhez válassza a **Virtual network gateways** (Virtuális hálózati átjárók) elemet, majd kattintson az átjárója nevére.


[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>7. VPN-kapcsolat létrehozása

Hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a helyszíni VPN-eszköz között.

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8. A VPN-kapcsolat ellenőrzése

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]


## <a name="next-steps"></a>Következő lépések

*  Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).
*  Információk a kényszerített bújtatásról: [Információk a kényszerített bújtatásról](vpn-gateway-forced-tunneling-rm.md)
*  Információk a magas rendelkezésre állású aktív-aktív kapcsolatokról: [Magas rendelkezésre állású kapcsolatok létesítmények, illetve virtuális hálózatok között](vpn-gateway-highlyavailable.md).

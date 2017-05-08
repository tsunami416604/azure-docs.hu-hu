---
title: "A helyszíni hálózat csatlakoztatása egy Azure-beli virtuális hálózathoz: Helyek közötti VPN: parancssori felület | Microsoft Docs"
description: "A helyszíni hálózatot az Azure-beli virtuális hálózattal a nyilvános interneten keresztül összekötő IPsec-kapcsolat létrehozásának lépései. Ezen lépéseket követve létrehozhat egy helyek közötti VPN-átjáró kapcsolatot a parancssori felület segítségével."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/24/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: af85e4921a2b81c71f1d132c6df591acbe5d3764
ms.contentlocale: hu-hu
ms.lasthandoff: 04/28/2017


---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal a parancssori felület használatával

Ez a cikk bemutatja, hogyan használhatja az Azure CLI-t egy helyek közötti VPN-átjárókapcsolat létrehozására egy helyszíni hálózat és a Vnet között. A cikkben ismertetett lépések a Resource Manager-alapú üzemi modellre vonatkoznak. Ezt a konfigurációt más üzembehelyezési eszközzel vagy üzemi modellel is létrehozhatja, ha egy másik lehetőséget választ az alábbi listáról:<br>

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Resource Manager – parancssori felület](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Klasszikus – Azure Portal](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Klasszikus – Klasszikus portál](vpn-gateway-site-to-site-create.md)
> 
>


![Helyek közötti VPN Gateway létesítmények közötti kapcsolathoz – diagram](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-connection-diagram.png)

A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím. További információk a VPN-átjárókról: [Információk a VPN Gatewayről](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás megkezdése előtt győződjön meg a következő feltételek teljesüléséről:

* Erősítse meg, hogy a Resource Manager-alapú üzemi modellt kívánja használni. [!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 
* Egy kompatibilis VPN-eszköz és egy azt konfigurálni képes személy. További információk a kompatibilis VPN-eszközökről és az eszközkonfigurációról: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).
* Egy kifelé irányuló, nyilvános IPv4-cím a VPN-eszköz számára. Ez az IP-cím nem lehet NAT mögötti.
* Ha nem ismeri a helyszíni hálózati konfigurációjában található IP-címtereket, egyeztessen valakivel, aki ezeket az adatokat megadhatja Önnek. Amikor létrehozza ezt a konfigurációt, meg kell határoznia az IP-címtartományok előtagjait, amelyeket az Azure majd a helyszínre irányít. A helyszíni hálózat egyik alhálózata sem lehet átfedésben azokkal a virtuális alhálózatokkal, amelyekhez csatlakozni kíván. 
* A CLI-parancsok legújabb verziói (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI 2.0-s verziójának telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI 2.0-s verziójának használatába](/cli/azure/get-started-with-azure-cli).

### <a name="example-values"></a>Példaértékek

Az alábbi értékek használatával létrehozhat egy tesztkörnyezetet, vagy segítségükkel értelmezheti a cikkben szereplő példákat:

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.12.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.12.0.0/24 
GatewaySubnet           = 10.12.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24 
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="Login"></a>1. Csatlakozás az előfizetéshez

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="2-create-a-resource-group"></a>2. Hozzon létre egy erőforráscsoportot

A következő példában létrehozunk egy „TestRG1” nevű erőforráscsoportot az „eastus” helyen. Ha már rendelkezik erőforráscsoporttal abban a régióban, ahol létre kívánja hozni a virtuális hálózatát, használhatja azt is.

```azurecli
az group create --name TestRG1 --location eastus
```

## <a name="VNet"></a>3. Virtuális hálózat létrehozása

Ha még nem rendelkezik virtuális hálózattal, akkor hozzon létre egyet az [az network vnet create](/cli/azure/network/vnet#create) paranccsal. Virtuális hálózat létrehozásakor győződjön meg róla, hogy a megadott címterek nincsenek átfedésben a helyszíni hálózaton található egyéb címterekkel. 

Az alábbi példa létrehoz egy „TestVNet1” nevű virtuális hálózatot és egy „Subnet-1” nevű alhálózatot.

```azurecli
az network vnet create --name TestVNet1 --resource-group TestRG1 --address-prefix 10.12.0.0/16 --location eastus --subnet-name Subnet1 --subnet-prefix 10.12.0.0/24
```

## 4. <a name="gwsub"></a>Az átjáróalhálózat létrehozása

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

Ehhez a konfigurációhoz átjáróalhálózat is szükséges. A virtuális hálózati átjáró átjáróalhálózatot használ, amely a VPN Gateway szolgáltatások által használt IP-címeket tartalmazza. A létrehozott átjáróalhálózatnak a „GatewaySubnet” nevet kell adnia. Ha ezt az alhálózatot máshogy nevezi el, az létrejön ugyan, de az Azure nem kezeli átjáró-alhálózatként.

Az Ön által megadott átjáróalhálózat mérete a létrehozni kívánt VPN-átjárókonfigurációtól függ. Bár akár /29-es átjáróalhálózatot is létrehozhat, javasolt egy ennél nagyobb, több címmel rendelkező alhálózatot létrehozni: /27-eset vagy /28-asat. Nagyobb átjáróalhálózat használatával elegendő IP-cím áll rendelkezésre az esetleges jövőbeni konfigurációk megvalósításához.

Az átjáróalhálózat létrehozásához használja az [az network vnet subnet create](/cli/azure/network/vnet/subnet#create) parancsot.


```azurecli
az network vnet subnet create --address-prefix 10.12.255.0/27 --name GatewaySubnet --resource-group TestRG1 --vnet-name TestVNet1
```

## <a name="localnet"></a>5. A helyi hálózati átjáró létrehozása

A helyi hálózati átjáró általában a helyszínt jelenti. Olyan nevet adjon a helynek, amellyel az Azure hivatkozhat rá, majd határozza meg annak a helyszíni VPN-eszköznek az IP-címét, amellyel létre kívánja hozni a kapcsolatot. Emellett megadhatja azokat az IP-címelőtagokat, amelyek a VPN-átjárón keresztül a VPN-eszközre lesznek irányítva. Az Ön által meghatározott címelőtagok a helyszíni hálózatán található előtagok. A helyszíni hálózat módosításakor az előtagok egyszerűen frissíthetők.

Használja a következő értékeket:

* A helyszíni VPN-eszköz IP-címe: *--gateway-ip-address*. A VPN-eszköz nem lehet NAT mögött.
* A helyszíni címterek: *--local-address-prefixes*.

Az [az network local-gateway create](/cli/azure/network/local-gateway#create) paranccsal hozzáadhat egy helyi hálózati átjárót több címelőtaggal:

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --resource-group TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="PublicIP"></a>6. Nyilvános IP-cím kérése

Egy VPN Gateway-nek rendelkeznie kell nyilvános IP-címmel. Először az IP-cím típusú erőforrást kell kérnie, majd hivatkoznia kell arra, amikor létrehozza a virtuális hálózati átjárót. Az IP-címet a rendszer dinamikusan rendeli hozzá az erőforráshoz a VPN Gateway létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Nem kérheti statikus IP-cím hozzárendelését. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gateway-hez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

Az [az network public-ip create](/cli/azure/network/public-ip#create) paranccsal kérhet dinamikus nyilvános IP-címet.

```azurecli
az network public-ip create --name VNet1GWIP --resource-group TestRG1 --allocation-method Dynamic
```

## <a name="CreateGateway"></a>7. A VPN-átjáró létrehozása

Hozza létre a virtuális hálózat VPN-átjáróját. A VPN-átjáró létrehozása akár 45 percet vagy többet is igénybe vehet.

Használja a következő értékeket:

* A helyek közötti konfiguráció *--gateway-type* paraméterének értéke: *Vpn*. Az átjáró típusa mindig a kiépítendő konfigurációra jellemző. További információért lásd: [Átjárótípusok](vpn-gateway-about-vpn-gateway-settings.md#gwtype).
* A *--vpn-type* a következők valamelyike lehet: *RouteBased* (egyes dokumentumokban Dinamikus átjáró néven szerepel) vagy *PolicyBased* (egyes dokumentumokban Statikus átjáró néven szerepel). A beállítás azon eszköz követelményeire vonatkozik, amelyhez csatlakozik. További információk a VPN-átjárótípusokról: [Információk a VPN Gateway konfigurációs beállításairól](vpn-gateway-about-vpn-gateway-settings.md#vpntype).
* A *--sku* paraméter lehetséges értékei: Alapszintű, Standard vagy HighPerformance. Egyes termékváltozatok konfigurációs korlátokkal rendelkeznek. További információkért lásd: [Az átjárók termékváltozatai](vpn-gateway-about-vpngateways.md#gateway-skus).

Hozza létre a VPN Gateway-t az [az network vnet-gateway create](/cli/azure/network/vnet-gateway#create) paranccsal. Ha ezt a parancsot a „--no-wait” paraméterrel futtatja, nem jelenik meg visszajelzés vagy kimenet. Ez a paraméter lehetővé teszi, hogy az átjáró a háttérben jöjjön létre. Egy átjáró létrehozása nagyjából 45 percet vesz igénybe.

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWIP --resource-group TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku Standard --no-wait 
```

## <a name="VPNDevice"></a>8. VPN-eszköz konfigurálása

[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]
  A virtuális hálózati átjáró IP-címét az [az network public-ip list](/cli/azure/network/public-ip#list) paranccsal keresheti meg. Az olvashatóság érdekében a kimenet táblázatos formában jeleníti meg a nyilvános IP-címek listáját.

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```

## <a name="CreateConnection"></a>9. VPN-kapcsolat létrehozása

Hozzon létre egy helyek közötti VPN-kapcsolatot a virtuális hálózati átjáró és a helyszíni VPN-eszköz között. Különösen figyeljen oda a megosztott kulcs értékére, amelynek meg kell egyeznie a VPN-eszköz konfigurált megosztottkulcs-értékével.

Hozza létre a kapcsolatot az [az network vnet-connection create](/cli/azure/network/vpn-connection#create) paranccsal.

```azurecli
az network vpn-connection create --name VNet1toSite2 -resource-group TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

A kapcsolat rövid időn belül létrejön.

## <a name="toverify"></a>10. A VPN-kapcsolat ellenőrzése

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)] 

Ha másik módszerrel kívánja ellenőrizni a kapcsolatot: [VPN Gateway-kapcsolat ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).

## <a name="common-tasks"></a>Gyakori feladatok

Ez a szakasz a helyek közötti konfigurációk használatakor hasznos gyakori parancsokat tartalmazza. A CLI hálózati parancsainak teljes listájáért lásd az [Azure CLI-hálózatkezelést](/cli/azure/network) bemutató cikket.

[!INCLUDE [local network gateway common tasks](../../includes/vpn-gateway-common-tasks-cli-include.md)] 

## <a name="next-steps"></a>Következő lépések

*  Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. További információkért lásd: [Virtuális gépek](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md).
* Információk a kényszerített bújtatásról: [Kényszerített bújtatás konfigurálása](vpn-gateway-forced-tunneling-rm.md).
* Az Azure CLI hálózati parancsainak listáját lásd: [Azure CLI](https://docs.microsoft.com/cli/azure/network).

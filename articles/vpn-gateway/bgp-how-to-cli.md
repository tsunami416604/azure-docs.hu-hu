---
title: 'Az Azure VPN gateway a BGP konfigurálása: Resource Manager és a parancssori felület |} A Microsoft Docs'
description: Ez a cikk végigvezeti az Azure VPN gateway a BGP konfigurálása Azure Resource Manager és a CLI használatával.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.openlocfilehash: 657cad69fff66aaaeba9819569442981d229f644
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971127"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>BGP konfigurálása Azure VPN Gateway-átjárók a parancssori felület használatával

Ez a cikk segít a Site-to-Site (S2S) VPN-kapcsolatot a létesítmények közötti és VNet – VNet kapcsolat (vagyis a virtuális hálózatok közötti kapcsolat) a BGP engedélyezéséhez az Azure Resource Manager üzemi modell és az Azure CLI használatával.

## <a name="about-bgp"></a>A BGP ismertetése

A BGP útválasztási és elérhetőségi információcserére információcserére két vagy több hálózat között, az interneten gyakran használt szabványos útválasztási protokoll egy. A BGP lehetővé teszi, hogy a VPN-átjárók és a helyszíni VPN-eszközök, néven BGP-társak vagy szomszédok útvonalak cseréjéhez. Az útvonalak átjárók tájékoztatja a rendelkezésre állási és útválasztókon áthaladó az átjárók és az érintett útválasztók előtagok. Tranzit útválasztás több hálózat között, amely BGP-átjáró egy BGP-partner, az összes többi BGP-társak megtanulja az útvonalak propagálásával BGP is engedélyezheti.

További információ előnyeiről BGP, valamint a műszaki követelményeket és szempontokat kell figyelembe vennie a BGP használatával: [BGP áttekintése az Azure VPN-átjárókkal](vpn-gateway-bgp-overview.md).

Ez a cikk segít a következő feladatokat:

* [A VPN Gateway a BGP engedélyezéséhez](#enablebgp) (kötelező)

  Ezután kitöltheti a következő szakaszok egyikét vagy mindkettőt:

* [A BGP létesítmények közötti kapcsolat létrehozása](#crossprembgp)
* [A BGP VNet – VNet kapcsolat létrehozása](#v2vbgp)

Ezek három szakaszra osztható mindegyike forms építőeleme a hálózati kapcsolatot a BGP engedélyezéséhez. Ha elvégezte az összes három szakaszra osztható, létre a topológia az alábbi ábrán látható módon:

![BGP-topológiákkal kapcsolatban](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Ezek a szakaszok hozhat létre egy összetettebb teszi a többszörös ugrásos átviteli hálózat, az igényeinek megfelelő kombinálhatók.

## <a name ="enablebgp"></a>A VPN Gateway a BGP engedélyezése

Ebben a szakaszban szükség, mielőtt hajt végre a lépéseket bármely más két konfigurációs szakaszokat a. Az alábbi konfigurációs lépéseket az Azure VPN gateway a BGP paramétereket állíthatja be az alábbi ábrán látható módon:

![BGP-átjáró](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Előkészületek

Telepítse a CLI-parancsok (2.0-s vagy újabb) legújabb verzióját. A CLI-parancsok telepítéséről további információkért lásd: [az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Azure CLI használatának első lépései](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>1. lépés: TestVNet1 létrehozása és konfigurálása

#### <a name="Login"></a>1. Csatlakozás az előfizetéshez

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Hozzon létre egy erőforráscsoportot

A következő példában létrehozunk egy erőforráscsoportot az "eastus" helyen TestRG1 nevű. Ha már rendelkezik erőforráscsoporttal abban a régióban, ahol szeretné a virtuális hálózat létrehozásához, használhatja helyette, hogy egy is.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. A TestVNet1 létrehozása

Az alábbi példa létrehoz egy TestVNet1 és három alhálózatot nevű virtuális hálózatot: GatewaySubnet, FrontEnd és BackEnd. Ön éppen értékek behelyettesítésekor fontos, mindig az átjáróalhálózat neve GatewaySubnet. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

Az első parancs az előtér-címterek és az előtér-alhálózatot hoz létre. A második parancs létrehoz egy új címteret a háttérrendszer alhálózatának. A negyedik és a harmadik parancs a BackEnd alhálózathoz és az átjáró-alhálózat létrehozása.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. lépés: A VPN-átjáró létrehozása a testvnet1-hez a BGP-paraméterekkel

#### <a name="1-create-the-public-ip-address"></a>1. A nyilvános IP-cím létrehozása

Kérjen egy nyilvános IP-címet. A nyilvános IP-címet oszt ki, amely hoz létre a virtuális hálózat VPN-átjáróhoz.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Az AS-számot a VPN-átjáró létrehozása

Hozza létre a TestVNet1 virtuális hálózati átjáróját. A BGP az Útvonalalapú VPN-átjáró szükséges. A kiegészítő paraméterrel is kell `-Asn` az autonóm rendszer száma (ASN) beállítása a testvnet1-hez. Az átjáró létrehozása eltarthat egy ideig (45 percet vagy többet) végrehajtásához. 

Ha ez a parancs használatával futtatja a `--no-wait` paramétert, nem jelenik meg semmilyen visszajelzés vagy kimenet. A `--no-wait` paraméter lehetővé teszi, hogy az átjáró a háttérben kell létrehozni. Ez nem jelenti azt, hogy a VPN-átjáró létrehozása azonnal.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Az Azure BGP-társ IP-cím beszerzése

Az átjáró létrehozása után kell beszerezni a BGP-társ IP-cím az Azure VPN gatewayen. Ez a cím a VPN-átjáró konfigurálása egy BGP-társ számára a helyszíni VPN-eszközök van szükség.

Futtassa a következő parancsot, és ellenőrizze a `bgpSettings` felső részén a kimeneti szakaszban:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Az átjáró létrehozása után a létesítmények közötti kapcsolat vagy egy BGP-vel VNet – VNet kapcsolat létrehozására használhatja ezt az átjárót.

## <a name ="crossprembgp"></a>A BGP létesítmények közötti kapcsolat létrehozása

Létesítmények közötti kapcsolatot létesíteni, szüksége a helyszíni VPN-eszköz képviselő helyi hálózati átjáró létrehozásához. Az Azure VPN gateway majd a helyi hálózati átjáróhoz csatlakozzon. Bár ezeket a lépéseket más kapcsolatok hasonlít, további tulajdonságokat kötelező megadni a BGP-konfigurációs paramétereket tartalmazzák.

![BGP-t létesítmények közötti esetében](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>1. lépés: Hozzon létre, és a helyi hálózati átjáró konfigurálása

Ebben a gyakorlatban továbbra is össze az ábrán látható. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni. Ha helyi hálózati átjárók dolgozik, vegye figyelembe az alábbiakat:

* A helyi hálózati átjáró lehet az ugyanazon a helyen és az erőforráscsoporthoz tartozik, mint a VPN-átjárót, vagy azok egy másik helyen és erőforráscsoportban. Ez a példa bemutatja az átjárók eltérő erőforráscsoportokban különböző helyeken.
* Deklarálja a helyi hálózati átjáró szükséges minimális előtagként a BGP társ IP-cím a VPN-eszköz gazdagépcímét. Ebben az esetben egy tulajdonságot/32 előtag 10.52.255.254/32.
* Ne feledje a helyszíni hálózatok és az Azure virtuális hálózat között eltérő BGP ASN-eket kell használnia. Ha azonos, módosítsa a virtuális hálózat ASN-t, ha a helyszíni VPN-eszközök már használja az ASN-t más BGP-szomszédok társviszonyt szeretne.

Mielőtt folytatná, győződjön meg arról, hogy végrehajtotta a [BGP engedélyezése a VPN-átjáró](#enablebgp) szakasz ebben a gyakorlatban és, hogy továbbra is csatlakozott az 1. előfizetéshez. Figyelje meg, hogy ebben a példában, akkor hozzon létre egy új erőforráscsoportot. Azt is Észreveheti a helyi hálózati átjáró két további paraméterek: `Asn` és `BgpPeerAddress`.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>2. lépés: A virtuális hálózati átjáró és a helyi hálózati átjáró csatlakoztatása

Ebben a lépésben, a TestVNet1 felől a létre Site5. Meg kell adnia a `--enable-bgp` paraméter BGP engedélyezése ehhez a kapcsolathoz. 

Ebben a példában a virtuális hálózati átjáró és a helyi hálózati átjáró vannak, eltérő erőforráscsoportokban. Amikor az átjárók eltérő erőforráscsoportokban találhatók, meg kell adnia a két átjáró a virtuális hálózatok közötti kapcsolat beállítása a teljes erőforrás-Azonosítóját.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Az azonosító a VNet1GW erőforrás lekérése

Használja a következő parancs kimenete VNet1GW erőforrás-azonosító lekéréséhez:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

A kimenetben keresse meg a `"id":` sor. A következő szakaszban a kapcsolat létrehozásához az idézőjelek között az értékekre szüksége lesz.

Példa a kimenetre:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Után az értékek másolásához `"id":` egy szövegszerkesztőbe, például a Jegyzettömbbe, így könnyen beillesztheti őket a kapcsolat létrehozásakor. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Az erőforrás-azonosító, Site5 beolvasása

A következő parancsot használja az erőforrás-azonosító, Site5 beolvasni a kimenetből:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. A TestVNet1-Site5 kapcsolat létrehozása

Ebben a lépésben, a TestVNet1 felől a létre Site5. Ahogy arra már korábban, lehetőség a BGP- és -BGP kapcsolatot az Azure VPN-átjáróhoz. Kivéve, ha BGP engedélyezve van a kapcsolat tulajdonságban, Azure nem engedélyezi BGP ehhez a kapcsolathoz, annak ellenére, hogy a BGP-paraméterek már konfigurálva vannak az átjárók. Cserélje le az előfizetés azonosítókat a saját.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Ehhez a gyakorlathoz az alábbi példa felsorolja a helyszíni VPN-eszköz a BGP konfigurálási szakaszban adja meg a paramétereket:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A kapcsolat néhány perc alatt létrejön. A BGP társviszony-létesítési munkamenet IPsec-kapcsolat létrejötte után elindul.

## <a name ="v2vbgp"></a>A BGP VNet – VNet kapcsolat létrehozása

Ez a szakasz ad hozzá egy VNet – VNet kapcsolat BGP-vel, az alábbi ábrán látható módon: 

![A VNet – VNet BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Az alábbi utasítások alapján az előző szakaszok lépéseit folytatják. Hozzon létre, és konfigurálja a TestVNet1 és a VPN-átjáró BGP-vel, meg kell adnia a [BGP engedélyezése a VPN-átjáró](#enablebgp) szakaszban.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>1. lépés: A TestVNet2 és a VPN-átjáró létrehozása

Fontos győződjön meg arról, hogy az IP-címtér az új TestVNet2, virtuális hálózat nem átfedésben a VNet-címtartományok.

Ebben a példában a virtuális hálózatok ugyanahhoz az előfizetéshez tartozik. Beállíthatja a VNet – VNet kapcsolatokhoz különböző előfizetések között. További tudnivalókért lásd: [VNet – VNet kapcsolat konfigurálása](vpn-gateway-howto-vnet-vnet-cli.md). Győződjön meg arról, hogy hozzáadhat `-EnableBgp $True` BGP engedélyezése a kapcsolatok létrehozásakor.

#### <a name="1-create-a-new-resource-group"></a>1. Új erőforráscsoport létrehozása

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. TestVNet2 az új erőforráscsoport létrehozása

Az első parancs az előtér-címterek és az előtér-alhálózatot hoz létre. A második parancs létrehoz egy új címteret a háttérrendszer alhálózatának. A negyedik és a harmadik parancs a BackEnd alhálózathoz és az átjáró-alhálózat létrehozása.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. A nyilvános IP-cím létrehozása

Kérjen egy nyilvános IP-címet. A nyilvános IP-címet oszt ki, amely hoz létre a virtuális hálózat VPN-átjáróhoz.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Az AS-számot a VPN-átjáró létrehozása

Hozzon létre a virtuális hálózati átjáró TestVNet2. Az Azure VPN Gateway átjárókról felül kell bírálnia az alapértelmezett ASN-t. Az ASN-eket a csatlakoztatott virtuális hálózatok ahhoz, hogy a BGP és a tranzit útválasztást különbözőnek kell lennie.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>2. lépés: Kapcsolódás a TestVNet1 és TestVNet2 átjárók

Ebben a lépésben, a TestVNet1 felől a létre Site5. Ehhez a kapcsolathoz a BGP engedélyezéséhez meg kell adnia a `--enable-bgp` paraméter.

A következő példában a virtuális hálózati átjáró és a helyi hálózati átjáró a következők eltérő erőforráscsoportokban. Amikor az átjárók eltérő erőforráscsoportokban találhatók, meg kell adnia a két átjáró a virtuális hálózatok közötti kapcsolat beállítása a teljes erőforrás-Azonosítóját. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Az azonosító a VNet1GW erőforrás lekérése 

Az azonosító a VNet1GW erőforrás lekérése a következő parancs kimenetéből származó:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Az erőforrás-azonosító, VNet2GW beolvasása

Az erőforrás-azonosító, VNet2GW lekérése a következő parancs kimenetéből származó:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. A kapcsolatok létrehozása

Hozzon létre a TestVNet1 kapcsolat TestVNet2 és, és a kapcsolat TestVNet2 a testvnet1-et. Cserélje le az előfizetés azonosítókat a saját.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> A BGP engedélyezéséhez *mindkét* kapcsolatok.
> 
> 

Miután végrehajtotta ezeket a lépéseket, néhány perc múlva létrejön a kapcsolat. A BGP társviszony-létesítési munkamenet be lesz a VNet – VNet kapcsolat befejeződése után.

## <a name="next-steps"></a>További lépések

A kapcsolat befejezése után a virtuális hálózatokhoz hozzáadhat virtuális gépeket. Útmutató: [hozzon létre egy virtuális gépet](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

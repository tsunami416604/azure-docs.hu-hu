---
title: 'Az Azure VPN gateway a BGP konfigurálása: erőforrás-kezelő és a parancssori Felülettel |} Microsoft Docs'
description: Ez a cikk végigvezeti az Azure VPN gateway a BGP konfigurálása Azure Resource Manager és a parancssori felület használatával.
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
ms.openlocfilehash: 98cd606ce930624ec5c591ffd8f13e0feae1a6c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884040"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Az Azure VPN gateway a BGP konfigurálása parancssori felület használatával

Ez a cikk segítséget nyújt a BGP engedélyezéséhez a létesítmények közötti pont-pont (S2S) VPN-kapcsolat és egy VNet – VNet-kapcsolatot (Ez azt jelenti, hogy virtuális hálózatok közötti kapcsolatot) az Azure Resource Manager üzembe helyezési modellben és az Azure parancssori felület használatával.

## <a name="about-bgp"></a>A BGP ismertetése

A BGP egy legalább két hálózatot az Útválasztás és elérhetőség adatcserére az interneten általánosan használt a szabványos útválasztási protokoll. A BGP lehetővé teszi, hogy a VPN-átjárók és az exchange-útvonalak BGP-társakat vagy szomszédok, a helyszíni VPN-eszközök. Az útvonalak mindkét átjárók tájékoztatja a rendelkezésre állás és a lépés az átjárók és az útválasztók érintett előtagok elérhetőség. Tranzit útválasztás több hálózat között az útvonalakat a BGP-átjáró minden más BGP-társhoz való egy BGP-partner, a megtanulja a propagálására BGP is engedélyezheti.

További információ előnyeiről BGP, valamint a műszaki követelményeiben és a BGP használatával kapcsolatos szempontok megismerése: [áttekintése a BGP az Azure VPN gatewayek](vpn-gateway-bgp-overview.md).

Ez a cikk segít a következő feladatokat:

* [A BGP engedélyezéséhez a VPN-átjáró](#enablebgp) (kötelező)

  Hajtható majd végre a következő szakaszok egyikét vagy mindkettőt:

* [A BGP-létesítmények közötti kapcsolat létrehozása](#crossprembgp)
* [A BGP VNet – VNet-kapcsolatot létrehozni](#v2vbgp)

Ezek a szakaszok mindegyikének képezi a BGP engedélyezve van a hálózati kapcsolatot az alapvető építőelemre. Minden három szakasz végezze el, ha a topológia elkészítette a következő ábrán látható módon:

![BGP-topológiákkal](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Ezek a szakaszok hozhat létre olyan összetett többszörös ugrásos átmenő hálózatról, az igényeinek megfelelő kombinálhatja.

## <a name ="enablebgp"></a>A BGP engedélyezéséhez a VPN-átjáró

Ebben a szakaszban szükség, mielőtt végrehajtaná lépést a többi két konfigurációs szakaszokat a. Az alábbi konfigurációs lépéseket az Azure VPN gateway a BGP paraméterek beállítása, a következő ábrán látható módon:

![BGP-átjáró](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Előkészületek

Telepítse a legújabb verzióját a parancssori felület parancsait (2.0-s vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI 2.0-s verziójának telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI 2.0-s verziójának használatába](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>1. lépés: Hozzon létre, és TestVNet1 konfigurálása

#### <a name="Login"></a>1. Csatlakozás az előfizetéshez

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Hozzon létre egy erőforráscsoportot

Az alábbi példakód létrehozza a "eastus" helyen TestRG1 nevű erőforráscsoport. Ha már van erőforráscsoport a régióban, ahol szeretne létrehozni a virtuális hálózat, helyette használhatja, hogy egy.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. A TestVNet1 létrehozása

Az alábbi példa létrehoz egy virtuális hálózatot TestVNet1 és három alhálózatok nevű: GatewaySubnet, előtér-, és háttérszolgáltatások. Értékek most és, fontos, hogy mindig nevezze el az átjáró alhálózatának kifejezetten GatewaySubnet. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

Az első parancs létrehozza az előtér-címterületet, valamint a FrontEnd alhálózathoz. A második parancs létrehoz egy további címtartomány a BackEnd alhálózathoz. A negyedik és harmadik parancs a BackEnd alhálózathoz és a GatewaySubnet létrehozása.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. lépés: A VPN-átjáró létrehozása TestVNet1 BGP paraméterekkel

#### <a name="1-create-the-public-ip-address"></a>1. A nyilvános IP-cím létrehozása

Kérjen egy nyilvános IP-címet. A VPN-átjáró, amely a virtuális hálózat létrehozása a nyilvános IP-címet fogja kiosztani.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Hozzon létre a VPN-átjáró AS száma

Hozza létre a TestVNet1 virtuális hálózati átjáróját. A BGP az Útvonalalapú VPN-átjáró szükséges. A kiegészítő paraméterrel is kell `-Asn` az autonóm rendszer számát (ASN) beállítását TestVNet1. Átjáró létrehozása időt vehet igénybe (45 perc vagy több) befejezéséhez. 

Ha ez a parancs használatával futtassa a `--no-wait` paraméter, nem jelenik meg a kimeneti vagy bármely visszajelzést. A `--no-wait` paraméter lehetővé teszi, hogy a háttérben létrehozni az átjárót. Ez nem jelenti azt, hogy a VPN-átjáró azonnal létrejött-e.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Az Azure BGP-társ IP-cím beszerzése

Az átjáró létrehozása után be kell szereznie a BGP-társ IP-cím az Azure VPN-átjárón. Ez a cím konfigurálása a VPN-átjáró BGP-partner a helyszíni VPN-eszközök esetében van szükség.

A következő parancsot, és ellenőrizze a `bgpSettings` szakasz elején található a kimeneti:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Az átjáró létrehozása után a létesítmények közötti kapcsolatot vagy a BGP-VNet – VNet-kapcsolatot létesíteni használhatja ezt az átjárót.

## <a name ="crossprembgp"></a>A BGP-létesítmények közötti kapcsolat létrehozása

A létesítmények közötti kapcsolat létrehozásához szüksége a helyszíni VPN-eszköz képviselő helyi hálózati átjáró létrehozásához. Ezután az Azure VPN gateway csatlakozott a helyi hálózati átjáró. Bár a lépések hasonlóak más kapcsolatok létrehozására, a BGP-konfigurációs paraméterek megadásához szükséges további tulajdonságokat tartalmazzák.

![A létesítmények közötti BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>1. lépés: Hozzon létre, és a helyi hálózati átjáró konfigurálása

Ebben a gyakorlatban továbbra is a konfiguráció az ábrán is látható. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni. Ha a helyi hálózati átjárók dolgozik, vegye figyelembe a következőket:

* A helyi hálózati átjáró lehet a ugyanarra a helyre és az erőforráscsoporthoz tartozik, mint a VPN-átjárót, vagy egy másik helyre, és az erőforráscsoport lehet. Ez a példa bemutatja az átjárók, a különböző erőforráscsoportokra különböző helyeken.
* A minimális előtagját, hogy a helyi hálózati átjáró deklarálnia kell az állomás címe a VPN-eszköz a BGP társ IP-cím. Ebben az esetben egy /32 10.52.255.254/32 előtagja.
* Ne feledje a helyszíni hálózatokhoz és az Azure virtuális hálózat között különböző BGP ASN-eket kell használnia. Ha a egyeznek, a VNet ASN módosításához, ha a helyszíni VPN-eszközök már segítségével az ASN társkapcsolatot létesíteni más BGP szomszédok szüksége.

Mielőtt továbblépne, győződjön meg arról, hogy végrehajtotta a [engedélyezze a BGP a VPN-átjáró](#enablebgp) szakasz ebben a gyakorlatban és, hogy továbbra is kapcsolódik előfizetés 1. Figyelje meg, hogy ebben a példában, akkor hozzon létre egy új erőforráscsoportot. Továbbá figyelje meg, a helyi hálózati átjáró két további paraméterek: `Asn` és `BgpPeerAddress`.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>2. lépés: Csatlakozás a virtuális hálózat átjáró és a helyi hálózati átjáró

Ebben a lépésben hoz létre a kapcsolat TestVNet1 Site5 számára. Meg kell adnia a `--enable-bgp` paraméter a kapcsolat a BGP engedélyezéséhez. 

Ebben a példában a virtuális hálózati átjáró és helyi hálózati átjáró van a különböző erőforráscsoportokra. Ha az átjárók különböző erőforráscsoportokra, meg kell adnia a teljes erőforrás-azonosító, a virtuális hálózatok közötti kapcsolat két átjáró.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Az erőforrás-azonosító a VNet1GW

Használja a következő parancs kimenetében VNet1GW beolvasni az erőforrás-azonosító:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

A kimenetben keresse meg a `"id":` sor. Az értékeket a következő szakasz a VPN-kapcsolat létrehozásához a idézőjelek között van szüksége.

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

Másolja az értékeket után `"id":` egy szövegszerkesztőben, például a Jegyzettömbben az, hogy könnyen illessze be őket a kapcsolat létrehozásakor. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Az erőforrás-azonosító a Site5

Az alábbi parancs segítségével az erőforrás-azonosító a Site5 beszerezni a kimeneti:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. A TestVNet1-Site5 kapcsolat létrehozása

Ebben a lépésben hoz létre a kapcsolat TestVNet1 Site5 számára. A fentiekben taglaltak, akkor lehetséges, hogy az ugyanazon Azure VPN gateway a BGP- és -BGP kapcsolatokat. A BGP engedélyezve van a connection tulajdonság, ha Azure teszik lehetővé a BGP ehhez a kapcsolathoz, annak ellenére, hogy a BGP-paraméterek már be van állítva mindkét átjárókon. Cserélje le az előfizetési azonosítók a saját.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Ebben a gyakorlatban az alábbi példa felsorolja a helyszíni VPN-eszköze a BGP konfigurációs szakaszban adja meg a paramétereket:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A kapcsolat néhány perc alatt létrejön. A BGP társviszony-létesítési munkamenetet IPsec-kapcsolat létrejöttét követően elindul.

## <a name ="v2vbgp"></a>A BGP VNet – VNet-kapcsolatot létrehozni

Ez a szakasz ad hozzá egy VNet – VNet-kapcsolatot, a BGP-vel, az alábbi ábrán látható módon: 

![A VNet – VNet BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Az alábbi utasítások alapján folytatni a fenti szakaszokban ismertetett lépéseket. Szeretne létrehozni és konfigurálni a TestVNet1 és a VPN-átjáró BGP, el kell végeznie a [engedélyezze a BGP a VPN-átjáró](#enablebgp) szakasz.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>1. lépés: TestVNet2 és a VPN-átjáró létrehozása

Győződjön meg arról, hogy az új virtuális hálózat, TestVNet2, az IP-címtér nem fedi át a virtuális hálózat tartomány egyik fontos.

Ebben a példában a virtuális hálózatok ugyanahhoz az előfizetéshez tartozik. Beállíthatja a VNet – VNet kapcsolatokhoz különböző előfizetések között. További tudnivalókért lásd: [VNet – VNet-kapcsolatot konfiguráló](vpn-gateway-howto-vnet-vnet-cli.md). Győződjön meg arról, hogy hozzáadta `-EnableBgp $True` a BGP engedélyezéséhez kapcsolatok létrehozásakor.

#### <a name="1-create-a-new-resource-group"></a>1. Új erőforráscsoport létrehozása

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. TestVNet2 az új erőforráscsoport létrehozása

Az első parancs létrehozza az előtér-címterületet, valamint a FrontEnd alhálózathoz. A második parancs létrehoz egy további címtartomány a BackEnd alhálózathoz. A negyedik és harmadik parancs a BackEnd alhálózathoz és a GatewaySubnet létrehozása.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. A nyilvános IP-cím létrehozása

Kérjen egy nyilvános IP-címet. A VPN-átjáró, amely a virtuális hálózat létrehozása a nyilvános IP-címet fogja kiosztani.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Hozzon létre a VPN-átjáró AS száma

Hozzon létre a virtuális hálózati átjáró TestVNet2. Az Azure VPN gatewayek a felül kell bírálnia az alapértelmezett ASN. Az ASN-eket a csatlakoztatott virtuális hálózatokat a BGP és a tranzit Útválasztás engedélyezése különbözőnek kell lennie.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>2. lépés: Csatlakozás az TestVNet1 és TestVNet2 átjárók

Ebben a lépésben hoz létre a kapcsolat TestVNet1 Site5 számára. A BGP engedélyezéséhez a kapcsolathoz, meg kell adnia a `--enable-bgp` paraméter.

A következő példában a virtuális hálózati átjáró és helyi hálózati átjáró van a különböző erőforráscsoportokra. Ha az átjárók különböző erőforráscsoportokra, meg kell adnia a teljes erőforrás-azonosító, a virtuális hálózatok közötti kapcsolat két átjáró. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Az erőforrás-azonosító a VNet1GW 

Az erőforrás-azonosító a VNet1GW beszerezni a kimenete a következő parancsot:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Az erőforrás-azonosító a VNet2GW

Az erőforrás-azonosító a VNet2GW beszerezni a kimenete a következő parancsot:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. A kapcsolatok létrehozása

Hozzon létre a kapcsolat TestVNet1 és TestVNet2, és a kapcsolat a TestVNet2 TestVNet1. Cserélje le az előfizetési azonosítók a saját.

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

Miután elvégezte ezeket a lépéseket, néhány perc múlva a kapcsolat jön létre. A BGP társviszony-létesítési munkamenet be lesz a VNet – VNet-kapcsolatot befejeződése után.

## <a name="next-steps"></a>Következő lépések

A kapcsolat befejezése után a virtuális gépek a virtuális hálózatokhoz is hozzáadhat. Útmutató: [hozzon létre egy virtuális gépet](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

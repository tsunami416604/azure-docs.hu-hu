---
title: A BGP konfigurálása az Azure VPN-átjárón:CLI
description: Ez a cikk bemutatja a BGP konfigurálását egy Azure VPN-átjáróval az Azure Resource Manager és a CLI használatával.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2018
ms.author: yushwang
ms.openlocfilehash: 42a07ac00fd8a26918164f6547bf57c2b021d14c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863614"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>A BGP konfigurálása Azure VPN-átjárón a CLI használatával

Ez a cikk segít a BGP engedélyezésében egy telephelyközi helyek közötti (S2S) VPN-kapcsolaton és egy virtuális hálózat és virtuális hálózat közötti kapcsolat (azaz a virtuális hálózatok közötti kapcsolat) az Azure Resource Manager telepítési modell és az Azure CLI használatával.

## <a name="about-bgp"></a>A BGP ismertetése

A BGP az a szabványos útválasztási protokoll, amelyet gyakran használnak az interneten két vagy több hálózat közötti útválasztásés elérhetőségi információk cseréjére. A BGP lehetővé teszi, hogy a VPN-átjárók és a helyszíni VPN-eszközök, az úgynevezett BGP-társak vagy szomszédok útvonalakat cseréljenek. Az útvonalak tájékoztatják mindkét átjárót az előtagok elérhetőségéről és elérhetőségéről az érintett átjárókon vagy útválasztókon való átutazáshoz. A BGP több hálózat közötti átmenő útválasztást is képes engedélyezni, ha propagálja azokat az útvonalakat, amelyeket a BGP-átjáró egy BGP-társtól az összes többi BGP-társhoz szerez.

A BGP előnyeiről, valamint a BGP használatával kapcsolatos technikai követelményekről és szempontokról a [BGP áttekintése az Azure VPN-átjárókkal című](vpn-gateway-bgp-overview.md)témakörben olvashat bővebben.

Ez a cikk a következő feladatokelvégzését segíti:

* [BGP engedélyezése a VPN-átjáróhoz](#enablebgp) (kötelező)

  Ezután a következő szakaszok egyikét vagy mindkettőt kivégezheti:

* [Telephelyközi kapcsolat létesítése a BGP-vel](#crossprembgp)
* [Virtuális hálózat és virtuális hálózat kapcsolat létrehozása a BGP-vel](#v2vbgp)

A három szakasz mindegyike alapvető építőelemként szolgáló, a BGP hálózati kapcsolatban való engedélyezéséhez. Ha mindhárom szakaszt kivégezte, a topológiát az alábbi ábrán látható módon kell elkészíteni:

![BGP topológia](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Ezeket a szakaszokat kombinálva összetettebb, az igényeinek megfelelő többhopos tranzithálózatot hozhat létre.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>BGP engedélyezése a VPN-átjáróhoz

Ez a szakasz szükséges, mielőtt a másik két konfigurációs szakasz ban végrehajtott lépések bármelyikét végrehajtana. A következő konfigurációs lépések az Azure VPN-átjáró BGP-paramétereit állították be az alábbi ábrán látható módon:

![BGP-átjáró](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Előkészületek

Telepítse a CLI parancsok legújabb verzióját (2.0 vagy újabb). Információk a CLI-parancsok telepítéséről: [Az Azure CLI telepítése](/cli/azure/install-azure-cli) és [Bevezetés az Azure CLI használatába](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>1. lépés: TestVNet1 létrehozása és konfigurálása

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Csatlakozás az előfizetéshez

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Erőforráscsoport létrehozása

A következő példa létrehoz egy TestRG1 nevű erőforráscsoportot az "eastus" helyen. Ha már van egy erőforráscsoport a régióban, ahol létre szeretné hozni a virtuális hálózatot, használhatja helyette.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Hozzon létre TestVNet1

A következő példa létrehoz egy TestVNet1 nevű virtuális hálózatot és három alhálózatot: GatewaySubnet, FrontEnd és BackEnd. Értékek helyettesítésekénél fontos, hogy mindig az átjáró alhálózatát nevezze el kifejezetten a GatewaySubnet-nek. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul.

Az első parancs létrehozza az előtér-címteret és az Előtér-alhálózatot. A második parancs további címterületet hoz létre a Háttérrendszer alhálózatszámára. A harmadik és a negyedik parancs létrehozza a BackEnd alhálózatot és a GatewaySubnet-et.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. lépés: A TestVNet1 VPN-átjárójának létrehozása BGP-paraméterekkel

#### <a name="1-create-the-public-ip-address"></a>1. A nyilvános IP-cím létrehozása

Kérjen egy nyilvános IP-címet. A nyilvános IP-cím a virtuális hálózathoz létrehozott VPN-átjáróhoz lesz hozzárendelve.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. A VPN-átjáró létrehozása az AS-számmal

Hozza létre a TestVNet1 virtuális hálózati átjáróját. A BGP-hez útvonalalapú VPN-átjáró szükséges. A testvnet1 `-Asn` autonóm rendszerszám (ASN) beállításához további paraméterre is szükség van. Az átjáró létrehozása eltarthat egy ideig (45 perc vagy több) a befejezéshez. 

Ha ezt a parancsot `--no-wait` a paraméter használatával futtatja, nem jelenik meg semmilyen visszajelzés vagy kimenet. A `--no-wait` paraméter lehetővé teszi az átjáró létrehozását a háttérben. Ez nem jelenti azt, hogy a VPN-átjáró azonnal létrejön.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Az Azure BGP-társ IP-címének beszerzése

Az átjáró létrehozása után be kell szereznie a BGP-társ IP-címét az Azure VPN-átjárón. Ez a cím szükséges a VPN-átjáró konfigurálásához a helyszíni VPN-eszközök BGP-társként való konfigurálásához.

Futtassa a következő `bgpSettings` parancsot, és ellenőrizze a kimenet tetején lévő szakaszt:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Az átjáró létrehozása után ezen az átjárón létrehozhat egy telephelyközi kapcsolatot vagy egy Virtuális hálózat és virtuális hálózat közötti kapcsolatot a BGP-vel.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>Telephelyközi kapcsolat létesítése a BGP-vel

A létesítmények közötti kapcsolat létrehozásához létre kell hoznia egy helyi hálózati átjárót a helyszíni VPN-eszköz ábrázolására. Ezután csatlakoztassa az Azure VPN-átjárót a helyi hálózati átjáróval. Bár ezek a lépések hasonlóak más kapcsolatok létrehozásához, a BGP konfigurációs paraméterek megadásához szükséges további tulajdonságokat tartalmazzák.

![BGP a telephelyek közötti](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>1. lépés: A helyi hálózati átjáró létrehozása és konfigurálása

Ez a gyakorlat folytatja az ábrán látható konfiguráció létrehozását. Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni. Ha helyi hálózati átjárókkal dolgozik, tartsa szem előtt az alábbi dolgokat:

* A helyi hálózati átjáró lehet a VPN-átjáróval azonos helyen és erőforráscsoportban, vagy lehet egy másik helyen és erőforráscsoportban. Ez a példa a különböző helyeken lévő különböző erőforráscsoportokátjáróit mutatja be.
* A helyi hálózati átjáróhoz deklarálandó minimális előtag a VpN-eszközön található BGP-társ IP-címének állomáscíme. Ebben az esetben ez egy /32 előtag 10.51.255.254/32.
* Emlékeztetőül kell használnia a különböző BGP ASN-ek között a helyszíni hálózatok és az Azure virtuális hálózat. Ha megegyeznek, módosítania kell a virtuális hálózat ASN-jét, ha a helyszíni VPN-eszközök már használják az ASN-t más BGP-szomszédok társviszony-lecseréléséhez.

Mielőtt folytatná, győződjön meg arról, hogy befejezte a [BGP engedélyezése a VPN-átjáró](#enablebgp) szakasza ebben a gyakorlatban, és hogy még mindig csatlakozik az Előfizetés 1. Figyelje meg, hogy ebben a példában új erőforráscsoportot hoz létre. Figyelje meg a helyi hálózati átjáró két `Asn` `BgpPeerAddress`további paraméterét is: és .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>2. lépés: A virtuális hálózat és a helyi hálózati átjáró csatlakoztatása

Ebben a lépésben hozza létre a kapcsolatot testvnet1 a site5. Meg kell `--enable-bgp` adnia a paramétert a BGP engedélyezéséhez ehhez a kapcsolathoz. 

Ebben a példában a virtuális hálózati átjáró és a helyi hálózati átjáró különböző erőforráscsoportokban vannak. Ha az átjárók különböző erőforráscsoportokban vannak, meg kell adnia a két átjáró teljes erőforrás-azonosítóját a virtuális hálózatok közötti kapcsolat beállításához.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. A VNet1GW erőforrás-azonosítójának beszereznie

A vnet1GW erőforrás-azonosítójának lekérnie a következő parancs kimenetét használja:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

A kimenetben keresse `"id":` meg a vonalat. A kapcsolat létrehozásához a következő szakaszban az idézőjelek között lévő értékekre van szükség.

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

Másolja az `"id":` értékeket egy szövegszerkesztőbe, például a Jegyzettömbbe, hogy a kapcsolat létrehozásakor könnyen beilleszthesse őket. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Az 5.

A következő paranccsal leszeretné késelni az 5.

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. A TestVNet1–Site5 kapcsolat létrehozása

Ebben a lépésben hozza létre a kapcsolatot testvnet1 a site5. Ahogy azt korábban már tárgyaltuk, lehetőség van bgp- és nem BGP-kapcsolatok ra ugyanazon Azure VPN-átjáróhoz. Ha a BGP nincs engedélyezve a kapcsolat tulajdonság, az Azure nem engedélyezi a BGP-t ehhez a kapcsolathoz, annak ellenére, hogy a BGP-paraméterek már konfigurálva vannak mindkét átjárón. Cserélje le az előfizetés-azonosítókat a sajátjára.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Ebben a gyakorlatban a következő példa felsorolja a helyszíni VPN-eszköz BGP-konfigurációs szakaszában beírandó paramétereket:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

A kapcsolat néhány perc alatt létrejön. A BGP-társviszony-létesítési munkamenet az IPsec-kapcsolat létrehozása után kezdődik.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Virtuális hálózat és virtuális hálózat kapcsolat létrehozása a BGP-vel

Ez a szakasz virtuális hálózatról virtuális hálózatra létesítő kapcsolatot ad hozzá a BGP-vel, az alábbi ábrán látható módon: 

![BGP a virtuális hálózat-vnet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Az alábbi utasítások az előző szakaszokban található lépésekből folytatódnak. A TestVNet1 és a VPN-átjáró BGP-vel való létrehozásához és konfigurálásához be kell fejeznie a [BGP engedélyezése a VPN-átjáróhoz](#enablebgp) szakaszt.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>1. lépés: TestVNet2 és a VPN-átjáró létrehozása

Fontos, hogy győződjön meg arról, hogy az új virtuális hálózat, a TestVNet2 IP-címtere nem fedi át a virtuális hálózat egyik tartományát sem.

Ebben a példában a virtuális hálózatok ugyanahhoz az előfizetéshez tartoznak. Virtuális hálózat és virtuális hálózat közötti kapcsolatokat állíthat be a különböző előfizetések között. További információ: [VNet-to-VNet-kapcsolat konfigurálása.](vpn-gateway-howto-vnet-vnet-cli.md) Győződjön meg `-EnableBgp $True` arról, hogy a BGP engedélyezéséhez való kapcsolatok létrehozásakor adja hozzá.

#### <a name="1-create-a-new-resource-group"></a>1. Új erőforráscsoport létrehozása

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Hozzon létre TestVNet2 az új erőforráscsoportban

Az első parancs létrehozza az előtér-címteret és az Előtér-alhálózatot. A második parancs további címterületet hoz létre a Háttérrendszer alhálózatszámára. A harmadik és a negyedik parancs létrehozza a BackEnd alhálózatot és a GatewaySubnet-et.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. A nyilvános IP-cím létrehozása

Kérjen egy nyilvános IP-címet. A nyilvános IP-cím a virtuális hálózathoz létrehozott VPN-átjáróhoz lesz hozzárendelve.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. A VPN-átjáró létrehozása az AS-számmal

Hozza létre a TestVNet2 virtuális hálózati átjáróját. Felül kell bírnod az alapértelmezett ASN-t az Azure VPN-átjáróin. A csatlakoztatott virtuális hálózatok ASN-ek eltérőek kell, hogy legyenek a BGP és a tranzitútválasztás engedélyezéséhez.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>2. lépés: A TestVNet1 és a TestVNet2 átjárók csatlakoztatása

Ebben a lépésben hozza létre a kapcsolatot testvnet1 a site5. A BGP engedélyezéséhez meg kell `--enable-bgp` adnia a paramétert.

A következő példában a virtuális hálózati átjáró és a helyi hálózati átjáró különböző erőforráscsoportokban vannak. Ha az átjárók különböző erőforráscsoportokban vannak, meg kell adnia a két átjáró teljes erőforrás-azonosítóját a virtuális hálózatok közötti kapcsolat beállításához. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. A VNet1GW erőforrás-azonosítójának beszereznie 

A VNet1GW erőforrás-azonosítójának beszereznie a következő parancs kimenetéből:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. A VNet2GW erőforrás-azonosítójának beszereznie

A VNet2GW erőforrás-azonosítójának beszereznie a következő parancs kimenetéből:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Hozza létre a kapcsolatokat

Hozza létre a kapcsolatot a TestVNet1 és a TestVNet2 között, valamint a TestVNet2 és a TestVNet1 között. Cserélje le az előfizetés-azonosítókat a sajátjára.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> BGP engedélyezése *mindkét* kapcsolathoz.
> 
> 

A lépések elvégzése után a kapcsolat néhány percen belül létrejön. A BGP-társviszony-létesítési munkamenet a virtuális hálózat és a virtuális hálózat között létesítési kapcsolat befejezése után fog befejeződni.

## <a name="next-steps"></a>További lépések

A kapcsolat létrejötte után virtuális gépeket adhat hozzá a virtuális hálózatokhoz. Lépések: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

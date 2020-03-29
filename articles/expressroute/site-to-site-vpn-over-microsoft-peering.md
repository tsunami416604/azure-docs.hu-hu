---
title: 'Azure ExpressRoute: Az S2S VPN konfigurálása a Microsoft társviszony-létesítésén keresztül'
description: Konfigurálja az IPsec/IKE-kapcsolatot az Azure-ral egy ExpressRoute-alapú Microsoft-társviszony-létesítési kapcsolati kapcsolaton keresztül egy helyek közötti VPN-átjáró használatával.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f3044a2701b0f1cd0e5f9ab3ab60c1d60cfb8f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436814"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Helyek közötti VPN konfigurálása ExpressRoute-on Microsoft társviszony-létesítés

Ez a cikk segít a helyszíni hálózat és az Azure virtuális hálózatok (VNets) közötti biztonságos titkosított kapcsolat konfigurálásában ExpressRoute-alapú privát kapcsolaton keresztül. A Microsoft társviszony-létesítés segítségével hozhat létre egy hely-to-site IPsec/IKE VPN-alagút a kiválasztott helyszíni hálózatok és az Azure virtuális hálózatok között. A biztonságos alagút konfigurálása az ExpressRoute-on lehetővé teszi az adatcserét titkossággal, visszajátszás-ellenességgel, hitelességgel és sértetlenséggel.

>[!NOTE]
>Ha a Microsoft-társviszony-létesítésen keresztül állítja be a helyek közötti VPN-t, a VPN-átjáróért és a VPN-kimenő forgalomért díjat kell fizetnie. További információ: [VPN Gateway pricing](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>Architektúra


  ![kapcsolat – áttekintés](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


A magas rendelkezésre állás és a redundancia érdekében több alagutat is konfigurálhat egy ExpressRoute-kapcsolat két MSEE-PE párján, és engedélyezheti a terheléselosztást az alagutak között.

  ![magas rendelkezésre állási lehetőségek](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

A Microsoft-társviszony-létesítésen keresztüli VPN-alagutak letilthatók a VPN-átjáró használatával, vagy az Azure Marketplace-en elérhető megfelelő hálózati virtuális berendezés (NVA) használatával. Az útvonalakat statikusan vagy dinamikusan cserélheti a titkosított alagutakon anélkül, hogy az útvonalcserét az alapul szolgáló Microsoft-társviszony-létesítésnek tenné ki. Ebben a cikkben a BGP (eltér a Microsoft társviszony-létesítéshez használt BGP-munkamenettől) dinamikusan cserélhet előtagokat a titkosított alagutakon keresztül.

>[!IMPORTANT]
>A helyszíni oldalon általában a Microsoft-társviszony-létesítés leáll a DMZ és a privát társviszony-létesítés leáll a törzs hálózati zónában. A két zóna tűzfalakkal lenne elkülönítve. Ha a Microsoft társviszony-létesítése kizárólag a biztonságos bújtatás engedélyezése expressRoute-on keresztül, ne feledje, hogy szűrje át csak a nyilvános IP-k, amelyek egyre hirdetett microsoftos társviszony-létesítési keresztül.
>
>

## <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

1. Konfigurálja a Microsoft társviszony-létesítését az ExpressRoute-kapcsolatcsoporthoz.
2. A microsoftos társviszony-létesítésen keresztül hirdethet kiválasztott Azure regionális nyilvános előtagokat a helyszíni hálózatra.
3. VPN-átjáró konfigurálása és IPsec-alagutak létrehozása
4. Konfigurálja a helyszíni VPN-eszközt.
5. Hozza létre a helyek közötti IPsec/IKE-kapcsolatot.
6. (Nem kötelező) Konfigurálja a tűzfalakat/szűrést a helyszíni VPN-eszközön.
7. Tesztelje és érvényesítse az IPsec-kommunikációt az ExpressRoute-kapcsolaton keresztül.

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1. A Microsoft társviszony-létesítéskonfigurálása

A helyek közötti VPN-kapcsolat ExpressRoute-on keresztüli konfigurálásához ki kell használnia az ExpressRoute Microsoft társviszony-létesítését.

* Új ExpressRoute-kapcsolat konfigurálásához kezdje az [ExpressRoute előfeltételeiről](expressroute-prerequisites.md) szóló cikket, majd [hozzon létre és módosítson egy ExpressRoute-áramkört.](expressroute-howto-circuit-arm.md)

* Ha már rendelkezik ExpressRoute-kapcsolati kapcsolatokkal, de nincs konfigurálva a Microsoft társviszony-létesítése, konfigurálja a Microsoft társviszony-létesítést az [ExpressRoute-kapcsolatlétesítés létrehozása és módosítása segítségével egy ExpressRoute-kapcsolatlétesítési cikkhez.](expressroute-howto-routing-arm.md#msft)

Miután konfigurálta a körhálózatot és a Microsoft-társviszony-létesítést, könnyedén megtekintheti azt az Azure Portal **Áttekintés lapján.**

![Áramkör](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2. Útvonalszűrők konfigurálása

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Ez lényegében egy engedélyezési lista az összes BGP közösségi értékeket. 

![útvonalszűrő](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

Ebben a példában a központi telepítés csak az *Azure West US 2* régióban. Egy útvonalszűrő-szabály hozzáadódik, hogy csak az Azure West USA 2 regionális előtagok hirdetését engedélyezze, amely a BGP-közösség *12076:51026*közösségi értéke. A Szabály kezelése lehetőség kiválasztásával megadhatja az engedélyezni kívánt területi **előtagokat.**

Az útvonalszűrőn belül ki kell választania azokat az ExpressRoute-áramköröket is, amelyekre az útvonalszűrő vonatkozik. Az ExpressRoute-áramkörök et az **Áramkör hozzáadása**lehetőséget választva választhatja ki. Az előző ábrán az útvonalszűrő az ExpressRoute-körhöz van társítva.

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2.1 Az útvonalszűrő konfigurálása

Útvonalszűrő konfigurálása. A lépéseket a [Microsoft társviszony-létesítési útvonalszűrőinek konfigurálása](how-to-routefilter-portal.md)című témakörben található.

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2.2 A BGP-útvonalak ellenőrzése

Miután sikeresen létrehozta a Microsoft társviszony-létesítését az ExpressRoute-kapcsolaton keresztül, és hozzárendelt egy útvonalszűrőt az áramkörhöz, ellenőrizheti a kis- és nagytest-összeget a kis- és nagytest-összeget létesítő pe-eszközökről kapott BGP-útvonalakat. Az ellenőrzési parancs a PE-eszközök operációs rendszerétől függően változik.

#### <a name="cisco-examples"></a>Cisco példák

Ez a példa Cisco IOS-XE parancsot használ. A példában egy virtuális útválasztási és továbbítási (VRF) példány a társviszony-létesítési forgalom elkülönítésére szolgál.

```
show ip bgp vpnv4 vrf 10 summary
```

A következő részleges kimenet azt mutatja, hogy 68 előtag érkezett a szomszédtól \*.243.229.34 az ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

A szomszédtól kapott előtagok listájának megtekintéséhez használja a következő példát:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Annak ellenőrzéséhez, hogy a megfelelő előtagokat kapja-e, ellenőrizheti a keresztellenőrzést. A következő Azure PowerShell-parancskimenet felsorolja a Microsoft-társviszony-létesítésen keresztül hirdetett előtagokat az egyes szolgáltatásokhoz és az Azure-régiókhoz:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3. Konfigurálja a VPN-átjárót és az IPsec-alagutakat

Ebben a szakaszban az IPsec VPN-alagutak jönnek létre az Azure VPN-átjáró és a helyszíni VPN-eszköz között. A példák a Cisco Cloud Service Router (CSR1000) VPN-eszközeit használják.

Az alábbi ábrán a helyszíni VPN-eszköz 1 és az Azure VPN-átjárópéldány pár között létrehozott IPsec VPN-bújtatások láthatók. A helyszíni VPN-eszköz 2 és az Azure VPN-átjárópéldány-pár között létrehozott két IPsec-VPN-bújtatás nem jelenik meg az ábrán, és a konfigurációs részletek nem szerepelnek. Azonban a további VPN-alagutak javítása a magas rendelkezésre állást.

  ![VPN-alagutak](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Az IPsec-alagútpáron keresztül eBGP-munkamenet jön létre a magánhálózati útvonalak cseréjéhez. Az alábbi ábra az IPsec-alagútpár felett létrehozott eBGP-munkamenetet mutatja be:

  ![eBGP-munkamenetek alagútpáron](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Az alábbi ábra a példahálózat absztrakt áttekintését mutatja be:

  ![példa hálózat](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Példák az Azure Resource Manager sablonról

A példákban a VPN-átjáró és az IPsec-alagút végződések konfigurálva egy Azure Resource Manager sablon használatával. Ha még nem használja az Erőforrás-kezelő sablonokat, vagy ismeri az Erőforrás-kezelő sablon alapjait, [olvassa el az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakört.](../azure-resource-manager/templates/template-syntax.md) Ebben a szakaszban a sablon zöldmezős Azure-környezetet (VNet) hoz létre. Azonban ha rendelkezik egy meglévő virtuális hálózat, hivatkozhat rá a sablonban. Ha nem ismeri a [VPN-átjáró](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)IPsec/IKE helyek közötti konfigurációit, olvassa el a Helyek közötti kapcsolat létrehozása című témakört.

>[!NOTE]
>A konfiguráció létrehozásához nem kell Azure Resource Manager-sablonokat használnia. Ezt a konfigurációt az Azure Portalon vagy a PowerShell használatával hozhatja létre.
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3.1 A változók deklarálása

Ebben a példában a változó deklarációk megfelelnek a példa hálózat. Változók deklarálásakor módosítsa ezt a szakaszt a környezetének megfelelően.

* A **variable localAddressPrefix** egy helyszíni IP-címek tömbje az IPsec-alagutak leállításához.
* A **gatewaySku** határozza meg a VPN átviteli. A gatewaySku és a vpnType alkalmazásról a [VPN-átjáró konfigurációs beállításai című](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)témakörben olvashat bővebben. Az árakról a [VPN-átjáró díjszabása](https://azure.microsoft.com/pricing/details/vpn-gateway)látható.
* Állítsa a **vpnType-ot** **RouteBased -re.**

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3.2 Virtuális hálózat létrehozása (VNet)

Ha egy meglévő virtuális hálózatot társít a VPN-alagutakkal, kihagyhatja ezt a lépést.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3.3 Nyilvános IP-címek hozzárendelése VPN-átjárópéldányokhoz
 
Rendeljen nyilvános IP-címet a VPN-átjáró minden egyes példányához.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3.4 Adja meg a helyszíni VPN-alagút befejezését (helyi hálózati átjáró)

A helyszíni VPN-eszközöket helyi hálózati **átjárónak**nevezzük. A következő jsonkódrészlet a távoli BGP-társ adatait is meghatározza:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3.5 A VPN-átjáró létrehozása

A sablon ezen szakasza konfigurálja a VPN-átjárót az aktív-aktív konfigurációhoz szükséges beállításokkal. Ne feledje az alábbi követelményeket:

* Hozza létre a VPN-átjárót egy **"RouteBased"** VpnType típussal. Ez a beállítás kötelező, ha engedélyezni szeretné a BGP-útválasztást a VPN-átjáró és a helyszíni VPN között.
* A VPN-átjáró két példánya és egy adott helyszíni eszköz aktív-aktív módban történő létrehozása érdekében az **"activeActive"** paraméter **true** értékre van állítva az Erőforrás-kezelő sablonban. Ha többet szeretne megtudni a magas rendelkezésre állású VPN-átjárókról, olvassa [el a Magas rendelkezésre állású VPN-átjáró-kapcsolat.](../vpn-gateway/vpn-gateway-highlyavailable.md)
* A VPN-alagutak közötti eBGP-munkamenetek konfigurálásához mindkét oldalon két különböző ASN-t kell megadnia. Célszerű megadni a privát ASN-számokat. További információ: [BGP- és Azure VPN-átjárók áttekintése.](../vpn-gateway/vpn-gateway-bgp-overview.md)

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3.6 Az IPsec-alagutak létrehozása

A parancsfájl végső művelete IPsec-alagutakat hoz létre az Azure VPN-átjáró és a helyszíni VPN-eszköz között.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4. Konfigurálja a helyszíni VPN-eszközt

Az Azure VPN-átjáró kompatibilis számos VPN-eszközzel különböző gyártóktól. A VPN-átjáróval való együttműködésre ellenőrzött konfigurációs információkat és eszközöket [a VPN-eszközök – betekintés című témakörben talál.](../vpn-gateway/vpn-gateway-about-vpn-devices.md)

A VPN-eszköz konfigurálásakor a következő elemekre van szükség:

* Megosztott kulcs. Ez ugyanaz a megosztott kulcs, amelyet a helyek közötti VPN-kapcsolat létrehozásakor megadott. A példák egy alapvető megosztott kulcsot használnak. Javasoljuk egy ennél összetettebb kulcs létrehozását.
* A VPN-átjáró nyilvános IP-címe. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. A VPN-átjáró nyilvános IP-címének Azure Portal használatával történő megkereséséhez válassza a Virtual network gateways (Virtuális hálózati átjárók) elemet, majd kattintson az átjárója nevére.

Az eBGP-partnerek általában közvetlenül kapcsolódnak (gyakran WAN-kapcsolaton keresztül). Ha azonban az eBGP-t IPsec VPN-alagutakon keresztül konfigurálja expressroute-i Microsoft társviszony-létesítésen keresztül, az eBGP-partnerek között több útválasztási tartomány van. Az **ebgp-multihop** paranccsal hozza létre az eBGP szomszédkapcsolatot a két nem közvetlenül csatlakoztatott társ között. Az ebgp-multihop parancsot követő egész szám a BGP-csomagok TTL értékét adja meg. Az **ebbgp 2 parancs maximális elérési útjalehetővé** teszi a két BGP-útvonal közötti forgalom terheléselosztását.

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Példa Cisco CSR1000

A következő példa bemutatja a Cisco CSR1000 konfigurációját egy Hyper-V virtuális gépen, mint a helyszíni VPN-eszközt:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5. A VPN-eszközszűrés és a tűzfalak konfigurálása (nem kötelező)

Konfigurálja a tűzfalat és a szűrést az Ön igényeinek megfelelően.

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6. Tesztelje és érvényesítse az IPsec-alagutat

Az IPsec-alagutak állapotát powershell-parancsokkal ellenőrizhetik az Azure VPN-átjárón:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Példa a kimenetre:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Az Azure VPN-átjárópéldányokon lévő alagutak állapotának független ellenőrzéséhez használja a következő példát:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Példa a kimenetre:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

A helyszíni VPN-eszközön ellenőrizheti az alagút állapotát is.

Cisco CSR1000 példa:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Példa a kimenetre:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

A virtuális alagút kapcsolatának (VTI) vonalprotokollja nem változik "fel" értékre, amíg az IKE 2. A következő parancs ellenőrzi a biztonsági társítást:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verify-end-to-end-connectivity-between-the-inside-network-on-premises-and-the-azure-vnet"></a><a name="verifye2e"></a>A helyszíni belső hálózat és az Azure virtuális hálózat közötti végpontok közötti kapcsolat ellenőrzése

Ha az IPsec-alagutak felvannak állítva, és a statikus útvonalak megfelelően vannak beállítva, akkor a távoli BGP-társ IP-címét pingelheti:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>A BGP-munkamenetek ellenőrzése IPsec-en keresztül

Az Azure VPN-átjárón ellenőrizze a BGP-társ állapotát:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Példa a kimenetre:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Az eBGP-n keresztül a VPN-koncentrátor helyszíni rendszeréből kapott hálózati előtagok listájának ellenőrzéséhez szűrhet az "Origin" attribútum szerint:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

A példakimeneti, az ASN 65010 a BGP autonóm rendszer száma a VPN helyszíni.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

A meghirdetett útvonalak listájának megtekintése:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Példa a kimenetre:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Példa a helyszíni Cisco CSR1000-re:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

A helyszíni Cisco CSR1000 és az Azure VPN-átjáró között meghirdetett hálózatok listája a következő paranccsal sorolható:

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>További lépések

* [Network Performance Monitor for ExpressRoute konfigurálása](how-to-npm.md)

* [Helyek közötti kapcsolat hozzáadása meglévő VPN-átjárókapcsolattal rendelkező virtuális hálózathoz](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)

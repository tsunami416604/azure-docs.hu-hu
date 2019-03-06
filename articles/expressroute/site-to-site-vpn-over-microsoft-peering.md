---
title: Site-to-site VPN konfigurálása a Microsoft Azure – az ExpressRoute - társviszony-létesítés feletti |} A Microsoft Docs
description: Site-to-site VPN gateway használatával egy ExpressRoute Microsoft társviszony-létesítési kapcsolatcsoport keresztül az Azure IPsec/IKE-kapcsolat konfigurálása.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f35ed65b25d469b524e7174affecb45ad7c4735c
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405868"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Site-to-site VPN konfigurálása ExpressRoute Microsoft társviszony-létesítésen keresztül

Ez a cikk segít a helyszíni hálózat és az Azure virtuális hálózatok (Vnetek) között biztonságos, titkosított kapcsolat konfigurálása egy privát ExpressRoute-kapcsolaton keresztül. A Microsoft társviszony-létesítést úgy, hogy ki a kijelölt helyszíni hálózatokhoz és az Azure virtuális hálózatok között helyek közötti IPsec/IKE VPN-alagutat is használhatja. Egy biztonságos alagúton konfigurálása expressroute-on keresztül lehetővé teszi, hogy az adatcseréhez bizalmas, visszajátszás, hitelességét és integritását.

>[!NOTE]
>Amikor beállít site-to-site VPN keresztül a Microsoft társviszony-létesítés, díjkötelesek a VPN-átjáró és a VPN-forgalom. További információkért lásd: [VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="architecture"></a>Architektúra


  ![kapcsolat – áttekintés](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Magas rendelkezésre állás és redundancia több alagút konfigurálása az ExpressRoute-kapcsolatcsoport két MSEE-PE pár keresztül, és lehetővé teszik a terheléselosztást az alagutak között.

  ![magas rendelkezésre állású lehetőségek](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

A Microsoft társviszony-létesítésen keresztül VPN-alagutat állíthatók le vagy VPN-átjáró használatával, vagy egy megfelelő hálózati virtuális készüléket (NVA) elérhető használatával az Azure Marketplace-en keresztül. Is útvonalakat cserél statikusan vagy dinamikusan a titkosított alagutakon keresztül anélkül, hogy a mögöttes Microsoft társviszony-létesítési útvonal cseréjét. A példákban Ez a cikk a BGP (a Microsoft társviszony-létesítés létrehozásához használt BGP-munkamenet eltérő) segítségével dinamikusan exchange-előtagokat a titkosított alagutakon keresztül.

>[!IMPORTANT]
>A helyi oldal általában a DMZ-t a Microsoft társviszony-létesítés megszakad, és privát társviszony-létesítés megszakad a központi hálózat zónára. A két zónák sorba kerül tűzfalakat használ. A Microsoft társviszony-létesítés kizárólag az expressroute-on keresztül biztonságos bújtatás engedélyezése konfigurálásakor, ne feledje, csak a nyilvános IP-címek a Microsoft társviszony-létesítésen keresztül meghirdetett első lényeges szűrése.
>
>

## <a name="workflow"></a>A munkafolyamat

1. Konfigurálja a Microsoft az ExpressRoute-kapcsolatcsoport társviszony-létesítést.
2. Hirdesse meg a kiválasztott Azure regionális nyilvános előtagok a Microsoft társviszony-létesítésen keresztül a helyszíni hálózathoz.
3. VPN-átjáró konfigurálása és az IPsec-alagutakat
4. A helyszíni VPN-eszköz konfigurálásához.
5. A helyek közötti IPsec/IKE-kapcsolat létrehozásához.
6. (Nem kötelező) Tűzfalak/szűrésének konfigurálása a helyszíni VPN-eszköz.
7. És az IPsec-kommunikáció hitelesíti az ExpressRoute-kapcsolatcsoport keresztül.

## <a name="peering"></a>1. Konfigurálja a Microsoft társviszony-létesítés

Site-to-site VPN-kapcsolat konfigurálása expressroute-on keresztül, akkor kell kihasználva ExpressRoute Microsoft társviszony-létesítés.

* Egy új ExpressRoute-kapcsolatcsoport beállításához kezdje a [ExpressRoute-Előfeltételek](expressroute-prerequisites.md) a cikkben, majd [létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport](expressroute-howto-circuit-arm.md).

* Ha már rendelkezik ExpressRoute-kapcsolatcsoport, de nem rendelkezik Microsoft társviszony-létesítés konfigurált, konfigurálja a Microsoft társviszony-létesítés használatával a [létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport társviszony-létesítést](expressroute-howto-routing-arm.md#msft) cikk.

Miután konfigurálta a kapcsolatcsoportot, és Microsoft társviszony-létesítés, egyszerűen megtekintheti a a **áttekintése** oldal az Azure Portalon.

![kapcsolatcsoport](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2. Útvonalszűrők konfigurálása

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Lényegében minden BGP-Közösség értékét engedélyezett legyen. 

![útvonalszűrő](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

Ebben a példában az üzembe helyezés nem csak a a *Azure West US 2* régióban. Csak a hirdetés az Azure West US 2 regionális előtagok, amely rendelkezik a BGP-közösségérték engedélyezéséhez felvettek egy útvonalszűrő-szabály *12076:51026*. Azt adja meg a kiválasztásával engedélyezheti a regionális előtagjait **kezelés szabály**.

Az útvonalszűrőt belül, is kell választania az ExpressRoute-Kapcsolatcsoportok, amelynek az útvonalszűrőt vonatkozik. Az ExpressRoute-Kapcsolatcsoportok választhat kiválasztásával **hozzáadni a kapcsolatcsoportot**. Az előző ábrán az útvonalszűrőt az ExpressRoute-kapcsolatcsoport példa társítva.

### <a name="configfilter"></a>2.1 útvonalszűrőt konfigurálása

Egy útvonalszűrőhöz konfigurálása. Útmutató: [útvonalszűrőket konfigurálása Microsoft társviszony-létesítés](how-to-routefilter-portal.md).

### <a name="verifybgp"></a>2.2-es ellenőrizze a BGP-útvonalak

Miután sikeresen a Microsoft társviszony-létesítéshez az ExpressRoute-kapcsolatcsoport létrehozása és egy útvonalszűrőhöz társított a kapcsolatcsoportot, ellenőrizheti a BGP-útvonalak kapott Msee vannak az msee-k a társviszony-létesítés PE eszközökön. Az ellenőrzési parancs PE eszközt, az operációs rendszer függően változik.

#### <a name="cisco-examples"></a>Cisco-példák

Ebben a példában egy Cisco IOS-XE parancsot használja. A példában egy virtuális Útválasztás és a továbbítás (VRF) példány segítségével a társviszony-létesítési forgalom elkülönítése.

```
show ip bgp vpnv4 vrf 10 summary
```

A következő részleges kimenet látható, hogy 68 előtagok érkezett a szomszédos \*.243.229.34 és az ASN 12076 számot használja (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

A szomszédos fogadott előtagok listájának megtekintéséhez, használja a következő példát:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Győződjön meg arról, hogy a helyes beállítása az előtagok azért küldtük Önnek, hogy cross-ellenőrizheti. A következő Azure PowerShell-parancs kimenete listázza a Microsoft társviszony-létesítést az minden és az egyes Azure-régióhoz keresztül meghirdetett előtagokat:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="vpngateway"></a>3. A VPN-átjáró és az IPsec-alagutak konfigurálása

Ebben a szakaszban az Azure VPN gateway és a helyszíni VPN-eszköz közötti IPsec VPN-alagutat jönnek létre. A példákban szolgáltatás Cisco útválasztó (CSR1000) VPN-eszközök.

Az alábbi ábrán látható az IPsec VPN alagutak a helyszíni VPN-eszköz 1 és a Azure VPN gateway-példány pár között létesített kapcsolat. A két IPsec VPN-alagutat a helyszíni VPN-eszköz 2 között létesített kapcsolat és az Azure VPN gateway példány pár nem ábrán látható, és a konfiguráció részleteit nem szerepelnek a listán. Azonban további VPN-alagutat kellene javítja a magas rendelkezésre állás.

  ![VPN-alagút](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

A IPsec-alagút pár keresztül egy eBGP munkamenet privát hálózati útvonalak cseréjéhez. Az alábbi ábrán látható az eBGP-munkamenet az IPsec-alagút pár keresztül történik:

  ![az eBGP-munkamenetek alagút pár keresztül](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Az alábbi ábrán látható a példa network hálója áttekintése:

  ![Példa hálózati](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Tudnivalók az Azure Resource Manager-sablon példák

A példákban a VPN-átjáró és az IPsec-alagút végződnek vannak konfigurálva az Azure Resource Manager-sablon használatával. Ha nem ismeri a Resource Manager-sablonokkal, vagy tekintse meg a Resource Manager-sablon alapjainak [struktúra és az Azure Resource Manager-sablonok szintaxisát](../azure-resource-manager/resource-group-authoring-templates.md). Ebben a szakaszban a sablon létrehoz egy előzmények nélküli Azure-környezet (VNet). Azonban ha egy meglévő Vnetet, hivatkozhasson rá a sablonban. Ha nem ismeri a VPN gateway IPsec/IKE helyek közötti konfigurációk, lásd: [hozzon létre egy helyek közötti kapcsolatot](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Nem kell ahhoz, hogy ez a konfiguráció létrehozása Azure Resource Manager-sablonok használata. Ez a konfiguráció az Azure Portalon vagy a PowerShell használatával is létrehozhat.
>
>

### <a name="variables3"></a>3.1. deklarálja a változókat az

Ebben a példában a változódeklarációkat a példa hálózaton felel meg. Változók deklarálásakor módosítsa ezt a részt, a környezetnek megfelelően.

* A változó **localAddressPrefix** helyi IP-címek az IPsec-alagutak le tömbje.
* A **gatewaySku** határozza meg a VPN teljesítménye. További információ a gatewaySku és VPN típusa: [VPN Gateway konfigurációs beállításairól](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). A díjszabással kapcsolatban lásd: [VPN-átjáró](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Állítsa be a **vpnType** való **RouteBased**.

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

### <a name="vnet"></a>3.2-es, hozzon létre virtuális hálózatot (VNet)

Ha egy meglévő Vnetet társítani a VPN-alagutat, kihagyhatja ezt a lépést.

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

### <a name="ip"></a>3.3-as nyilvános IP-címek hozzárendelése a VPN gateway-példány
 
Rendeljen egy nyilvános IP-cím a VPN-átjáró minden egyes példányánál.

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

### <a name="termination"></a>3.4. Adja meg a helyszíni virtuális Magánhálózati alagút lejártáig (helyi hálózati átjáró)

A helyszíni VPN-eszközök nevezzük a **helyi hálózati átjáró**. Az alábbi json-kódrészlet megadja a távoli BGP-társ részletei is:

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

### <a name="creategw"></a>3.5-ös verzióját a VPN-átjáró létrehozása

Ez a szakasz a sablon egy aktív-aktív konfigurációban a szükséges beállításokkal konfigurálja a VPN-átjárót. Tartsa szem előtt az alábbi követelményeknek:

* Hozzon létre a VPN gateway és a egy **"RouteBased"** VpnType. Ez a beállítás akkor kötelező, ha szeretné engedélyezni a BGP-útválasztás a VPN-átjáró és a VPN helyszíni között.
* Lévő aktív – aktív üzemmód, VPN-alagutat a VPN-átjáró két példánya és a egy adott helyszíni eszköz között, a **"aktív"** paraméter értéke **igaz** a Resource Manager-sablon . Magas rendelkezésre állású VPN-átjárókkal kapcsolatos további információk: [VPN-átjáró magas rendelkezésre állású kapcsolat](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Konfigurálja az eBGP-munkamenetek között a VPN-alagutat, meg kell adnia a két különböző ASN-eket sem. Célszerű adja meg a privát ASN számok tartományába. További információkért lásd: [áttekintése a BGP és az Azure VPN-átjárók](../vpn-gateway/vpn-gateway-bgp-overview.md).

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

### <a name="ipsectunnel"></a>3.6-os IPsec-alagutakat a

A parancsfájl az utolsó műveletet hoz létre az Azure VPN gateway és a helyszíni VPN-eszköz közötti IPsec-alagutak.

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

## <a name="device"></a>4. A helyszíni VPN-eszköz konfigurálása

Az Azure VPN gateway számos, különböző szállítóktól származó VPN-eszközök esetén. Konfigurációs adatokat és eszközöket, amelyek működnek a VPN gateway használatának ellenőrzése: [tudnivalók a VPN-eszközök](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

A VPN-eszköz konfigurálásakor szüksége van a következő elemek:

* Megosztott kulcs. Ez a ugyanaz a megosztott kulcs, amely a site-to-site VPN-kapcsolat létrehozásakor megadott. A példákban alapvető megosztott kulcsot. Javasoljuk egy ennél összetettebb kulcs létrehozását.
* A VPN-átjáró nyilvános IP-címét. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. Keresse meg az Azure portal használatával a VPN-átjáró nyilvános IP-címét, navigáljon a virtuális hálózati átjárók, majd kattintson az átjárója nevére.

Általában eBGP társak közvetlenül (gyakran keresztül csatlakoznak egy WAN-kapcsolat). Azonban amikor eBGP ExpressRoute Microsoft társviszony-létesítésen keresztül IPsec VPN-alagutakon keresztül konfigurál, nincsenek több útválasztási tartomány az eBGP partnerek között. Használja a **ebgp-Többszörös ugrási** parancsot az eBGP szomszédos kapcsolatot a két nem hozhat létre – közvetlenül csatlakozik a társaknak. Az ebgp-Többszörös ugrási parancs a következő egész szám a BGP-csomagok az élettartam értéke határozza meg. A parancs **maximális-elérési utak eibgp 2** lehetővé teszi, hogy a a két BGP-útvonalak közötti forgalom terheléselosztásához.

### <a name="cisco1"></a>Cisco CSR1000 példa

Az alábbi példa bemutatja a Cisco CSR1000 konfigurációját, a helyszíni VPN-eszköz Hyper-V virtuális gépen:

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

## <a name="firewalls"></a>5. Konfigurálja a VPN-eszközök szűréséhez és tűzfalak (nem kötelező)

A tűzfal és a szűrést az igényeknek megfelelően konfigurálja.

## <a name="testipsec"></a>6. Tesztelés és ellenőrzés az IPsec-alagút

Az IPsec-alagutak állapotát az Azure VPN gatewayen Powershell-parancsok ellenőrizhető:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Példa a kimenetre:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Az Azure VPN gatewayen az alagutak állapotának ellenőrzéséhez példányok egymástól függetlenül, használja a következő példát:

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

Az alagút állapota a helyszíni VPN-eszközén is ellenőrizheti.

Cisco CSR1000. példa:

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

A sor protokoll a virtuális alagút Interface (VTI) a "mentése" az IKE 2. fázis befejezése nem változik. A következő parancs ellenőrzi a biztonsági társítás:

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

### <a name="verifye2e"></a>Ellenőrizze a között a belső végpontok közötti kapcsolatot a helyszíni és az Azure-beli virtuális hálózat

Ha az IPsec-alagutak működik, és a statikus útvonalakat megfelelően vannak beállítva, pingelni a távoli BGP-társ IP-címét kell lennie:

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

### <a name="verifybgp"></a>Ellenőrizze a BGP-munkamenetek IPSec

Az Azure VPN gateway a BGP-társ állapotának ellenőrzése:

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

Ha ellenőrizni szeretné a VPN kívánt elemet a helyi eBGP-n keresztül fogadott hálózati előtagok listáját, szűrheti "Origin" attribútum által:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

A példa a kimenetre az ASN 65010 a BGP-autonóm rendszer száma a VPN helyszíni.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

A hirdetett útvonalakat listája:

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

A helyszíni Cisco CSR1000 példa:

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

Az Azure VPN-átjáró a helyszíni Cisco CSR1000 a hirdetett hálózatok is listázva lehet a következő paranccsal:

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

* [Helyek közötti kapcsolat hozzáadása virtuális hálózathoz meglévő VPN gateway-kapcsolattal](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)

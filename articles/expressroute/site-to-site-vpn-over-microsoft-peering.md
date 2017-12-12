---
title: "A telephelyek közötti VPN konfigurálása az Azure ExpressRoute-társviszony létesítése – Microsoft keresztül |} Microsoft Docs"
description: "A webhelyek közötti VPN átjáró használatával egy ExpressRoute Microsoft társviszony-létesítési expressroute keresztül IPsec/IKE Azure kapcsolat konfigurálása."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: cherylmc
ms.openlocfilehash: 64203e2cbac1206224f0e0ad8b7d364f19ad0332
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>A telephelyek közötti VPN konfigurálása a ExpressRoute Microsoft-társviszony létesítése – keresztül

Ez a cikk segítséget nyújt a saját ExpressRoute-kapcsolaton keresztül a helyszíni hálózat és az Azure virtuális hálózatokról (Vnetekről) közötti biztonságos titkosított kapcsolati konfigurálásához. Egy biztonságos csatornán keresztül ExpressRoute konfigurálása lehetővé teszi, hogy az adatcseréhez bizalmas, visszajátszás, eredetiséget és sértetlenségét.

## <a name="architecture"></a>Architektúra

Kihasználhatja a Microsoft társviszony-létesítést úgy, hogy a kijelölt helyszíni hálózatokhoz és az Azure Vnet közötti pont-pont IPsec/IKE VPN-alagúton létrehozásához.

  ![kapcsolat – áttekintés](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)

>[!NOTE]
>Beállításakor telephelyek közötti VPN keresztül a Microsoft társviszony-létesítést, a VPN-átjáró és a VPN-kilépő van szó. További információkért lásd: [VPN-átjáró árképzési](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

A magas rendelkezésre állás és redundancia több alagutat konfigurálása az ExpressRoute-kapcsolatcsoportot két MSEE-PE pár keresztül, és engedélyezni a terheléselosztást közötti alagutak.

  ![Magas rendelkezésre állású lehetőség](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

VPN-alagutat a Microsoft társviszony-létesítés keresztül VPN-átjáró, akár segítségével megfelelő hálózati virtuális készülék (NVA) elérhető Azure piactéren keresztül állíthatók le. Ön továbbíthatja az útvonalakat statikus vagy dinamikus titkosított alagutakon keresztül anélkül, hogy az útvonal exchange az alapul szolgáló Microsoft társviszony-létesítés. A cikkben szereplő példák (a BGP-munkamenetet a Microsoft társviszony-létesítés létrehozásához használt eltérő) BGP-vel dinamikusan exchange-előtagok titkosított alagutakon keresztül.

>[!IMPORTANT]
>A helyszíni oldalon a általában a Szegélyhálózaton a Microsoft társviszony megszakad, és az alapvető hálózati zónára magánhálózati társviszony-létesítés megszakadt. A két zónák sorba kerül tűzfalakat használ. A Microsoft társviszony-létesítés kizárólag az ExpressRoute keresztül biztonságos bújtatás engedélyezése beállításakor, ne feledje, csak a nyilvános IP-címek használatával a Microsoft társviszony-létesítés első meghirdetett érdeklő szűrése.
>
>

## <a name="workflow"></a>Munkafolyamat

1. Az ExpressRoute-kör társviszony Microsoft konfigurálása.
2. Kijelölt Azure regionális nyilvános előtagok a helyi hálózaton keresztül a Microsoft társviszony-létesítés esetében.
3. Konfigurálja a VPN-átjáró, és létrehozza az IPsec-alagutak
4. Konfigurálja a helyszíni VPN-eszközön.
5. A pont-pont IPsec/IKE-kapcsolat létrehozása.
6. (Választható) Tűzfal/szűrésének beállítása a helyszíni VPN-eszközön.
7. És az IPsec-kommunikáció hitelesíti az ExpressRoute-kapcsolatcsoport keresztül.

## <a name="peering"></a>1. Konfigurálja a Microsoft társviszony-létesítés

Pont-pont VPN-kapcsolaton keresztül ExpressRoute konfigurálásához is kell használja a Microsoft ExpressRoute-társviszony létesítése –.

* Egy új ExpressRoute-kapcsolatcsoportot konfigurálásához indítsa el a a [ExpressRoute Előfeltételek](expressroute-prerequisites.md) a cikkben, majd [létrehozása és módosítása az ExpressRoute-kapcsolatcsoportot](expressroute-howto-circuit-arm.md).

* Ha már rendelkezik ExpressRoute-kapcsolatcsoportot, de nem rendelkezik a Microsoft társviszony-létesítés konfigurált, konfigurálja a Microsoft társviszony-létesítés használatával a [létrehozása és módosítása az ExpressRoute-kör társviszony](expressroute-howto-routing-arm.md#msft) cikk.

Miután konfigurálta a kapcsolatcsoport és a Microsoft társviszony-létesítést, megtekintéséhez, használatával a **áttekintése** oldal az Azure portálon.

![kör](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2. Útvonal-szűrők konfigurálása

Útvonal szűrő lehetővé teszi, hogy azonosíthassa a keresztül az ExpressRoute-kapcsolatcsoportot Microsoft társviszony-létesítés használni kívánt szolgáltatásokat. Alapvetően az összes BGP közösségi érték engedélyezett legyen. 

![útvonal-szűrő](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

Ebben a példában a következő értékek egyikét csak ebben a *Azure nyugati Velünk 2* régióban. Egy útvonal az Állapotszűrő szabálynak hozzáadódik engedélyezése csak a hirdetés Azure nyugati Velünk 2 regionális előtaglistát kezel, amely a BGP közösségi értéke *12076:51026*. Megadhatja a regionális előtagok kiválasztásával engedélyezni kívánt **kezelése szabály**.

A útvonal szűrőt akkor is kell választania az ExpressRoute-Kapcsolatcsoportok, amelyre az útvonal szűrő vonatkozik. Az ExpressRoute-Kapcsolatcsoportok választhat kiválasztásával **adja hozzá a kapcsolatcsoport**. Az előző ábra az útvonal szűrő az ExpressRoute-kapcsolatcsoportot példa társítva.

### <a name="configfilter"></a>2.1 a útvonal szerinti szűrés beállítása

Egy útvonal szerinti szűrés beállítása. Útmutató: [konfigurálása útvonal szűrők a Microsoft társviszony-létesítéshez](how-to-routefilter-portal.md).

### <a name="verifybgp"></a>2.2 Ellenőrizze a BGP-útvonalakat

Miután sikeresen létrehozta a Microsoft társviszony-létesítéshez az ExpressRoute-kapcsolatcsoportot és útvonal szűrő társított a kapcsolatcsoport, ellenőrizheti a BGP-útvonalakat, amelyek társviszony-létesítés a MSEEs PE eszközön kapott MSEEs. Az ellenőrzési parancs attól függően változik, az operációs rendszer, a PE-eszközök.

#### <a name="cisco-examples"></a>Cisco példák

Ez a példa egy Cisco IOS-XE parancsot használja. A példában a virtuális útválasztási és továbbítása (VRF) példányát használja, hogy a társviszony-létesítési forgalom elkülönítésére.

```
show ip bgp vpnv4 vrf 10 summary
```

A következő részleges az alábbiakat mutatja be, hogy 68 előtagok érkezett a szomszédos *.243.229.34 és az ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

A szomszédos érkezett-előtaglistát kezel a lista megtekintéséhez használja a következő példát:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Előtagok kívánt részesül megerősítéséhez kereszt-ellenőrizheti. A következő Azure PowerShell-parancs kimenete felsorolja a Microsoft társviszony-létesítés egyes szolgáltatások és az egyes Azure-régióhoz meghirdetett előtagokat:

```powershell
Get-AzureRmBgpServiceCommunity
```

## <a name="vpngateway"></a>3. A VPN-átjáró és az IPsec-alagutak konfigurálása

Ebben a szakaszban az IPsec VPN-alagutat az Azure VPN gateway és a helyszíni VPN-eszköz között jönnek létre. A példák szolgáltatás Cisco útválasztó (CSR1000) VPN-eszközök használata.

Az alábbi ábrán látható az IPsec VPN a helyszíni VPN-eszköz 1 és az Azure VPN gateway példány pár közötti alagutak. A két IPsec VPN-alagutat hoznak létre a helyszíni VPN-eszköz 2 és az Azure VPN gateway példány pár nem ábra szemlélteti, és a konfiguráció részleteit nem szerepelnek a listán. Azonban további VPN-alagutat hogy növeli a magas rendelkezésre állású.

  ![VPN-alagutat](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Az IPsec-alagút pár, keresztül egy eBGP munkamenet a magánhálózati útvonalak továbbításához. Az alábbi ábrán látható, az IPsec-alagút pár keresztül létesített eBGP munkamenetre:

  ![a munkamenetek eBGP alagút pár](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Az alábbi ábrán látható a példa hálózati abstracted áttekintése:

  ![Példa hálózati](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Tudnivalók az Azure Resource Manager sablon példák

A példákban a VPN-átjáró és az IPsec-alagút végződnek használatával vannak konfigurálva az Azure Resource Manager-sablon. Ha nem ismeri a Resource Manager-sablonok használatával, vagy tekintse meg a Resource Manager sablon alapjainak [megérteni a felépítését és Azure Resource Manager-sablonok szintaxisát](../azure-resource-manager/resource-group-authoring-templates.md). Ebben a szakaszban létrejön egy greenfield Azure környezetben (VNet). Azonban ha egy meglévő Vnetet, hivatkozhasson rá a sablonban. Ha nem ismeri a VPN gateway IPsec/IKE pont-pont konfigurációival, lásd: [pont-pont kapcsolatot](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Nem szeretné használni, Azure Resource Manager-sablonok létrehozásához az ebben a konfigurációban. Ez a konfiguráció az Azure-portálon, vagy a PowerShell használatával is létrehozhat.
>
>

### <a name="variables3"></a>3.1 deklarálja a változókat

Ebben a példában a változók deklarációja felel meg a hálózatot. Amikor deklaráló változók, módosítsa ezt a részt, a környezetnek megfelelően.

* A változó **localAddressPrefix** tömb a helyi IP-címek és az IPsec-alagutak leáll.
* A **gatewaySku** a VPN-átviteli sebesség határozza meg. GatewaySku és vpnType kapcsolatos további információkért lásd: [VPN-átjáró konfigurációs beállítások](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Díjszabási, lásd: [VPN-átjáró árképzési](https://azure.microsoft.com/pricing/details/vpn-gateway).
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
  "sharedKey": "string",                  // shared secret needs to match with on-premise configuration
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

### <a name="vnet"></a>3.2 hozzon létre virtuális hálózatot (VNet)

Ha egy meglévő virtuális hálózatot társít a VPN-alagutat, kihagyhatja ezt a lépést.

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

### <a name="ip"></a>3.3 nyilvános IP-címek kiosztása a VPN-átjáró példányok
 
Rendelje hozzá egy nyilvános IP-címet a VPN-átjáró minden egyes példányánál.

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

### <a name="termination"></a>3.4 adja meg a helyszíni VPN-alagút vége (helyi hálózati átjáró)

A helyszíni VPN-eszközök nevezzük a **helyi hálózati átjáró**. A következő json-részlet is határozza meg a távoli BGP-társ részletek:

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

Ez a szakasz a sablon aktív-aktív konfigurációhoz szükséges beállításokkal konfigurálja a VPN-átjáró. Vegye figyelembe az alábbi követelményeknek:

* Hozzon létre a VPN-átjárót egy **"RouteBased"** VpnType. A beállítás nem kötelező, ha engedélyezi a BGP-útválasztás a VPN-átjáró és a VPN-helyszíni között.
* Aktív-aktív módban, a VPN-átjáró két példánya és egy adott helyszíni eszköz közötti VPN-alagutat létrehozásához a **"activeActive"** paraméter értéke **igaz** a Resource Manager sablon . További információk a magas rendelkezésre állású VPN-átjárók ismertetése: [magas rendelkezésre állású típusú VPN-átjárókapcsolat](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Az eBGP-munkamenetek között a VPN-alagutat megadásához adjon meg mindkét oldalon két különböző ASN-eket. Célszerű titkos ASN számok megadása. További információkért lásd: [áttekintése a BGP és az Azure VPN-átjárók](../vpn-gateway/vpn-gateway-bgp-overview.md).

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

### <a name="ipsectunnel"></a>3.6 létesíteni az IPsec-alagutak

A végső művelet, a parancsfájl az Azure VPN gateway és a helyszíni VPN-eszköz közötti IPsec-alagutak hoz létre.

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

## <a name="device"></a>4. Konfigurálja a helyszíni VPN-eszköz

Az Azure VPN gateway különböző szállítóktól származó sok VPN-eszközök esetén. Konfigurációs adatok és eszközök VPN-átjáróval használható érvényesítése: [kapcsolatos VPN-eszközök](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

A VPN-eszköz konfigurálásakor kell a következő elemek:

* Megosztott kulcs. Ez az azonos megosztott kulccsal, amely megadja a pont-pont VPN-kapcsolat létrehozása során. A példák alapvető megosztott kulcsot használ. Javasoljuk egy ennél összetettebb kulcs létrehozását.
* A VPN-átjáró nyilvános IP-címét. A nyilvános IP-címet az Azure Portalon, valamint a PowerShell vagy a CLI használatával is megtekintheti. A nyilvános IP-címének a VPN-átjáró, az Azure portál használatával, navigáljon a virtuális hálózati átjárók, majd kattintson az átjárója nevére.

Általában eBGP társak közvetlenül (gyakran keresztül csatlakoznak a WAN-kapcsolaton keresztül). Keresztül IPsec VPN-alagutat keresztül ExpressRoute Microsoft-társviszony létesítése – eBGP beállításához, van azonban több útválasztási tartomány az eBGP partnerek között. Használja a **ebgp-Többszörös ugrási** parancs az eBGP szomszédos kapcsolat a két nem létrehozására-közvetlenül csatlakozik a társaknak. Az egész szám, amely a következő ebgp-Többszörös ugrási parancs az élettartam értéke a BGP-csomagok határozza meg. A parancs **maximális-elérési utak eibgp 2** lehetővé teszi, hogy a két BGP-útvonalak közötti forgalom terheléselosztás.

### <a name="cisco1"></a>Cisco CSR1000 – példa

A következő példa megjeleníti a Cisco CSR1000 konfigurációját a Hyper-V rendszerű virtuális gép, a helyszíni VPN-eszközön:

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

## <a name="firewalls"></a>5. Konfigurálja a VPN-eszköz szűrést és a tűzfalak (nem kötelező)

Konfigurálja a tűzfalat, és a szűrés a követelményeknek megfelelően.

## <a name="testipsec"></a>6. Tesztelése és érvényesítése az IPsec-alagút

IPsec-alagutak állapotának Powershell-parancsokkal ellenőrizheti az Azure VPN-átjárón:

```powershell
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Példa a kimenetre:

```powershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Az Azure VPN gateway-példányokra alagutak állapotának egymástól függetlenül ellenőrzéséhez használja a következő példa:

```powershell
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Példa a kimenetre:

```powershell
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

Bújtatás állapotát is ellenőrizheti, ha a helyszíni VPN-eszköz.

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

A sor protokoll a virtuális alagút Interface (VTI) a "akár" csak internetes KULCSCSERE 2. fázis befejezése után módosítsa. A következő parancsot a biztonsági társítás ellenőrzi:

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

### <a name="verifye2e"></a>Ellenőrizze a végpontok közötti kapcsolatot belső közötti hálózati helyszíni és az Azure virtuális hálózat

Ha az IPsec-alagutak működik, és a statikus útvonalak helyesen vannak-e beállítva, kell tudni pingelni a távoli BGP-társ IP-címe:

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

```powershell
Get-AzureRmVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Példa a kimenetre:

```powershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Ellenőrizze a hálózati előtagot a VPN kívánt elemet helyszíni eBGP keresztül fogadott listáját, az "Origin" attribútum szerint szűrheti:

```powershell
Get-AzureRmVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

A példa az alábbiakat az ASN 65010 a BGP-autonóm rendszer számának a VPN helyszíni.

```powershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

A hirdetett útvonalakat listájának megtekintéséhez:

```powershell
Get-AzureRmVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Példa a kimenetre:

```powershell
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

A helyszíni Cisco CSR1000 hirdetni az Azure VPN gateway hálózatok listája is listázva lehet a következő parancsot:

```powershell
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

## <a name="next-steps"></a>Következő lépések

* [Network Performance Monitor for ExpressRoute konfigurálása](how-to-npm.md)

* [Pont-pont kapcsolat hozzáadása a virtuális hálózatot egy meglévő VPN-átjáró kapcsolattal](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
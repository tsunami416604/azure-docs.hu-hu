---
title: 'Klasszikus virtuális hálózatok csatlakoztatása az Azure Resource Manager virtuális hálózataihoz: PowerShell'
description: Hozzon létre egy VPN-kapcsolatot a klasszikus virtuális hálózatok és az erőforrás-kezelő virtuális hálózatok között vpn-átjáró és a PowerShell használatával.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 1dc0eec6178420976181b05a059e9f8b4859ec2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152006"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Különböző üzemi modellekből származó virtuális hálózatok összekapcsolása a PowerShell-lel

Ez a cikk segít a klasszikus virtuális hálózatok és az erőforrás-kezelő virtuális hálózatok csatlakoztatása, hogy a különálló központi telepítési modellekben található erőforrások kommunikálhassanak egymással. Ebben a cikkben a powershell használata, de az Azure Portalon is létrehozhatja ezt a konfigurációt a cikk kiválasztásával ebből a listából.

> [!div class="op_single_selector"]
> * [Portál](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Powershell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Klasszikus virtuális hálózat csatlakoztatása egy erőforrás-kezelő virtuális hálózathoz hasonló a virtuális hálózat helyszíni helyhez való csatlakoztatásához. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához. Létrehozhat egy kapcsolatot a különböző előfizetésekben és különböző régiókban lévő virtuális hálózatok között. A helyszíni hálózatokkal már rendelkező virtuális hálózatokat is csatlakoztathatja, feltéve, hogy az átjáró, amelyhez konfigurálva vannak, dinamikus vagy útvonalalapú. A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, a [Virtuális hálózatok közötti kapcsolat – gyakori kérdések](#faq) című részben talál. 

Ha még nem rendelkezik virtuális hálózati átjáróval, és nem szeretne létrehozni egyet, érdemes lehet inkább a virtuális hálózatok virtuális hálózatok használatával való csatlakoztatását. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

## <a name="before-you-begin"></a><a name="before"></a>Mielőtt elkezdené

A következő lépések végigvezeti kutatnak a dinamikus vagy útvonal-alapú átjáró konfigurálásához szükséges beállításokon minden virtuális hálózathoz, és létre kell hozniuk egy VPN-kapcsolatot az átjárók között. Ez a konfiguráció nem támogatja a statikus vagy házirend-alapú átjárók.

### <a name="prerequisites"></a><a name="pre"></a>Előfeltételek

* Mindkét virtuális hálózat már létrejött. Ha erőforrás-kezelő virtuális hálózatot kell létrehoznia, olvassa el [az Erőforráscsoport és a virtuális hálózat létrehozása](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network)című témakört. Klasszikus virtuális hálózat létrehozásához olvassa el a [Klasszikus virtuális hálózat létrehozása](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic)című témakört.
* A virtuális hálózatok címtartományai nem fedik át egymást, és nem fedik át az átjárók által összekapcsolt egyéb kapcsolatok tartományait.
* Telepítette a legújabb PowerShell-parancsmagokat. További információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview) című témakörben talál további információt. Győződjön meg arról, hogy a Service Management (SM) és az Erőforrás-kezelő (RM) parancsmagjait is telepíti. 

### <a name="example-settings"></a><a name="exampleref"></a>Példabeállítások

Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat.

**Klasszikus virtuális hálózat beállításai**

Virtuális hálózat neve = ClassicVNet <br>
Helyszín = USA nyugati telephelye <br>
Virtuális hálózati címterek = 10.0.0.0/24 <br>
Alhálózat-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Helyi hálózat neve = RMVNetLocal <br>
GatewayType = DynamicRouting

**Erőforrás-kezelő virtuális hálózatának beállításai**

Virtuális hálózat neve = RMVNet <br>
Erőforráscsoport = RG1 <br>
Virtuális hálózati IP-címterek = 192.168.0.0/16 <br>
Alhálózat-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Helyszín = USA keleti része <br>
Átjáró nyilvános IP-neve = gwpip <br>
Helyi hálózati átjáró = ClassicVNetLocal <br>
Virtuális hálózati átjáró neve = RMGateway <br>
Átjáró IP-címzési konfigurációja = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>1. szakasz - A klasszikus virtuális hálózat konfigurálása
### <a name="1-download-your-network-configuration-file"></a>1. A hálózati konfigurációs fájl letöltése
1. Jelentkezzen be Azure-fiókjába a PowerShell-konzol emelt szintű jogosultságokkal. A következő parancsmag az Azure-fiók bejelentkezési hitelesítő adatait kéri. A bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell számára. A klasszikus Service Management (SM) Azure PowerShell-parancsmagok ebben a szakaszban használatosak.

   ```azurepowershell
   Add-AzureAccount
   ```

   Azure-előfizetése.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Exportálja az Azure hálózati konfigurációs fájlját a következő parancs futtatásával. Szükség esetén módosíthatja a fájl helyét, hogy egy másik helyre exportálja.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Nyissa meg a szerkesztéshez letöltött .xml fájlt. A hálózati konfigurációs fájlra vonatkozó példát a [Hálózati konfigurációs séma című témakörben tanévenként láthatja](https://msdn.microsoft.com/library/jj157100.aspx)el.

### <a name="2-verify-the-gateway-subnet"></a>2. Az átjáró alhálózatának ellenőrzése
A **VirtualNetworkSites** elemben adjon hozzá egy átjáró alhálózatot a virtuális hálózathoz, ha még nem hoztak létre egyet. A hálózati konfigurációs fájlhasználata során az átjáró alhálózatának "GatewaySubnet" nevet kell adnia, különben az Azure nem ismeri fel és nem tudja átjáró-alhálózatként használni.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Példa:**

    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>

### <a name="3-add-the-local-network-site"></a>3. A helyi hálózati hely hozzáadása
A hozzáadott helyi hálózati hely azt az RM virtuális hálózatot jelöli, amelyhez csatlakozni szeretne. Ha még nem létezik, vegyen fel egy **LocalNetworkSites** elemet a fájlba. A konfiguráció ezen pontján a VPNGatewayAddress bármely érvényes nyilvános IP-cím lehet, mert még nem hoztuk létre az átjárót az erőforrás-kezelő virtuális hálózatához. Miután létrehoztuk az átjárót, ezt a helyőrző IP-címet az RM-átjáróhoz rendelt megfelelő nyilvános IP-címre cseréljük.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. A virtuális hálózat társítása a helyi hálózati helyhez
Ebben a szakaszban azt a helyi hálózati helyet adjuk meg, amelyhez a virtuális hálózatot csatlakoztatni szeretné. Ebben az esetben az erőforrás-kezelő virtuális hálózat, amely korábban hivatkozott. Győződjön meg róla, hogy a nevek egyeznek. Ez a lépés nem hoz létre átjárót. Azt a helyi hálózatot határozza meg, amelyhez az átjáró csatlakozni fog.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Mentse a fájlt, és töltse fel
Mentse a fájlt, majd importálja az Azure-ba a következő parancs futtatásával. Győződjön meg arról, hogy a környezethez szükségesen módosítja a fájl elérési útját.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Hasonló eredmény jelenik meg, amely azt mutatja, hogy az importálás sikeres volt.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Az átjáró létrehozása

A példa futtatása előtt tekintse meg a hálózati konfigurációs fájlt, amely et letöltött a pontos neveket, amelyek et az Azure várhatóan látni. A hálózati konfigurációs fájl tartalmazza a klasszikus virtuális hálózatok értékeit. Előfordulhat, hogy a klasszikus virtuális hálózatok nevei módosulnak a hálózati konfigurációs fájlban, amikor klasszikus virtuális hálózat-beállításokat hoz létre az Azure Portalon a telepítési modellek eltérései miatt. Ha például az Azure Portalon egy "Classic VNet" nevű klasszikus virtuális hálózat ot hozott létre, és létrehozta azt egy "ClassicRG" nevű erőforráscsoportban, a hálózati konfigurációs fájlban lévő név "Group ClassicRG Classic VNet" lesz. A szóközöket tartalmazó virtuális hálózat nevének megadásakor használjon idézőjeleket az érték körül.


Dinamikus útválasztási átjáró létrehozásához használja az alábbi példát:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Az átjáró állapotát a **Get-AzureVNetGateway** parancsmag használatával ellenőrizheti.

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>2. szakasz - Az RM virtuális hálózat átjárójának konfigurálása



Az előfeltételek feltételezik, hogy már létrehozott egy RM virtuális hálózatot. Ebben a lépésben hozzon létre egy VPN-átjárót az RM virtuális hálózathoz. Ne indítsa el ezeket a lépéseket, amíg le nem kérte a klasszikus virtuális hálózat átjárónyilvános IP-címét. 

1. Jelentkezzen be Azure-fiókjába a PowerShell-konzolon. A következő parancsmag az Azure-fiók bejelentkezési hitelesítő adatait kéri. A bejelentkezés után a fiók beállításai letöltődnek, hogy azok elérhetők az Azure PowerShell számára. Az Azure Cloud Shell böngészőben való elindításához tetszés szerint használhatja a "Try It" funkciót.

   Ha az Azure Cloud Shellt használja, hagyja ki a következő parancsmast:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Annak ellenőrzéséhez, hogy a megfelelő előfizetést használja-e, futtassa a következő parancsmast:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Ha egynél több előfizetéssel rendelkezik, adja meg a használni kívánt előfizetést.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Hozzon létre egy helyi hálózati átjárót. Virtuális hálózatokban a helyi hálózati átjáró általában a helyszínt jelenti. Ebben az esetben a helyi hálózati átjáró a klasszikus virtuális hálózatra hivatkozik. Adjon meg egy nevet, amellyel az Azure hivatkozhat rá, és adja meg a címtér előtagot is. Az Azure a megadott IP-címelőtagot a helyszíni helyre küldendő adatforgalom azonosítására használja. Ha később módosítania kell az adatokat, az átjáró létrehozása előtt módosíthatja az értékeket, és újra futtathatja a mintát.
   
   **-A név** az a név, amelyet a helyi hálózati átjáróhoz kíván rendelni.<br>
   **-AddressPrefix** a címtér a klasszikus virtuális hálózat.<br>
   **-GatewayIpAddress** a klasszikus virtuális hálózat átjárójának nyilvános IP-címe. Be sure to change the following sample text "n.n.n.n" to reflect the correct IP address.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Kérjen nyilvános IP-címet az erőforrás-kezelő virtuális hálózatának virtuális hálózati átjárójához. A használni kívánt IP-címet nem adhatja meg. Az IP-cím dinamikusan van lefoglalva a virtuális hálózati átjáró számára. Ez azonban nem jelenti azt, hogy az IP-cím változik. A virtuális hálózati átjáró IP-címe csak akkor változik meg, amikor az átjárót törlik és újra létrehozják. Nem változik átméretezés, alaphelyzetbe állítás vagy az átjáró egyéb belső karbantartása/frissítése során.

   Ebben a lépésben egy későbbi lépésben használt változót is beállítunk.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Ellenőrizze, hogy a virtuális hálózat rendelkezik-e átjáróalhálózattal. Ha nem létezik átjáró alhálózat, adjon hozzá egyet. Győződjön meg arról, hogy az átjáró alhálózatának *neve GatewaySubnet.*
5. Az átjáróhoz használt alhálózat beolvasása a következő parancs futtatásával. Ebben a lépésben egy változót is beállítunk, amelyet a következő lépésben kell használni.
   
   **-A név** az erőforrás-kezelő virtuális hálózatának neve.<br>
   **-ResourceGroupName** az az erőforráscsoport, amelyhez a virtuális hálózat társítva van. Az átjáró alhálózatának már léteznie kell ehhez a virtuális hálózathoz, és a megfelelő működéshez *GatewaySubnet* nevűnek kell lennie.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. Hozza létre az átjáró IP-címzési konfigurációját. Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. A következő minta használatával hozza létre az átjáró konfigurációját.

   Ebben a lépésben a **-SubnetId** és **-PublicIpAddressId** paramétereket át kell adni az id tulajdonságnak az alhálózatból, illetve AZ IP-címobjektumokat. Nem használhatsz egyszerű karakterláncot. Ezek a változók a lépésben vannak beállítva, hogy nyilvános IP-címet és az alhálózat lekéréséhez.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Hozza létre az Erőforrás-kezelő virtuális hálózati átjáróját a következő parancs futtatásával. A `-VpnType` kell *RouteBased*. Az átjáró létrehozása 45 percet vagy többet is igénybe vehet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Másolja a nyilvános IP-címet a VPN-átjáró létrehozása után. Akkor használja, ha konfigurálja a helyi hálózati beállításokat a klasszikus virtuális hálózat. A következő parancsmag segítségével lekérheti a nyilvános IP-címet. A nyilvános IP-cím a visszatérésben IpAddress néven jelenik *meg.*

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>3. szakasz - A klasszikus virtuális hálózat helyi helybeállításainak módosítása

Ebben a szakaszban a klasszikus virtuális hálózattal dolgozik. Az Erőforrás-kezelő virtuális hálózat átjárójához való csatlakozáshoz használt helyi helybeállítások megadásakor használt helyőrző IP-címet cseréli le. Mivel a klasszikus virtuális hálózattal dolgozik, használja a helyileg telepített PowerShellt a számítógépre, ne az Azure Cloud Shell TryIt-et.

1. A hálózati konfigurációs fájl exportálása.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Szövegszerkesztő használatával módosítsa a VPNGatewayAddress értékét. Cserélje le a helyőrző IP-címet az Erőforrás-kezelő átjáró nyilvános IP-címére, majd mentse a módosításokat.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importálja a módosított hálózati konfigurációs fájlt az Azure-ba.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>4. szakasz - Kapcsolat létrehozása az átjárók között
Az átjárók közötti kapcsolat létrehozásához PowerShellszükséges. Előfordulhat, hogy hozzá kell adnia az Azure-fiókját a PowerShell-parancsmagok klasszikus verziójának használatához. Ehhez használja **az Add-AzureAccount .to**do do do it, use Add-AzureAccount .

1. A PowerShell-konzolon állítsa be a megosztott kulcsot. A parancsmagok futtatása előtt tekintse meg a hálózati konfigurációs fájlt, amely et letöltött a pontos neveket, amelyek et az Azure várhatóan látni. A szóközöket tartalmazó virtuális hálózat nevének megadásakor használjon aknaidézőjeleket az érték körül.<br><br>A következő példában **a -VNetName** a klasszikus virtuális hálózat neve, a **-LocalNetworkSiteName** pedig a helyi hálózati helyhez megadott név. A **-SharedKey** egy létrehozott és megadott érték. A példában az "abc123"-at használtuk, de létrehozhat és használhat valami bonyolultabbat. A lényeg az, hogy az itt megadott értéknek meg kell egyeznie a következő lépésben megadott értékkel. A visszatérésnek az Állapot: Sikeres állapot ot kell **mutatnia.**

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Hozza létre a VPN-kapcsolatot a következő parancsok futtatásával:
   
   Állítsa be a változókat.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Hozza létre a kapcsolatot. Figyelje meg, hogy a **-ConnectionType** iPsec, nem Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>5. szakasz - A kapcsolatok ellenőrzése

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>A klasszikus virtuális hálózat és az erőforrás-kezelő virtuális hálózatának kapcsolatának ellenőrzése

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure portál

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Az erőforrás-kezelő virtuális hálózatának a klasszikus virtuális hálózathoz való csatlakozásának ellenőrzése

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure portál

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

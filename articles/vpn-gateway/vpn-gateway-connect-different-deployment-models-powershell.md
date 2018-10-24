---
title: 'Klasszikus virtuális hálózatok összekapcsolása az Azure Resource Manager-alapú virtuális hálózatokhoz: PowerShell |} A Microsoft Docs'
description: Erőforrás-kezelői virtuális hálózatok VPN Gateway és a PowerShell használatával és a klasszikus virtuális hálózatok közötti VPN-kapcsolat létrehozása.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 5f133af5ec077821607bf3e942c8a931808d34fc
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953587"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Különböző üzemi modellekből származó virtuális hálózatok összekapcsolása a PowerShell-lel

Ez a cikk segítséget nyújt a klasszikus virtuális hálózatok csatlakoztatása Resource Manager-alapú virtuális hálózatokhoz kommunikálnak egymással a különböző üzembe helyezési modellekben található erőforrások. A jelen cikkben ismertetett lépések PowerShell-lel, de ez a konfiguráció a listából a cikk az Azure portal használatával is létrehozhat.

> [!div class="op_single_selector"]
> * [Portál](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Egy klasszikus virtuális hálózat csatlakoztatása virtuális hálózathoz a Resource Manager nagyon hasonlít egy virtuális hálózathoz csatlakozik egy helyszíni helyhez. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához. Különböző előfizetésben található, és különböző régiókban található virtuális hálózatok közötti kapcsolatot hozhat létre. Már van kapcsolat a helyszíni hálózathoz, virtuális hálózatok mindaddig, amíg az átjárót, hogy fiókjuk konfigurálva lett a dinamikus vagy útválasztó-alapú is csatlakoztathat. A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, a [Virtuális hálózatok közötti kapcsolat – gyakori kérdések](#faq) című részben talál. 

Ha még nem rendelkezik a virtuális hálózati átjáró, és nem szeretne létrehozni egyet, érdemes inkább fontolja meg a virtuális hálózatok összekapcsolása a virtuális hálózatok közötti társviszony-létesítés. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

## <a name="before"></a>Előkészületek

A következő lépések végigvezetik a dinamikus vagy útvonalalapú átjárót konfigurálja az egyes virtuális hálózatok és az átjárók közötti VPN-kapcsolat létrehozásához szükséges beállításokat. Ez a konfiguráció nem támogatja a statikus vagy csoportházirend-alapú átjárók.

### <a name="pre"></a>Előfeltételek

* Mindkét virtuális hálózaton van már létre van hozva. Ha a resource manager virtuális hálózat létrehozása van szüksége, tekintse meg [hozzon létre egy virtuális hálózatot](../virtual-network/quick-create-powershell.md#create-a-virtual-network). Egy klasszikus virtuális hálózat létrehozása: [klasszikus virtuális hálózat létrehozása](https://docs.microsoft.com/azure/virtual-network/create-virtual-network-classic).
* A virtuális hálózat címtartományát nem egymással átfedésben vannak, vagy más kapcsolatokat, az átjárók csatlakoznak előfordulhat, hogy azokat a tartományokat átfedésben.
* Telepítette a legújabb PowerShell-parancsmagokat. Lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview) további információt. Ellenőrizze, hogy a Service Management (SM) és az erőforrás-kezelő (RM) parancsmagok is telepíti. 

### <a name="exampleref"></a>Példabeállítások

Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat.

**Klasszikus virtuális hálózat beállításait**

Virtuális hálózat neve = ClassicVNet <br>
Hely = USA nyugati RÉGIÓJA <br>
Virtuális hálózat Címterei = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
Átjáró-alhálózat = 10.0.0.32/29 <br>
Helyi hálózati neve = RMVNetLocal <br>
GatewayType = DynamicRouting

**Resource Manager virtuális hálózat beállításait**

Virtuális hálózat neve = RMVNet <br>
Erőforráscsoport = RG1 <br>
Virtuális hálózati IP-címterek = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
Átjáró-alhálózat = 192.168.0.0/26 <br>
Hely = USA keleti RÉGIÓJA <br>
Átjáró nyilvános IP-név = gwpip <br>
Helyi hálózati átjáró = ClassicVNetLocal <br>
Virtuális hálózati átjáró neve = RMGateway <br>
Átjáró IP-címkonfigurációjának = gwipconfig

## <a name="createsmgw"></a>1. szakasz – a klasszikus virtuális hálózat konfigurálása
### <a name="1-download-your-network-configuration-file"></a>1. Töltse le a hálózati konfigurációs fájlt
1. Jelentkezzen be a PowerShell-konzol emelt szintű jogosultságokkal rendelkeznek az Azure-fiókjával. A következő parancsmagot a bejelentkezési hitelesítő adatokat kér az Azure-fiók. A bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell számára. A klasszikus Azure PowerShell Service Management (SM) parancsmagok ebben a szakaszban szolgálnak.

  ```azurepowershell
  Add-AzureAccount
  ```

  Az Azure-előfizetés beszerzése.

  ```azurepowershell
  Get-AzureSubscription
  ```

  Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

  ```azurepowershell
  Select-AzureSubscription -SubscriptionName "Name of subscription"
  ```
2. Exportálhatja az Azure hálózati konfigurációs fájlt a következő parancs futtatásával. Módosíthatja egy másik helyre, szükség esetén exportálhatja a fájl helyét.

  ```azurepowershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
3. Nyissa meg a letöltött szerkeszteni, .xml fájlt. A hálózati konfigurációs fájlt egy példa: a [Network konfigurációs séma](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Ellenőrizze az átjáró-alhálózat
Az a **VirtualNetworkSites** elemben adja hozzá egy átjáró-alhálózatot a virtuális hálózathoz, ha egy nem már lett létrehozva. A hálózati konfigurációs fájlt az használatakor az átjáró-alhálózat neve legyen "GatewaySubnet" vagy az Azure nem ismeri fel, és szeretné használni egy átjáró-alhálózatot.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Példa**

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

### <a name="3-add-the-local-network-site"></a>3. Adja hozzá a helyi hálózati telephely
A helyi hálózati telephely hozzáadhat jelöli az erőforrás-kezelő virtuális hálózatot, amelyhez szeretné csatlakoztatni. Adjon hozzá egy **LocalNetworkSites** elem a fájlt, ha egy már nem létezik. Ezen a ponton az a konfiguráció a VPNGatewayAddress lehet bármely érvényes nyilvános IP-címet, mert azt még nem hozta létre az átjáró a Resource Manager virtuális hálózathoz. Miután hozunk létre az átjáró, hogy cserélje le a helyőrző IP-címet a megfelelő nyilvános IP-címet, amely az erőforrás-kezelő átjáró hozzá lett rendelve.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. A virtuális hálózat társítása a helyi hálózati telephely
Ebben a szakaszban adjuk meg a helyi hálózati telephely, amely szeretné csatlakoztatni a virtuális hálózat számára. Ebben az esetben a Resource Manager virtuális hálózattal, akkor korábban hivatkozott. Ellenőrizze, hogy a nevek megegyeznek. Ez a lépés nem hoz létre az átjáró. Azt adja meg a helyi hálózaton, amelyhez az átjáró csatlakozni fognak.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Mentse a fájlt, és töltse fel
Mentse a fájlt, majd azok importálása az Azure a következő parancs futtatásával. Ellenőrizze, hogy a fájl elérési útját a környezetben szükség szerint módosíthatja.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Látni fogja a hasonló eredmény megjelenítése, hogy az importálás sikeres volt.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Az átjáró létrehozása

Mielőtt futtatná az ebben a példában, tekintse meg a hálózati konfigurációs fájlt, ahonnan letöltötte a pontos nevek, amely az Azure tervei szerint megtekintéséhez. A hálózati konfigurációs fájlt az értékeket a klasszikus virtuális hálózatokat tartalmaz. Néha az a klasszikus virtuális hálózatok az eszköznevek módosultak a hálózat konfigurációs fájljában a az üzemi modellek között fennálló különbségek miatt az Azure Portalon a klasszikus virtuális hálózat beállításait létrehozásakor. Például ha az Azure portal segítségével hozzon létre egy klasszikus virtuális hálózat "Klasszikus virtuális hálózat" nevű és "ClassicRG" nevű erőforráscsoportban létrehozott azt, a neve, a hálózat konfigurációs fájljában szereplő alakítja át "Csoport ClassicRG klasszikus virtuális hálózat". Adjon meg, amely tartalmazza a tárolóhelyek virtuális hálózat nevére, használja az értéket szimpla idézőjelek között.


Használja az alábbi példa egy dinamikus útválasztású átjáró létrehozása:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Az átjáró állapotának ellenőrzéséhez használja a **Get-AzureVNetGateway** parancsmagot.

## <a name="creatermgw"></a>2. szakasz – az erőforrás-kezelő virtuális hálózati átjáró konfigurálása

Az Előfeltételek azt feltételezik, hogy már létrehozott egy erőforrás-kezelő virtuális hálózatot. Ebben a lépésben a VPN-átjáró az erőforrás-kezelő virtuális hálózat létrehozása. Ne indítsa el ezeket a lépéseket, amíg a klasszikus virtuális hálózati átjáró nyilvános IP-címét a lekérdezés után. 

1. Jelentkezzen be az Azure-fiókjába a PowerShell-konzolon. A következő parancsmagot a bejelentkezési hitelesítő adatokat kér az Azure-fiók. Miután bejelentkezett, a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell-lel letöltődnek. A "Kipróbálom" funkció segítségével igény szerint indítsa el az Azure Cloud Shellt a böngészőben.

  Ha az Azure Cloud Shellt használja, hagyja ki a következő parancsmagot:

  ```azurepowershell
  Connect-AzureRmAccount
  ``` 
  Győződjön meg arról, hogy a megfelelő előfizetést használ, futtassa a következő parancsmagot:  

  ```azurepowershell-interactive
  Get-AzureRmSubscription
  ```
   
  Ha több előfizetéssel rendelkezik, válassza ki a használni kívánt előfizetést.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Hozzon létre egy helyi hálózati átjárót. Virtuális hálózatokban a helyi hálózati átjáró általában a helyszínt jelenti. A helyi hálózati átjáró ebben az esetben a klasszikus virtuális hálózat hivatkozik. Neki egy nevet, amellyel Azure hivatkozhat rá, és is megadhatja a címtér-előtagját. Az Azure a megadott IP-címelőtagot a helyszíni helyre küldendő adatforgalom azonosítására használja. Ha szeretné az adatokat ide később módosíthatja az átjáró létrehozása előtt módosítsa az értékeket, és futtassa újra a mintakódot.
   
   **-Név** , tekintse meg a helyi hálózati átjáróhoz hozzárendelendő neve.<br>
   **-AddressPrefix** a klasszikus virtuális hálózat a címtér van.<br>
   **-GatewayIpAddress** a klasszikus virtuális hálózati átjáró nyilvános IP-címe. Győződjön meg arról, a következő minta szöveg "n.n.n.n", hogy a megfelelő IP-cím módosításához.<br>

  ```azurepowershell-interactive
  New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
  -Location "West US" -AddressPrefix "10.0.0.0/24" `
  -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
  ```
3. Kérjen egy nyilvános IP-cím kiosztását a virtuális hálózati átjáró a Resource Manager virtuális hálózathoz. A használni kívánt IP-címet nem adhatja meg. Az IP-címet a virtuális hálózati átjáró számára dinamikusan történik. Ez azonban nem jelenti azt, hogy az IP-cím változik. Csak ha az átjárót törli, majd újra létre kell hozni a virtuális hálózati átjáró IP-cím módosításainak van. Nem módosul átméretezés, alaphelyzetbe vagy más belső karbantartása/frissítése során az átjárót.

  Ebben a lépésben azt is beállíthatja egy későbbi lépésben használt.

  ```azurepowershell-interactive
  $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
  -ResourceGroupName RG1 -Location 'EastUS' `
  -AllocationMethod Dynamic
  ```

4. Győződjön meg arról, hogy a virtuális hálózat rendelkezik-e egy átjáró-alhálózatot. Ha nincs átjáró-alhálózat létezik, adjon hozzá egyet. Győződjön meg arról, hogy az átjáró-alhálózat neve *GatewaySubnet*.
5. Az átjáró a következő parancs futtatásával használt alhálózatból lekéréséhez. Ebben a lépésben azt is beállíthatja a következő lépésben használandó.
   
   **-Név** a Resource Manager virtuális hálózat neve.<br>
   **-ResourceGroupName** van az erőforráscsoportot, amelyhez a virtuális hálózathoz társítva van. Az átjáró-alhálózat már léteznie kell a virtuális hálózathoz, és névvel kell rendelkeznie *GatewaySubnet* megfelelően működjön.<br>

  ```azurepowershell-interactive
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
  -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
  ``` 

6. Az átjáró IP-címkonfigurációjának létrehozása. Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. A következő minta használatával hozza létre az átjáró konfigurációját.

  Ebben a lépésben a **- SubnetId** és **- PublicIpAddressId** kell átadni az id tulajdonság az alhálózatot és IP-cím objektumok, illetve. Egyszerű karakterláncnak nem használható. Ezek a változók vannak állítva a lépésben egy nyilvános IP-címet és a lépés beolvasni az alhálózat.

  ```azurepowershell-interactive
  $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
  -Name gwipconfig -SubnetId $subnet.id `
  -PublicIpAddressId $ipaddress.id
  ```
7. A Resource Manager virtuális hálózati átjáró létrehozása a következő parancs futtatásával. A `-VpnType` kell *RouteBased*. További, az átjáró létrehozása akár 45 percet is igénybe vehet.

  ```azurepowershell-interactive
  New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
  -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
  -IpConfigurations $gwipconfig `
  -EnableBgp $false -VpnType RouteBased
  ```
8. A VPN-átjáró létrehozása után másolja a nyilvános IP-cím. Akkor használja a klasszikus virtuális hálózat számára a helyi hálózati beállítások konfigurálásakor. A következő parancsmag használatával a nyilvános IP-cím lekéréséhez. A nyilvános IP-cím szerepel, a visszatérési *IP-cím*.

  ```azurepowershell-interactive
  Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1
  ```

## <a name="localsite"></a>3. szakasz – a klasszikus virtuális hálózatok helyi hely beállításainak módosítása

Ebben a szakaszban dolgozik a klasszikus virtuális hálózatot. Lecseréli a helyőrző IP-címet, amelyet a helyi webhely beállításait, a Resource Manager VNet-átjáró való kapcsolódáshoz használandó megadásakor használt. A klasszikus virtuális hálózattal dolgozik, mert helyben telepítve a számítógépre, nem az Azure Cloud Shell TryIt PowerShell-lel.

1. Exportálhatja a hálózati konfigurációs fájlt.

  ```azurepowershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Egy szövegszerkesztővel, módosítsa a értéket a VPNGatewayAddress. Cserélje le a helyőrző IP-címet a Resource Manager-átjáró nyilvános IP-címét, és mentse a módosításokat.

  ```
  <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  ```
3. Importálja a módosított hálózati konfigurációs fájlt az Azure-bA.

  ```azurepowershell
  Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
  ```

## <a name="connect"></a>4. szakasz – az átjárók közötti kapcsolat létrehozása
Az átjárók közötti kapcsolat létrehozása a PowerShell szükséges. Szükség lehet a PowerShell-parancsmagokat a klasszikus verzió használatához az Azure-fiók hozzáadása. Ehhez használja **Add-AzureAccount**.

1. A PowerShell-konzolon állítsa be a megosztott kulcsot. A-parancsmagok futtatása előtt tekintse meg a hálózati konfigurációs fájlt, ahonnan letöltötte a pontos nevek, amely az Azure tervei szerint megtekintéséhez. Adjon meg, amely tartalmazza a tárolóhelyek virtuális hálózat nevét, ha egyetlen idézőjelek közé az értéket használja.<br><br>A következő példában **- VNetName** neve a klasszikus virtuális hálózatot, és **- LocalNetworkSiteName** a helyi hálózati telephelyhez megadott neve. A **- SharedKey** érték, amely az Ön hozza létre, és adja meg. A példában az "abc123" használtuk, de Ön hozza létre, és összetettebb használja. A lényeg az, hogy az itt megadott értéknek kell lennie a következő lépés a kapcsolat létrehozásakor megadott ugyanazt az értéket. Meg kell jelennie a visszatérési **állapota: sikeres**.

  ```azurepowershell
  Set-AzureVNetGatewayKey -VNetName ClassicVNet `
  -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
  ```
2. A VPN-kapcsolat létrehozása a következő parancsok futtatásával:
   
  Állítsa be a változókat.

  ```azurepowershell-interactive
  $vnet01gateway = Get-AzureRmLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
  $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
  ```
   
  Hozza létre a kapcsolatot. Figyelje meg, hogy a **- ConnectionType** IPsec, nem Vnet2Vnet van.

  ```azurepowershell-interactive
  New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
  -Location "East US" -VirtualNetworkGateway1 `
  $vnet02gateway -LocalNetworkGateway2 `
  $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

## <a name="verify"></a>5. szakasz – a kapcsolatok ellenőrzése

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>A kapcsolat a klasszikus virtuális hálózat és a Resource Manager virtuális hálózat ellenőrzése

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>A kapcsolat a Resource Manager virtuális hálózat és a klasszikus virtuális hálózat ellenőrzése

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

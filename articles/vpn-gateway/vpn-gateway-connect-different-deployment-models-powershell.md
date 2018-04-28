---
title: 'Klasszikus virtuális hálózatok csatlakoztatása Azure Resource Manager Vnetek: PowerShell |} Microsoft Docs'
description: Klasszikus virtuális hálózatokat és erőforrás-kezelő Vnetek VPN-átjáró és a PowerShell használatával közötti VPN-kapcsolat létrehozása.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: f17c3bf0-5cc9-4629-9928-1b72d0c9340b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2018
ms.author: cherylmc
ms.openlocfilehash: 65faf1a4f78244d9fdd03b6415bf2cadac923504
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Különböző üzemi modellekből származó virtuális hálózatok összekapcsolása a PowerShell-lel

Ez a cikk segít az erőforrás-kezelő Vnetek engedélyezi a különálló üzembe helyezési modellel egymással kommunikálni található erőforrásokhoz a hagyományos Vneteket csatlakozni. A cikkben található lépéseket használhatja a PowerShell, de ez a konfiguráció a listáról a cikk kiválasztásával az Azure portál használatával is létrehozhat.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

A klasszikus virtuális hálózat csatlakozik egy erőforrás-kezelő virtuális hálózat hasonlít egy Vnetet csatlakozik egy helyszíni hely. Mindkét kapcsolattípus egy VPN-átjárót használ a biztonságos alagút IPsec/IKE használatával való kialakításához. Létrehozhat, amelyek különböző előfizetésekhez és különböző régiókban Vnetek közötti kapcsolat. A helyszíni hálózatokhoz való csatlakozás már rendelkező Vnetek mindaddig, amíg az átjáró, amely a konfigurálva a dinamikus- vagy útválasztó-alapú is csatlakoztathatja. A virtuális hálózatok közötti kapcsolatokról további információt a cikk végén, a [Virtuális hálózatok közötti kapcsolat – gyakori kérdések](#faq) című részben talál. 

Ha még nincs a virtuális hálózati átjáró, és nem szeretne létrehozni egyet, érdemes lehet helyette figyelembe kell venni a Vnetben társviszony-létesítés használatával Vnetek kapcsolódás. A virtuális hálózatok közötti társviszony nem használ VPN-átjárót. További információ: [Társviszony létesítése virtuális hálózatok között](../virtual-network/virtual-network-peering-overview.md).

## <a name="before"></a>Előkészületek

A következő lépések végigvezetik a dinamikus- vagy útválasztó-alapú átjárók konfigurálása az egyes virtuális hálózat és az átjárók közötti VPN-kapcsolat létrehozásához szükséges beállításokat. Ez a konfiguráció nem támogatja a statikus vagy csoportházirend-alapú átjárók.

### <a name="pre"></a>Előfeltételek

* Mindkét Vnetek létre lett hozva.
* A Vnetek címtartományát nem átfedésben vannak egymással, vagy átfedésben vannak a tartományt az átjárók csatlakoztathatók más kapcsolatok esetében.
* A legújabb PowerShell-parancsmagok telepítése. Lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) további információt. Győződjön meg arról, hogy a Service Management (SM) és az erőforrás-kezelő (RM) parancsmagok telepítenie. 

### <a name="exampleref"></a>Példabeállítások

Ezekkel az értékekkel létrehozhat egy tesztkörnyezetet, vagy a segítségükkel értelmezheti a cikkben szereplő példákat.

**Klasszikus virtuális hálózat beállításai**

Virtuális hálózat neve = ClassicVNet <br>
Hely = USA nyugati régiója <br>
Virtuális hálózat = 10.0.0.0/24 <br>
Alhálózat-1 = 10.0.0.0/27 <br>
A GatewaySubnet = 10.0.0.32/29 <br>
Helyi hálózati név = RMVNetLocal <br>
GatewayType = DynamicRouting

**Erőforrás-kezelő VNet beállításait**

Virtuális hálózat neve = RMVNet <br>
Erőforráscsoport = RG1 <br>
Virtuális hálózati IP-címterek = 192.168.0.0/16 <br>
Alhálózat-1 = 192.168.1.0/24 <br>
A GatewaySubnet = 192.168.0.0/26 <br>
Hely = USA keleti régiója <br>
Átjáró nyilvános IP-név = gwpip <br>
Helyi hálózati átjáró = ClassicVNetLocal <br>
A virtuális hálózati átjáró neve = RMGateway <br>
Átjáró IP-címzési beállításokat = gwipconfig

## <a name="createsmgw"></a>1. szakasz - a klasszikus virtuális hálózat konfigurálása
### <a name="1-download-your-network-configuration-file"></a>1. A hálózati konfigurációs fájl letöltése
1. Jelentkezzen be az Azure-fiókjával a PowerShell-konzolban, emelt szintű jogosultságokkal. A következő parancsmag kéri a bejelentkezési hitelesítő adatok az Azure-fiók. A bejelentkezés után letölti a fiók beállításait, hogy elérhetők legyenek az Azure PowerShell számára. A klasszikus Service Management (SM) Azure PowerShell-parancsmagok használhatók ebben a szakaszban.

  ```powershell
  Add-AzureAccount
  ```

  Az Azure-előfizetéshez beolvasása.

  ```powershell
  Get-AzureSubscription
  ```

  Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

  ```powershell
  Select-AzureSubscription -SubscriptionName "Name of subscription"
  ```
2. Az Azure-hálózat konfigurációs fájl exportálása a következő parancs futtatásával. Módosíthatja a fájlt egy másik helyre, szükség esetén helyét.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
3. Nyissa meg az .xml fájlt a szerkesztéshez letöltött. A hálózati konfigurációs fájl példa: a [hálózati konfigurációs séma](https://msdn.microsoft.com/library/jj157100.aspx).

### <a name="2-verify-the-gateway-subnet"></a>2. Ellenőrizze az átjáró alhálózatának
Az a **VirtualNetworkSites** elemet, adja hozzá egy átjáró-alhálózatot a virtuális hálózat, ha egy nem már létezik. A hálózati konfigurációs fájl használata, ha az átjáró alhálózatának névvel kell ellátni "GatewaySubnet" vagy az Azure nem ismeri fel és nem használható egy átjáró-alhálózatot.

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
A helyi hálózati telephely hozzáadhat jelenti. az erőforrás-kezelő virtuális hálózatot, amelyhez csatlakozni szeretne. Adja hozzá a **LocalNetworkSites** elemben, amely a fájl, ha egy már nem létezik. Ezen a ponton a konfigurációban az a vpngatewayaddress elemmel lehet egy másik érvényes nyilvános IP-címet, mert azt még nem hozta létre az átjáró a Resource Manager vnet. Az átjáró létrehozhatunk, ha azt cserélje le a helyőrző IP-cím megfelelő nyilvános IP-cím van hozzárendelve az erőforrás-kezelő átjáró.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.0.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. Rendelje hozzá a Vnetet a helyi hálózati telephely
Ebben a szakaszban a Vnetet a csatlakozáshoz használni kívánt helyi hálózati telephely adtuk meg. Ebben az esetben a korábban hivatkozott erőforrás-kezelő virtuális hálózat. Győződjön meg arról, hogy az megfelel-e. Ez a lépés nem hoz létre egy átjáró. Azt adja meg a helyi hálózaton, amelyhez az átjáró csatlakozni fognak.

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### <a name="5-save-the-file-and-upload"></a>5. Mentse a fájlt, majd töltse fel
Mentse a fájlt, majd importálja az Azure-bA a következő parancs futtatásával. Győződjön meg arról, hogy a fájl elérési útját a környezet szükség szerint módosíthatja.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Látni fogja, hogy sikerült-e az importálás megjelenítő hasonló eredményt.

        OperationDescription        OperationId                      OperationStatus                                                
        --------------------        -----------                      ---------------                                                
        Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### <a name="6-create-the-gateway"></a>6. Az átjáró létrehozása

Mielőtt futtatná az ebben a példában, tekintse meg a hálózati konfigurációs fájljának letöltött Azure vár, hogy pontos neveit. A hálózati konfigurációs fájlt a klasszikus virtuális hálózatok értékeket tartalmazza. Egyes esetekben a hagyományos Vneteket neve módosult a hálózati konfigurációs fájlban klasszikus VNet beállításait az Azure-portálon az üzembe helyezési modellekről eltérései miatt létrehozásakor. Például ha a klasszikus virtuális hálózat neve "Klasszikus VNet", és létrehozta a "ClassicRG" nevű erőforráscsoport létrehozásához használt Azure-portálon, a neve, amely a hálózati konfigurációs fájlban lévő alakítja át "Csoport ClassicRG klasszikus virtuális hálózat". A szóközöket tartalmazó VNet neve megadásakor idézőjelekbe értékét.


Az alábbi példát követve hozzon létre egy dinamikus útválasztási:

```powershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

Az átjáró állapotának használatával ellenőrizheti a **Get-AzureVNetGateway** parancsmag.

## <a name="creatermgw"></a>2. szakasz – az erőforrás-kezelő hálózatok átjáró konfigurálása
Az erőforrás-kezelő vnet VPN-átjáró létrehozásához kövesse az alábbi utasításokat. Ne indítsa el a lépéseket, amíg a nyilvános IP-címet a klasszikus virtuális hálózat átjáróként beolvasása után. 

1. Jelentkezzen be az Azure-fiókjával a PowerShell-konzolban. A következő parancsmag kéri a bejelentkezési hitelesítő adatok az Azure-fiók. A bejelentkezés után a fiók beállításait, hogy elérhetők az Azure PowerShell letöltése.

  ```powershell
  Connect-AzureRmAccount
  ``` 
   
  Olvassa be az Azure-előfizetések listáját.

  ```powershell
  Get-AzureRmSubscription
  ```
   
  Ha egynél több előfizetéssel rendelkezik, adja meg a használni kívánt előfizetést.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```
2. Hozzon létre egy helyi hálózati átjárót. Virtuális hálózatokban a helyi hálózati átjáró általában a helyszínt jelenti. A helyi hálózati átjáró ebben az esetben a klasszikus virtuális hálózat hivatkozik. Adjon neki egy nevet, amellyel Azure is hivatkozik rá, és is adja meg a címelőtag terület. Az Azure a megadott IP-címelőtagot a helyszíni helyre küldendő adatforgalom azonosítására használja. Ha az adatok itt később, úgy, hogy az átjáró létrehozása előtt kell módosítsa az értékeket, és futtassa újból a minta.
   
   **-Név** utal, hogy a helyi hálózati átjáró hozzárendelni kívánt név.<br>
   **-AddressPrefix** a klasszikus virtuális hálózat a címtér van.<br>
   **-GatewayIpAddress** a klasszikus virtuális hálózatot átjáró nyilvános IP-címét. Ne feledje módosítani a következő példával, hogy tükrözze a megfelelő IP-címet.<br>

  ```powershell
  New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
  -Location "West US" -AddressPrefix "10.0.0.0/24" `
  -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
  ```
3. Egy nyilvános IP-címet a virtuális hálózati átjáró számára engedélyezett az erőforrás-kezelő vnet kérelmet. A használni kívánt IP-címet nem adhatja meg. Az IP-címet a virtuális hálózati átjáró dinamikusan történik. Ez azonban nem jelenti azt, hogy az IP-cím változik. A csak a virtuális hálózati átjáró IP-cím módosításai, amikor az átjáró törölni, majd újból. Az átméretezés, alaphelyzetbe állítása vagy más belső karbantartás vagy frissítés az átjáró nem változik.

  Ebben a lépésben is hivatott egy változó, amely egy későbbi lépésben lesz szükség.

  ```powershell
  $ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
  -ResourceGroupName RG1 -Location 'EastUS' `
  -AllocationMethod Dynamic
  ```

4. Ellenőrizze, hogy a virtuális hálózati átjáró-alhálózatot. Ha nincs átjáró-alhálózat létezik-e, vegyen fel egyet. Győződjön meg arról, hogy az átjáró alhálózatának elnevezése *GatewaySubnet*.
5. Az alhálózat, a következő parancs futtatásával az átjáróként használt beolvasása. Ebben a lépésben azt is állítsa be a következő lépésben használandó változó.
   
   **-Név** az erőforrás-kezelő virtuális hálózat neve.<br>
   **-ResourceGroupName** az erőforráscsoport, amelyhez a virtuális hálózat társítva van. Az átjáró-alhálózatot a virtuális hálózat már léteznie kell, és névvel kell ellátni *GatewaySubnet* megfelelően működjön.<br>

  ```powershell
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
  -VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
  ``` 

6. Hozzon létre átjáró IP-címzési konfigurációját. Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. A következő minta használatával hozza létre az átjáró konfigurációját.

  Ebben a lépésben a **- SubnetId** és **- PublicIpAddressId** paramétereket kell átadni azonosítóját megadó tulajdonságot az alhálózatot és IP-cím objektumok, illetve. Nem használhat egy egyszerű karakterlánc. Ezek a változók van beállítva a lépésben a kérelem egy nyilvános IP-cím és a lépés az alhálózat beolvasása.

  ```powershell
  $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
  -Name gwipconfig -SubnetId $subnet.id `
  -PublicIpAddressId $ipaddress.id
  ```
7. Az erőforrás-kezelő virtuális hálózati átjáró létrehozása a következő parancs futtatásával. A `-VpnType` kell *RouteBased*. Vagy akár többet az átjáró létrehozása 45 percig is tarthat.

  ```powershell
  New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
  -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
  -IpConfigurations $gwipconfig `
  -EnableBgp $false -VpnType RouteBased
  ```
8. Másolja át a nyilvános IP-cím, a VPN-átjáró létrehozása után. Használja a klasszikus virtuális hálózat helyi hálózati beállításainak konfigurálásakor. A következő parancsmag használatával a nyilvános IP-cím beolvasása. A nyilvános IP-cím szerepel, a visszatérési *IP-cím*.

  ```powershell
  Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1
  ```

## <a name="localsite"></a>3. szakasz - a klasszikus virtuális hálózat helyi webhely beállításainak módosítása

Ebben a szakaszban dolgozik a klasszikus virtuális hálózaton. Lecseréli a helyőrző IP-címet, amely a helyi beállításokat telepíthet, amelyek a Resource Manager virtuális hálózat átjárón megadásakor használatos. 

1. A hálózati konfigurációs fájl exportálása.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Az érték egy szövegszerkesztővel, módosíthatók vpngatewayaddress elemmel. Cserélje le a helyőrző IP-címet az erőforrás-kezelő átjáró nyilvános IP-címét, és mentse a módosításokat.

  ```
  <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  ```
3. Importálja a módosított hálózati konfigurációs fájlt az Azure-bA.

  ```powershell
  Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
  ```

## <a name="connect"></a>A szakasz 4 - átjáró közötti kapcsolat létrehozása
Az átjárók közötti kapcsolat létrehozása a PowerShell szükséges. Szükség lehet a PowerShell-parancsmagok klasszikus verzióját használja az Azure-fiók hozzáadása. Ehhez használja **Add-AzureAccount**.

1. A PowerShell-konzolban a megosztott kulcs beállítása. Az parancsmagok futtatása előtt tekintse meg a hálózati konfigurációs fájljának letöltött Azure vár, hogy pontos neveit. A szóközöket tartalmazó VNet neve megadásakor idézőjelekbe egyetlen érték.<br><br>A következő példában **- VNetName** neve a klasszikus virtuális hálózaton, és **- LocalNetworkSiteName** a helyi hálózati telephelyhez megadott neve. A **- SharedKey** érték, amely Ön hozza létre, és adja meg. A példában használtuk "abc123", de Ön hozza létre és összetettebb használja. Figyeljen arra, hogy az itt megadott érték ugyanazt az értéket a következő lépés a kapcsolat létrehozásakor meg kell lennie. Meg kell jelennie a visszatérési **állapota: sikeres**.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName ClassicVNet `
  -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
  ```
2. A VPN-kapcsolat létrehozása a következő parancsok futtatásával:
   
  Állítsa be a változókat.

  ```powershell
  $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
  $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
  ```
   
  Hozza létre a kapcsolatot. Figyelje meg, hogy a **- ConnectionType** IPsec, nem Vnet2Vnet van.

  ```powershell
  New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
  -Location "East US" -VirtualNetworkGateway1 `
  $vnet02gateway -LocalNetworkGateway2 `
  $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```

## <a name="verify"></a>Szakasz 5 – ellenőrizze a kapcsolatokat.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>A kapcsolat a klasszikus virtuális hálózat és az erőforrás-kezelő virtuális hálózat ellenőrzése

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>A kapcsolat az erőforrás-kezelő virtuális hálózat és a klasszikus virtuális hálózat ellenőrzése

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Virtuális hálózatok közötti kapcsolat – gyakori kérdések

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

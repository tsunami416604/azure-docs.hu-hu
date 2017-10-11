---
title: "Az Azure-webhelyek kapcsolatok kényszerített bújtatás konfigurálása: klasszikus |} Microsoft Docs"
description: "Hogyan lehet irányítani, vagy \"kényszerített\" minden internetre irányuló forgalomnak a helyszíni helyre."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 5c0177f1-540c-4474-9b80-f541fa44240b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 79bf6892c823da282c3e763921e830f986419854
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Kényszerített bújtatás konfigurálása klasszikus üzemi modellel

A kényszerített bújtatás lehetővé teszi az átirányítási vagy "kényszerített" minden internetre irányuló forgalomnak biztonsági másolatot a helyszíni helyre vizsgálati és naplózási pont-pont VPN-alagúton keresztül. Ez az kritikus fontosságú biztonsági előfeltétele annak, hogy a legtöbb vállalati informatikai házirendek. Kényszerített bújtatás nélkül internetre irányuló forgalomnak a virtuális gépek Azure-ban lesz mindig haladnak át Azure hálózati infrastruktúráról közvetlenül kimenő csatlakozik az internethez, a beállítás lehetővé teszi vizsgálja meg, vagy a forgalom naplózása nélkül. Jogosulatlan Internet-hozzáférés is eredményezhet, információfelfedés vagy más típusú biztonsági problémákat.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ez a cikk bemutatja, hogyan konfigurálása, a kényszerített bújtatás a klasszikus telepítési modellel létrehozott virtuális hálózatokat. A kényszerített bújtatás PowerShell, nem a portálon keresztül használatával konfigurálható. Ha azt szeretné, a Resource Manager üzembe helyezési modellben a kényszerített bújtatás konfigurálása, a következő legördülő listából válassza a klasszikus cikk:

> [!div class="op_single_selector"]
> * [PowerShell – Klasszikus](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Követelmények és szempontok
Az Azure-ban kényszerített bújtatás konfigurálása virtuális hálózati felhasználó által definiált útvonalak (UDR) keresztül. Egy helyszíni hely irányít át forgalmat az Azure VPN gatewayhez alapértelmezett útvonalat kifejezve. A következő szakaszban azok az aktuális útválasztási táblázat és korlátozása útvonalak egy Azure virtuális hálózat:

* Minden egyes virtuális hálózati alhálózat van egy beépített, rendszer-útválasztási táblázatához. A rendszer útválasztási táblázatban az útvonalak a következő három csoport rendelkezik:

  * **Helyi VNet útvonalak:** közvetlenül és a cél virtuális gépek ugyanabban a virtuális hálózatban.
  * **A helyi útvonalak:** számára az Azure VPN gateway.
  * **Alapértelmezett útvonal:** közvetlenül kell az internethez. A privát IP-címek nem fedi le az előző két útvonalak szánt csomagok törlődnek.
* Felhasználó által definiált útvonalak kiadása létrehoz egy útválasztási táblázatot egy alapértelmezett útvonal hozzáadása, és majd társítani a virtuális hálózat alhálózat ezek alhálózatok kényszerített bújtatás engedélyezése az útválasztási táblázatban.
* Egy "alapértelmezett"nevű hely a létesítmények közötti helyi helyek között a virtuális hálózathoz be kell.
* A kényszerített bújtatás kell rendelni egy Vnetet, amely egy dinamikus útválasztási VPN-átjáró (nem a statikus átjárók) rendelkezik.
* A kényszerített bújtatás ExpressRoute a mechanizmus révén nincs konfigurálva, de ehelyett szerint engedélyezve van egy alapértelmezett útvonalat hirdet a ExpressRoute BGP társviszony-létesítési munkameneteket keresztül. Tekintse meg a [ExpressRoute dokumentációja](https://azure.microsoft.com/documentation/services/expressroute/) további információt.

## <a name="configuration-overview"></a>Konfigurálása – áttekintés
A következő példában a Frontend alhálózathoz nem kényszeríti a bújtatott. A munkaterhelések Frontend alhálózaton fogadja el, és a felhasználói kérésekre válaszol az internetről közvetlenül is. A közepes réteg és a háttérkiszolgáló alhálózatok kényszerítve vannak bújtatott. A kimenő kapcsolatokat ezek két alhálózat-Internet kell kényszerített vagy egy helyszíni hely keresztül az S2S VPN-alagutat átirányítva.

Ez lehetővé teszi, hogy korlátozza és vizsgálja meg a virtuális gépek Internet-hozzáféréssel, vagy miközben továbbra is a többrétegű szolgáltatás architektúrája szükséges engedélyezése a felhőalapú Azure-szolgáltatások. Is alkalmazhat kényszerített bújtatás a teljes virtuális hálózatokra Ha szerepel a virtuális hálózatok nem internetre munkaterhelések.

![Alagúthasználat kényszerítése](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Előkészületek
A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következő elemekkel.

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* A konfigurált virtuális hálózati. 
* Az Azure PowerShell-parancsmagok legújabb verzióját. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

## <a name="configure-forced-tunneling"></a>Kényszerített bújtatás konfigurálása
Az alábbi eljárás segítségével megadhatja, hogy kényszerített bújtatást a virtuális hálózat. A konfigurálás lépéseinek végrehajtásához a virtuális hálózat hálózati konfigurációs fájl tartozik.

```
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

Ebben a példában a virtuális hálózat MultiTier – VNet három alhálózatok rendelkezik: "Háttér", "Előtér" és "Midtier" alhálózatok, négy helyszínek közötti kapcsolatok: "DefaultSiteHQ", és három ágak. 

A lépések állítja be a "DefaultSiteHQ" az alapértelmezett hely kapcsolat a kényszerített bújtatás, és konfigurálja a Midtier és háttér-alhálózatok használata a kényszerített bújtatást.

1. Hozzon létre egy útválasztási táblázatban. Az útvonaltábla létrehozásához használja a következő parancsmagot.

  ```powershell
  New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
  ```
2. Adjon hozzá egy alapértelmezett útvonalat az útválasztási táblázathoz. 

  A következő példa egy alapértelmezett útvonalat ad az 1. lépésben létrehozott útválasztási táblázathoz. Vegye figyelembe, hogy az csak útvonal támogatott "0.0.0.0/0" a "A(z)" következő ugrás a cél-előtagot.

  ```powershell
  Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
  ```
3. Az útválasztási táblázatban az alhálózatok társítása. 

  Után létre lett hozva egy útválasztási táblázat és egy útvonalat, az alábbi példát követve adja hozzá, vagy rendeljen hozzá egy virtuális hálózat alhálózathoz útvonaltábla. A példa az útvonaltáblát "MyRouteTable" hozzáadása a Midtier és a háttérkiszolgáló alhálózatok MultiTier-VNet hálózatok.

  ```powershell
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
  ```
4. Rendeljen hozzá egy alapértelmezett webhelyet, a kényszerített bújtatást. 

  Az előző lépésben parancsmag mintaparancsfájlok az útvonaltábla létrehozása, és két VNet alhálózatok útvonaltábla társítva. A többi lépés helyet kell kijelölnie egy helyi a többhelyes kapcsolatok a virtuális hálózat között az alapértelmezett hely, vagy alagút.

  ```powershell
  $DefaultSite = @("DefaultSiteHQ")
  Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
  ```

## <a name="additional-powershell-cmdlets"></a>További PowerShell-parancsmagok
### <a name="to-delete-a-route-table"></a>Új útvonaltábla törlése

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Egy útválasztási táblázatot listájára

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Egy útválasztási táblázatot egy útvonal törlése

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Egy alhálózat egy útvonal eltávolítása

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Alhálózat társított útvonaltábla listázásához

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Az alapértelmezett hely eltávolítása a virtuális hálózat VPN-átjáró

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
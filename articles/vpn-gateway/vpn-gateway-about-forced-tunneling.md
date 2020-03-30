---
title: 'Azure VPN-átjáró: Kényszerített bújtatás konfigurálása – Helyek közötti kapcsolatok: klasszikus'
description: Az összes internethez kötött forgalom átirányítása vagy kényszerítése a helyszíni helyre.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: fe06257127ff352f68fb27d3507cee0229e31498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201577"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Kényszerített bújtatás konfigurálása klasszikus üzemi modellel

A kényszerített bújtatással a helyek közötti VPN-alagúton keresztül az internetre irányuló összes forgalom visszairányítható (kényszeríthető) a helyszíni helyre vizsgálat és naplózás céljából. Ez a legtöbb vállalati informatikai házirend kritikus biztonsági követelménye. Kényszerített bújtatás nélkül az Azure-beli virtuális gépekről érkező, internethez kötött forgalom mindig közvetlenül az internetre fog haladni az Azure hálózati infrastruktúrájából az internetre, anélkül, hogy lehetővé tenné a forgalom vizsgálatát vagy naplózását. A jogosulatlan internet-hozzáférés potenciálisan információhoz való illetéktelen hozzáféréshez vagy a biztonság más típusú megsértéséhez vezethet.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ez a cikk bemutatja a klasszikus telepítési modell használatával létrehozott virtuális hálózatok kényszerített bújtatásának konfigurálását. Kényszerített bújtatás konfigurálható a PowerShell használatával, nem a portálon keresztül. Ha az Erőforrás-kezelő telepítési modelljéhez kötelező bújtatáskonfigurálni szeretné, válassza az Erőforrás-kezelő cikket az alábbi legördülő listából:

> [!div class="op_single_selector"]
> * [PowerShell – Klasszikus](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Követelmények és megfontolások
Az Azure-ban kényszerített bújtatás virtuális hálózati felhasználó által definiált útvonalakon (UDR) keresztül konfigurálva van. A forgalom átirányítása egy helyszíni helyre az Azure VPN-átjáró alapértelmezett útvonalaként van kifejezve. A következő szakasz az útválasztási tábla és az Azure virtuális hálózat útvonalainak aktuális korlátozását sorolja fel:

* Minden virtuális hálózati alhálózat rendelkezik egy beépített rendszer-útválasztási táblával. A rendszerútválasztási tábla a következő három útvonalcsoportot sorasztja:

  * **Helyi virtuális hálózat útvonalai:** Közvetlenül a cél virtuális gépek ugyanabban a virtuális hálózatban.
  * **Helyszíni útvonalak:** Az Azure VPN-átjáróhoz.
  * **Alapértelmezett útvonal:** Közvetlenül az internetre. Az előző két útvonal által nem lefedett privát IP-címekre szánt csomagokat a rendszer elfogja dobni.
* A felhasználó által definiált útvonalak kiadásával létrehozhat egy útválasztási táblát egy alapértelmezett útvonal hozzáadásához, majd társíthatja az útválasztási táblát a virtuális hálózat alhálózat(oka)hoz, hogy lehetővé tegye a kényszerített bújtatást ezeken az alhálózatokon.
* Be kell állítania egy "alapértelmezett helyet" a virtuális hálózathoz kapcsolódó helyi telephelyek között.
* A kényszerített bújtatást olyan virtuális hálózathoz kell társossá tenni, amely dinamikus útválasztási VPN-átjáróval rendelkezik (nem statikus átjáróval).
* ExpressRoute kényszerített bújtatás nem konfigurált ezzel a mechanizmussal, hanem engedélyezve van a reklám egy alapértelmezett útvonal at ExpressRoute BGP társviszony-létesítési munkamenetek. További információt az [ExpressRoute dokumentációjában](https://azure.microsoft.com/documentation/services/expressroute/) talál.

## <a name="configuration-overview"></a>Konfiguráció áttekintése
A következő példában az előtér-alhálózat nincs kényszerített bújtatása. Az előtér-alhálózat munkaterhelései továbbra is közvetlenül fogadhatják és válaszolhatják az ügyfelek kéréseit az internetről. A középső rétegés háttér-alhálózatok kényszerített bújtatás. A két alhálózatról az internetre irányuló kimenő kapcsolatokat a rendszer kényszeríti vagy visszairányítja egy helyszíni helyre az S2S VPN-alagutak egyikén keresztül.

Ez lehetővé teszi, hogy korlátozza és vizsgálja meg az internet-hozzáférést a virtuális gépekről vagy felhőszolgáltatásokból az Azure-ban, miközben továbbra is engedélyezi a többrétegű szolgáltatásarchitektúrát. A kényszerített bújtatás a teljes virtuális hálózatokra is alkalmazható, ha a virtuális hálózatokban nincsenek internetfelé irányuló munkaterhelések.

![Kényszerített bújtatás](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Előkészületek
A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel:

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Konfigurált virtuális hálózat. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="to-sign-in"></a>Bejelentkezés

1. Nyissa meg emelt szintű jogosultságokkal rendelkező PowerShell-konzolját. A szolgáltatáskezelésre való váltáshoz használja ezt a parancsot:

   ```powershell
   azure config mode asm
   ```
2. Csatlakozás a fiókhoz. A következő példa segít a kapcsolódásban:

   ```powershell
   Add-AzureAccount
   ```

## <a name="configure-forced-tunneling"></a>Kényszerített bújtatás konfigurálása
A következő eljárás segít a virtuális hálózat kényszerített bújtatásának megadásában. A konfigurációs lépések megfelelnek a virtuális hálózat hálózati konfigurációs fájljának.

```xml
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

Ebben a példában a "MultiTier-VNet" virtuális hálózat három alhálózattal rendelkezik: "Előtér", "Midtier" és "Háttérrendszer" alhálózat, négy telephelyközi kapcsolattal: "DefaultSiteHQ" és három ág. 

A lépések a "DefaultSiteHQ" beállítást a kényszerített bújtatás alapértelmezett helykapcsolataként határozzák meg, és a Midtier és a Backend alhálózatokat kényszerített bújtatáshasználatára konfigurálják.

1. Útválasztási tábla létrehozása. Az alábbi parancsmag segítségével hozza létre az útvonaltáblát.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Adjon hozzá egy alapértelmezett útvonalat az útválasztási táblához. 

   A következő példa hozzáad egy alapértelmezett útvonalat az 1. Vegye figyelembe, hogy az egyetlen támogatott útvonal a "0.0.0.0"-os célelőtag a "VPNGateway" NextHop számára.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Társítsa az útválasztási táblát az alhálózatokhoz. 

   Az útválasztási tábla létrehozása és az útvonal hozzáadása után a következő példával adja hozzá vagy társíthatja az útvonaltáblát egy virtuális hálózat alhálózatához. A példa hozzáadja a "MyRouteTable" útvonaltáblát a VNet MultiTier-VNet Midtier és Backend alhálózatához.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Rendeljen hozzá egy alapértelmezett helyet a kényszerített bújtatáshoz. 

   Az előző lépésben a mintaparancslevél-parancsfájlok létrehozták az útválasztási táblát, és az útvonaltáblát a virtuális hálózat két alhálózatához társították. A fennmaradó lépés az, hogy válasszon ki egy helyi helyet a virtuális hálózat többhelyes kapcsolatai közül alapértelmezett helyként vagy alagútként.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>További PowerShell-parancsmagok
### <a name="to-delete-a-route-table"></a>Útvonaltábla törlése

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Útvonaltábla listázása

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Útvonal törlése útvonaltáblából

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Útvonal eltávolítása alhálózatból

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Az alhálózathoz társított útvonaltábla listázása

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Alapértelmezett hely eltávolítása virtuális hálózat VPN-átjárójából

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```

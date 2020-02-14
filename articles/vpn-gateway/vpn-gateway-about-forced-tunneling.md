---
title: 'Azure VPN Gateway: kényszerített bújtatás konfigurálása a helyek közötti kapcsolatokhoz: klasszikus'
description: Hogyan lehet irányítani, vagy "kényszerítéséhez" internetre irányuló összes forgalmat a helyszíni helyre.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: fe06257127ff352f68fb27d3507cee0229e31498
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201577"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Kényszerített bújtatás konfigurálása klasszikus üzemi modellel

A kényszerített bújtatással a helyek közötti VPN-alagúton keresztül az internetre irányuló összes forgalom visszairányítható (kényszeríthető) a helyszíni helyre vizsgálat és naplózás céljából. Ez a legtöbb vállalati informatikai kritikus fontosságú biztonsági követelményeket a szabályzatokat. Anélkül, hogy kényszerített bújtatást végez, internetre irányuló forgalmat a virtuális gépekről az Azure-ban fog mindig haladnak át az Azure hálózati infrastruktúráról közvetlenül meg az internethez, ezáltal lehetővé teszi a forgalmat, vagy vizsgálja meg a beállítás nélkül. Jogosulatlan Internet-hozzáférés potenciálisan vezethet információfelfedés vagy más biztonsági résekkel szemben.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ez a cikk konfigurálásán vezeti végig kényszerített bújtatás a klasszikus üzemi modellel létrehozott virtuális hálózatok esetében. Kényszerített bújtatás PowerShell-lel, nem a portálon keresztül konfigurálható. Ha a Resource Manager-alapú üzemi modellhez kényszerített bújtatást szeretne beállítani, válassza ki a Resource Manager-cikket a következő legördülő listából:

> [!div class="op_single_selector"]
> * [PowerShell – Klasszikus](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Követelmények és szempontok
Kényszerített bújtatás az Azure-beli virtuális hálózati felhasználó által megadott útvonalak (UDR) keresztül van konfigurálva. Egy alapértelmezett útvonalat az Azure VPN gateway kifejezése egy helyszíni helyre irányítja a forgalmat. A következő szakaszban azok az Azure Virtual Network az útválasztási tábla-és útvonalak a jelenlegi korlátozás:

* Minden egyes virtuális hálózat alhálózatához rendelkezik egy beépített, rendszer-útválasztási táblázatához. A rendszer útválasztási tábla az útvonalak a következő három csoport rendelkezik:

  * **Helyi VNet útvonalak:** Közvetlenül az azonos virtuális hálózatban található cél virtuális gépekre.
  * Helyszíni **útvonalak:** Az Azure VPN Gateway-hez.
  * **Alapértelmezett útvonal:** Közvetlenül az internethez. Nem fedi le az előző két útvonalak privát IP-címekre irányuló csomagokat a rendszer eldobja.
* Felhasználó által megadott útvonalak kiadása hozzon létre egy útválasztási táblázatot, adjon hozzá egy alapértelmezett útvonalat, és majd rendelheti hozzá az ezekhez az alhálózatokhoz a kényszerített bújtatás engedélyezése a virtuális hálózat alhálózat az útválasztási táblázatban.
* Meg kell állítania egy "alapértelmezett webhely" a létesítmények közötti helyek között a virtuális hálózathoz csatlakozik.
* Kényszerített bújtatás kell rendelni egy virtuális hálózattal, amely dinamikus útválasztási VPN-átjáró (nem a statikus átjárók) rendelkezik.
* Kényszerített bújtatás ExpressRoute nincs konfigurálva ez a mechanizmus keresztül, de ehelyett szerint engedélyezve van a társviszony-létesítési ExpressRoute BGP-munkamenetek használatával egy alapértelmezett útvonalat hirdet. További információkért tekintse meg a [ExpressRoute dokumentációját](https://azure.microsoft.com/documentation/services/expressroute/) .

## <a name="configuration-overview"></a>Konfiguráció áttekintése
A következő példában az előtérbeli alhálózat nem kényszerítetten bújtatott. Az előtérben levő alhálózathoz számítási feladatokhoz továbbra is fogadni és válaszol ügyfelek az internetről közvetlenül. A középső rétegbeli és a háttérbeli alhálózat kényszerítve bújtatott. Az internethez az alábbi két alhálózat bármely kimenő kapcsolatok kényszerített vagy átirányítva az S2S VPN-alagúton keresztül egy helyszíni helyhez lesz.

Ez lehetővé teszi, hogy korlátozza és vizsgálja meg az Internet-hozzáférést a virtuális gépek vagy felhőszolgáltatások az Azure-ban, miközben továbbra is szükséges a többrétegű szolgáltatások architektúra engedélyezése. Is alkalmazhat a kényszerített bújtatás a teljes virtuális hálózatok Ha sem internetkapcsolattal rendelkező számítási feladatok a virtuális hálózatokon.

![Alagúthasználat kényszerítése](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Előkészületek
A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következőkkel:

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Egy konfigurált virtuális hálózatot. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="to-sign-in"></a>Bejelentkezés

1. Nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal. A Service Management szolgáltatásra való váltáshoz használja a következő parancsot:

   ```powershell
   azure config mode asm
   ```
2. Csatlakozás a fiókhoz. A következő példa segít a kapcsolódásban:

   ```powershell
   Add-AzureAccount
   ```

## <a name="configure-forced-tunneling"></a>Kényszerített bújtatás konfigurálása
Az alábbi eljárás segítségével adja meg a kényszerített bújtatás egy virtuális hálózathoz. A konfigurációs lépések megegyeznek a virtuális hálózatok közötti hálózati konfigurációs fájlt.

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

Ebben a példában a virtuális hálózat MultiTier – VNet három alhálózatot tartalmaz: "Háttér", "Előtér" és "Midtier" alhálózatok, négy létesítmények közötti kapcsolat: "DefaultSiteHQ", és három ágat. 

A lépések a "DefaultSiteHQ' állítja az alapértelmezett hely kapcsolat a kényszerített bújtatás, és konfigurálja a Midtier, és a háttérbeli alhálózatok használata kényszerített bújtatás.

1. Útválasztási táblázat létrehozása. A következő parancsmag használatával az útválasztási táblázat létrehozása.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Adjon hozzá egy alapértelmezett útvonalat az útválasztási táblázathoz. 

   Az alábbi példa hozzáad egy alapértelmezett útvonalat az útválasztási táblázatban az 1. lépésben létrehozott. Vegye figyelembe, hogy az egyetlen útvonalat támogat a cél előtagjához "0.0.0.0/0" a "VPN" következő ugrás.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Az útválasztási táblázatban az alhálózatokhoz társítja. 

   Miután egy útválasztási tábla jön létre, és egy útvonalat, használja a következő példát hozzáadása és társít egy virtuális hálózat alhálózatához. A példa az útvonaltáblában "MyRouteTable" Midtier és a háttérbeli alhálózatok, MultiTier hálózatok ad hozzá.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. A kényszerített bújtatás, rendeljen hozzá egy alapértelmezett helyet. 

   Az előző lépésben a minta parancsmagot parancsprogramok az útválasztási táblázat létrehozása, és az útvonaltábla két virtuális hálózat alhálózatok társítva. A fennmaradó lépéseként válassza ki a többhelyes kapcsolatokat a virtuális hálózat között egy helyi alagút vagy alapértelmezett webhelyhez.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>További PowerShell-parancsmagok
### <a name="to-delete-a-route-table"></a>Útválasztási táblázat törlése

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Útválasztási táblázat listázásához

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Egy útválasztási táblázatot egy útvonal törlése

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Egy alhálózatról egy útvonal eltávolítása

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Az alhálózatokhoz társított útvonaltábla listázásához

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Az alapértelmezett hely eltávolítása egy virtuális hálózatok közötti VPN-átjáró

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```

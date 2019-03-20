---
title: 'Az Azure Site-to-Site-kapcsolatok kényszerített bújtatás konfigurálása: klasszikus |} A Microsoft Docs'
description: Hogyan lehet irányítani, vagy "kényszerítéséhez" internetre irányuló összes forgalmat a helyszíni helyre.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 5c0177f1-540c-4474-9b80-f541fa44240b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 0955d95ebfd9e1f72ed1da577bf3520a70b71624
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58008334"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Kényszerített bújtatás konfigurálása klasszikus üzemi modellel

Kényszerített bújtatás lehetővé teszi az átirányítási vagy "kényszerített" internetre irányuló összes forgalmat a helyszíni helyre biztonsági és vizsgálati és naplózási Site-to-Site VPN-alagúton keresztül. Ez a legtöbb vállalati informatikai kritikus fontosságú biztonsági követelményeket a szabályzatokat. Anélkül, hogy kényszerített bújtatást végez, internetre irányuló forgalmat a virtuális gépekről az Azure-ban fog mindig haladnak át az Azure hálózati infrastruktúráról közvetlenül meg az internethez, ezáltal lehetővé teszi a forgalmat, vagy vizsgálja meg a beállítás nélkül. Jogosulatlan Internet-hozzáférés potenciálisan vezethet információfelfedés vagy más biztonsági résekkel szemben.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ez a cikk konfigurálásán vezeti végig kényszerített bújtatás a klasszikus üzemi modellel létrehozott virtuális hálózatok esetében. Kényszerített bújtatás PowerShell-lel, nem a portálon keresztül konfigurálható. Ha szeretné a Resource Manager üzemi modell kényszerített bújtatás konfigurálása, válassza ki a cikk a Resource Manager az alábbi legördülő listából:

> [!div class="op_single_selector"]
> * [PowerShell – Klasszikus](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Követelmények és szempontok
Kényszerített bújtatás az Azure-beli virtuális hálózati felhasználó által megadott útvonalak (UDR) keresztül van konfigurálva. Egy alapértelmezett útvonalat az Azure VPN gateway kifejezése egy helyszíni helyre irányítja a forgalmat. A következő szakaszban azok az Azure Virtual Network az útválasztási tábla-és útvonalak a jelenlegi korlátozás:

* Minden egyes virtuális hálózat alhálózatához rendelkezik egy beépített, rendszer-útválasztási táblázatához. A rendszer útválasztási tábla az útvonalak a következő három csoport rendelkezik:

  * **Helyi VNet-útvonal:** Közvetlenül az a cél virtuális gépek ugyanazon a virtuális hálózaton.
  * **A helyszíni útvonalakat:** Az Azure VPN gatewayhez.
  * **Alapértelmezett útvonal:** Közvetlenül a az interneten. Nem fedi le az előző két útvonalak privát IP-címekre irányuló csomagokat a rendszer eldobja.
* Felhasználó által megadott útvonalak kiadása hozzon létre egy útválasztási táblázatot, adjon hozzá egy alapértelmezett útvonalat, és majd rendelheti hozzá az ezekhez az alhálózatokhoz a kényszerített bújtatás engedélyezése a virtuális hálózat alhálózat az útválasztási táblázatban.
* Meg kell állítania egy "alapértelmezett webhely" a létesítmények közötti helyek között a virtuális hálózathoz csatlakozik.
* Kényszerített bújtatás kell rendelni egy virtuális hálózattal, amely dinamikus útválasztási VPN-átjáró (nem a statikus átjárók) rendelkezik.
* Kényszerített bújtatás ExpressRoute nincs konfigurálva ez a mechanizmus keresztül, de ehelyett szerint engedélyezve van a társviszony-létesítési ExpressRoute BGP-munkamenetek használatával egy alapértelmezett útvonalat hirdet. Tekintse át a [az ExpressRoute dokumentációja](https://azure.microsoft.com/documentation/services/expressroute/) további információt.

## <a name="configuration-overview"></a>Konfiguráció áttekintése
A következő példában az előtérbeli alhálózat nem kényszerítetten bújtatott. Az előtérben levő alhálózathoz számítási feladatokhoz továbbra is fogadni és válaszol ügyfelek az internetről közvetlenül. A középső rétegbeli és a háttérbeli alhálózat kényszerítve bújtatott. Az internethez az alábbi két alhálózat bármely kimenő kapcsolatok kényszerített vagy átirányítva az S2S VPN-alagúton keresztül egy helyszíni helyhez lesz.

Ez lehetővé teszi, hogy korlátozza és vizsgálja meg az Internet-hozzáférést a virtuális gépek vagy felhőszolgáltatások az Azure-ban, miközben továbbra is szükséges a többrétegű szolgáltatások architektúra engedélyezése. Is alkalmazhat a kényszerített bújtatás a teljes virtuális hálózatok Ha sem internetkapcsolattal rendelkező számítási feladatok a virtuális hálózatokon.

![Alagúthasználat kényszerítése](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Előkészületek
A konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik a következő elemekkel.

* Azure-előfizetés. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Egy konfigurált virtuális hálózatot. 
* Az Azure PowerShell-parancsmagjainak legújabb verzióját. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

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

Ebben a példában a virtuális hálózat MultiTier – VNet rendelkezik három alhálózatot: "Előtér", "Midtier" és "Háttér", rendelkező alhálózatokhoz négy létesítmények közötti kapcsolat: "DefaultSiteHQ", és három ágak. 

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

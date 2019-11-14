---
title: 'ExpressRoute: VNet összekapcsolása egy áramkörrel: klasszikus'
description: Ez a dokumentum áttekintést nyújt a virtuális hálózatok (virtuális hálózatok) ExpressRoute-áramkörökhöz való összekapcsolásáról a klasszikus üzemi modell és a PowerShell használatával.
services: expressroute
documentationcenter: na
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: cherylmc
ms.openlocfilehash: e02073e777c62be00b5c25c2242294e54795a0d4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031610"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-áramkörrel a PowerShell (klasszikus) használatával
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
>

Ez a cikk segítséget nyújt a virtuális hálózatok (virtuális hálózatok) és az Azure ExpressRoute-áramkörök összekapcsolásához a PowerShell használatával. Egyetlen virtuális hálózat legfeljebb négy ExpressRoute-Kapcsolatcsoportok lehet kapcsolódni. A cikkben ismertetett lépéseket követve létrehozhat egy új hivatkozást minden olyan ExpressRoute-áramkörhöz, amelyhez csatlakozik. Az ExpressRoute-Kapcsolatcsoportok ugyanahhoz az előfizetéshez tartozik, eltérő előfizetésekben vagy mindkét vegyesen is lehet. Ez a cikk a klasszikus üzembe helyezési modellel létrehozott virtuális hálózatokra vonatkozik.

Akár 10 virtuális hálózatot is összekapcsolhat egy ExpressRoute-áramkörhöz. Minden virtuális hálózatnak ugyanabban a geopolitikai régióban kell lennie. Nagyobb számú virtuális hálózatot csatolhat a ExpressRoute-áramkörhöz, vagy más geopolitikai régiókban található virtuális hálózatokat is összekapcsolhat, ha engedélyezi a ExpressRoute prémium bővítményét. A prémium szintű bővítménysel kapcsolatos további információkért olvassa el a [Gyakori kérdések](expressroute-faqs.md) című részt.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* Tekintse át a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
   * Az utasításokat követve [hozzon létre egy ExpressRoute áramkört](expressroute-howto-circuit-classic.md) , és engedélyezze a kapcsolati szolgáltatónak az áramkört.
   * Gondoskodjon arról, hogy az Azure privát társviszony-létesítést a kapcsolatcsoporthoz konfigurálva. Tekintse meg a [útválasztás konfigurálását](expressroute-howto-routing-classic.md) cikk vonatkozó útválasztási utasításokat.
   * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva legyen, és a BGP társviszony-létesítés a hálózat és a Microsoft között működik, így engedélyezheti a végpontok közötti kapcsolat.
   * Létre kell hoznia egy virtuális hálózatot és egy virtuális hálózati átjárót, és azt teljesen ki kell építeni. A [ExpressRoute virtuális hálózatának konfigurálásához](expressroute-howto-vnet-portal-classic.md)kövesse az utasításokat.

### <a name="download-the-latest-powershell-cmdlets"></a>A legújabb PowerShell-parancsmagok letöltése

Telepítse az Azure Service Management (SM) PowerShell-modulok és az ExpressRoute-modul legújabb verzióit. Az alábbi példa használata esetén vegye figyelembe, hogy a verziószáma (a példában 5.1.1-es) változik, amint a parancsmagok újabb verziói jelennek meg.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

Ha az Azure PowerShell-lel kapcsolatos további információra van szüksége, tekintse meg [Ismerkedés az Azure PowerShell-parancsmagok](/powershell/azure/overview) konfigurálása az Azure PowerShell-modulok használata a számítógép részletes útmutatást.

### <a name="sign-in"></a>Bejelentkezés

Az Azure-fiókba való bejelentkezéshez használja az alábbi példákat:

1. Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához.

   ```powershell
   Connect-AzAccount
   ```
2. Keresse meg a fiókot az előfizetésekben.

   ```powershell
   Get-AzSubscription
   ```
3. Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyiket használni szeretné.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

4. Ezután használja a következő parancsmagot az Azure-előfizetés hozzáadása a PowerShell a klasszikus üzemi modellhez.

   ```powershell
   Add-AzureAccount
   ```

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Azonos előfizetésben található virtuális hálózat csatlakoztatása egy kapcsolatcsoporthoz
A virtuális hálózatot a következő parancsmag használatával kapcsolhatja össze egy ExpressRoute-áramkörhöz. Győződjön meg arról, hogy a virtuális hálózati átjáró létrejött, és készen áll a csatolásra a parancsmag futtatása előtt.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Egy áramkörhöz tartozó virtuális hálózati kapcsolat eltávolítása
A következő parancsmag használatával eltávolíthatja a virtuális hálózati kapcsolatot egy ExpressRoute-áramkörhöz. Győződjön meg arról, hogy az aktuális előfizetés van kiválasztva az adott virtuális hálózathoz. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba
ExpressRoute-kapcsolatcsoport több előfizetésre kiterjedő megoszthatja. A következő ábrán látható egy egyszerű az ExpressRoute-Kapcsolatcsoportok hogyan megosztási alkotások sematikus több előfizetéshez.

A nagyméretű felhőbeli belül a kisebb felhők mindegyike egy szervezet különböző részlegei tartozó előfizetések megjelenítésére szolgál. A szervezeten belüli részlegek saját előfizetést használhatnak a szolgáltatásaik üzembe helyezéséhez, de a részlegek egyetlen ExpressRoute áramkört is megoszthatnak a helyszíni hálózathoz való kapcsolódáshoz. Egyetlen részleg (ebben a példában: informatikai) is a saját ExpressRoute-kapcsolatcsoportot. Más előfizetésekre, a szervezeten belül használható az ExpressRoute-kapcsolatcsoporthoz.

> [!NOTE]
> Az ExpressRoute-kapcsolatcsoport tulajdonosát a kapcsolatot és a sávszélesség díjak a dedikált kapcsolatcsoport lépnek érvénybe. Minden virtuális hálózat ossza meg ugyanazt a sávszélesség.
> 
> 

![Az előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Adminisztráció
Az *áramkör tulajdonosa* annak az előfizetésnek a rendszergazdája/rendszergazdája, amelyben az ExpressRoute-áramkör létrejött. Az áramkör tulajdonosa engedélyezheti a más előfizetések rendszergazdái vagy rendszergazdái *számára, hogy*a saját dedikált áramkört használják. Azok a felhasználók, akik jogosultak a szervezet ExpressRoute-áramkör használatára, az előfizetésben lévő virtuális hálózatot a hitelesítésük után a ExpressRoute-áramkörhöz kapcsolhatják.

A kapcsolatcsoport tulajdonosát a rendelkezik módosítja, és bármikor engedélyek visszavonása. Az engedélyek visszavonása az összes olyan hivatkozás törlését eredményezi, amelynek a hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Kapcsolatcsoport-tulajdonos műveletek

**Engedély létrehozása**

Az áramkör tulajdonosa más előfizetések rendszergazdái számára engedélyezi a megadott áramkör használatát. A következő példában az áramkör rendszergazdája (contoso IT) lehetővé teszi egy másik előfizetés (dev-test) rendszergazdájának, hogy két virtuális hálózatot kapcsoljon az áramkörhöz. A contoso informatikai rendszergazdája ezt a fejlesztési-tesztelési Microsoft-azonosító megadásával teszi lehetővé. A parancsmag nem küld e-mailt a megadott Microsoft-AZONOSÍTÓnak. Az áramkör tulajdonosának explicit módon értesítenie kell a másik előfizetés tulajdonosát az engedélyezés befejezéséről.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Visszatérési

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Engedélyek áttekintése**

A kapcsolatcsoport tulajdonosát a következő parancsmag futtatásával egy adott kapcsolatcsoportban kiállított összes engedélyek tekintheti át:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Visszatérési

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**Engedélyek frissítése**

Az áramkör tulajdonosa a következő parancsmaggal módosíthatja az engedélyeket:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Visszatérési

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Engedélyek törlése**

A kapcsolatcsoport tulajdonosát is visszavonása/törlése engedélyek a felhasználónak a következő parancsmagot:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Kapcsolatcsoport felhasználói műveletek

**Engedélyek áttekintése**

Az áramkör felhasználója a következő parancsmaggal ellenőrizheti az engedélyeket:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Visszatérési

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Kapcsolati engedélyek beváltása**

A kapcsolatcsoport felhasználói beváltása egy kapcsolat a következő parancsmag futtatásával:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Visszatérési

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Futtassa ezt a parancsot a virtuális hálózat újonnan csatolt előfizetésében:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Következő lépések

További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).

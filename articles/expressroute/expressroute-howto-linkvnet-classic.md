---
title: 'Azure ExpressRoute: Virtuális hálózat összekapcsolása egy áramkörhöz: klasszikus'
description: Ez a dokumentum áttekintést nyújt arról, hogyan kapcsolhatja össze a virtuális hálózatokat (virtuális hálózatokat) az ExpressRoute-áramkörökkel a klasszikus üzembe helyezési modell és a PowerShell használatával.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: cherylmc
ms.openlocfilehash: 53c200b01dfa6bce09cfc058dc24ab8e38d253a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930038"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Virtuális hálózat csatlakoztatása ExpressRoute-áramkörhöz a PowerShell használatával (klasszikus)
> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Powershell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure-portál](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
>

Ez a cikk segítséget nyújt a virtuális hálózatok (virtuális hálózatok) összekapcsolására a PowerShell használatával az Azure ExpressRoute-áramkörökkel. Egyetlen virtuális hálózat legfeljebb négy ExpressRoute-kapcsolatcsoporthoz kapcsolható. A cikkben ismertetett lépésekkel új hivatkozást hozhat létre minden olyan ExpressRoute-kapcsolatra, amelyhez csatlakozik. Az ExpressRoute-áramkörök lehetnek ugyanabban az előfizetésben, különböző előfizetések, vagy a kettő kombinációja. Ez a cikk a klasszikus telepítési modell használatával létrehozott virtuális hálózatokra vonatkozik.

Egy ExpressRoute-kapcsolathoz legfeljebb 10 virtuális hálózat kapcsolódhat. Minden virtuális hálózatnak ugyanabban a geopolitikai régióban kell lennie. Ha engedélyezi az ExpressRoute prémium szintű bővítmény engedélyezése, nagyobb számú virtuális hálózatot kapcsolhat össze az ExpressRoute-hálózattal, vagy összekapcsolhatja a más geopolitikai régiókban található virtuális hálózatokat. A prémium kiegészítővel kapcsolatos további részletekért tekintse meg a [GYIK-et.](expressroute-faqs.md)

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket,](expressroute-prerequisites.md) [útválasztási követelményeket](expressroute-routing.md)és [munkafolyamatokat.](expressroute-workflows.md)
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
   * Kövesse az utasításokat [egy ExpressRoute-kapcsolat létrehozásához,](expressroute-howto-circuit-classic.md) és a kapcsolatszolgáltató engedélyezze az áramkört.
   * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva van a kapcsolatcsoporthoz. Az útválasztási utasításokat az Útválasztási útmutató [konfigurálása](expressroute-howto-routing-classic.md) című cikkben találja.
   * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva van, és a BGP-társviszony-létesítés a hálózat és a Microsoft között, így lehetővé teszi a végpontok közötti kapcsolatot.
   * Virtuális hálózattal és virtuális hálózati átjáróval kell rendelkeznie, amelyet létrehozott és teljesen ki épített. Az Utasítások követéséhez [konfiguráljon egy virtuális hálózatot az ExpressRoute számára.](expressroute-howto-vnet-portal-classic.md)

### <a name="download-the-latest-powershell-cmdlets"></a>A legújabb PowerShell-parancsmagok letöltése

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Virtuális hálózat csatlakoztatása ugyanabban az előfizetésben egy áramkörhöz
A virtuális hálózat ot expressroute-kapcsolathoz a következő parancsmag használatával kapcsolhatja össze. Győződjön meg arról, hogy a virtuális hálózati átjáró létrejött, és készen áll a csatolásra a parancsmag futtatása előtt.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Virtuális hálózati kapcsolat eltávolítása áramkörhöz
Az ExpressRoute-áramkörhöz való virtuális hálózati kapcsolatot a következő parancsmag használatával távolíthatja el. Győződjön meg arról, hogy az aktuális előfizetés ki van jelölve az adott virtuális hálózathoz. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba
Az ExpressRoute-áramköröket több előfizetésen keresztül is megoszthatja. Az alábbi ábra egy egyszerű vázlatot mutat be, amely bemutatja, hogyan működik az ExpressRoute-áramkörök megosztása több előfizetésen keresztül.

A nagy felhőn belüli kisebb felhők mindegyike olyan előfizetéseket jelöl, amelyek a szervezet különböző részlegeihez tartoznak. A szervezet minden részlege használhatja a saját előfizetését a szolgáltatásaik üzembe helyezéséhez – de a részlegek egyetlen ExpressRoute-kapcsolaton osztozhatnak a helyszíni hálózathoz való csatlakozáshoz. Egyetlen részleg (ebben a példában: IT) birtokolhatja az ExpressRoute-áramkört. A szervezeten belüli egyéb előfizetések használhatják az ExpressRoute-áramkört.

> [!NOTE]
> A dedikált kapcsolati és sávszélesség-díjak az ExpressRoute-kapcsolat tulajdonosa lesz alkalmazva. Minden virtuális hálózat ugyanazt a sávszélességet használja.
> 
> 

![Előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Adminisztráció
A *kapcsolatcsoport tulajdonosa* annak az előfizetésnek a rendszergazdája/társadminisztrátora, amelyben az ExpressRoute-kapcsolat létrejön. A kapcsolatcsoport tulajdonosa engedélyezheti más előfizetések rendszergazdáinak/társrendszergazdáinak, a továbbiakban: *a felhasználói körcsoport-felhasználók*számára, hogy a saját felhasználói dedikált áramkört használják. A szervezet ExpressRoute-áramkörének használatára jogosult áramköri felhasználók az előfizetésben lévő virtuális hálózatot az ExpressRoute-kapcsolattal kapcsolhatják össze az engedélyezésük után.

Az áramkör tulajdonosa bármikor módosíthatja és visszavonhatja az engedélyeket. Az engedélyezés visszavonása azt eredményezi, hogy az összes hivatkozás törlődik az előfizetésből, amelynek hozzáférését visszavonták.

### <a name="circuit-owner-operations"></a>Áramkörtulajdonosi műveletek

**Engedélyezés létrehozása**

A kapcsolatcsoport tulajdonosa felhatalmazza a rendszergazdák más előfizetések a megadott áramkör használatát. A következő példában a hálózatkapcsolati hálózat (Contoso IT) rendszergazdája lehetővé teszi, hogy egy másik előfizetés rendszergazdája (dev-test) legfeljebb két virtuális hálózatot kapcsoljon a hálózathoz. A Contoso-rendszergazda ezt a Microsoft-azonosító tesztelésének megadásával engedélyezi. A parancsmag nem küld e-mailt a megadott Microsoft-azonosítóra. A kör tulajdonosának explicit módon értesítenie kell a másik előfizetés tulajdonosát, hogy az engedélyezés befejeződött.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  Eredményük a következő:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Engedélyek áttekintése**

A kör tulajdonosa a következő parancsmag futtatásával ellenőrizheti az adott áramkörre kiadott összes engedélyt:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  Eredményük a következő:

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

Az áramkör tulajdonosa a következő parancsmag használatával módosíthatja az engedélyeket:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  Eredményük a következő:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Engedélyek törlése**

A kapcsolatcsoport tulajdonosa visszavonhatja/törölheti a felhasználónak szóló engedélyeket a következő parancsmag futtatásával:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Áramkörfelhasználói műveletek

**Engedélyek áttekintése**

Az áramkör felhasználója a következő parancsmag használatával tekintheti át az engedélyeket:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  Eredményük a következő:

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

**Hivatkozási engedélyek beváltása**

Az áramkör-felhasználó a következő parancsmast futtathatja a kapcsolatengedélyezés beváltásához:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  Eredményük a következő:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Futtassa ezt a parancsot a virtuális hálózat újonnan csatolt előfizetésében:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>További lépések

Az ExpressRoute-ról további információt az [ExpressRoute gyakori kérdések című](expressroute-faqs.md)témakörben talál.

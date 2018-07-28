---
title: 'Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoport: PowerShell: klasszikus: Azure |} A Microsoft Docs'
description: Ez a dokumentum az ExpressRoute-Kapcsolatcsoportok összekapcsolása virtuális hálózatok (Vnetek) áttekintést nyújt a klasszikus üzemi modell és a PowerShell használatával.
services: expressroute
documentationcenter: na
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: ganesr
ms.openlocfilehash: e598249d0065bde8b3fe74883da8a0e39c9bc7c7
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308085"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporthoz a PowerShell (klasszikus) használatával
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Videó – Azure portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-linkvnet-classic.md)
>

Ez a cikk segítséget nyújt a virtuális hálózatok (Vnetek) összekapcsolása az Azure ExpressRoute-Kapcsolatcsoportok a klasszikus üzemi modell és a PowerShell használatával. Virtuális hálózatok ugyanabban az előfizetésben vagy egy másik előfizetés részeként is szerepelhetnek.

Egyetlen virtuális hálózat legfeljebb négy ExpressRoute-Kapcsolatcsoportok lehet kapcsolódni. Ez a cikk a lépéseket követve hozzon létre új kapcsolatot minden egyes ExpressRoute-kapcsolatcsoporthoz csatlakozik. Az ExpressRoute-Kapcsolatcsoportok ugyanahhoz az előfizetéshez tartozik, eltérő előfizetésekben vagy mindkét vegyesen is lehet.

Legfeljebb 10 virtuális hálózatok csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz kapcsolat. Az összes virtuális hálózatok ugyanazon geopolitikai régióban kell lennie. Kapcsolat egy nagyobb számú virtuális hálózatokat az ExpressRoute-kapcsolatcsoport vagy hivatkozás virtuális hálózatok, amelyek a geopolitikai régiók, ha az ExpressRoute prémium bővítmény engedélyezve. Ellenőrizze a [– gyakori kérdések](expressroute-faqs.md) kapcsolatos további részletekért a premium bővítményt.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembe helyezési modelljeiről**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* Tekintse át a [Előfeltételek](expressroute-prerequisites.md), [útválasztási követelmények](expressroute-routing.md), és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége.
   * Kövesse az utasításokat [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md) , és engedélyezze a kapcsolatcsoportot kapcsolatszolgáltatójával rendelkezik.
   * Gondoskodjon arról, hogy az Azure privát társviszony-létesítést a kapcsolatcsoporthoz konfigurálva. Tekintse meg a [útválasztás konfigurálását](expressroute-howto-routing-classic.md) cikk vonatkozó útválasztási utasításokat.
   * Győződjön meg arról, hogy az Azure privát társviszony-létesítés konfigurálva legyen, és a BGP társviszony-létesítés a hálózat és a Microsoft között működik, így engedélyezheti a végpontok közötti kapcsolat.
   * Rendelkeznie kell egy virtuális hálózat és a egy virtuális hálózati átjáró létrehozása, és teljesen kiépítve. Kövesse az utasításokat [virtuális hálózat konfigurálása expressroute](expressroute-howto-vnet-portal-classic.md).

### <a name="download-the-latest-powershell-cmdlets"></a>Töltse le a legújabb PowerShell-parancsmagok

Az Azure PowerShell-modulok legújabb verziójára van szüksége. Letöltheti a legújabb PowerShell-modulok PowerShell szakaszában a [Azure letöltőoldala](https://azure.microsoft.com/downloads/). Kövesse a [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview) konfigurálása az Azure PowerShell-modulok használata a számítógép részletes útmutatást.

Emellett meg kell töltse le az ExpressRoute-modult. A következő Példaparancsok használatával töltse le az Azure és az ExpressRoute-modulok. Ezek a parancsok használatakor vegye figyelembe, hogy a verziószáma (a példában 5.1.1-es) változik, amint a parancsmagok újabb verziói jelennek meg.

```powershell
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\Azure\Azure.psd1'
Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.1.1\ExpressRoute\ExpressRoute.psd1'
```

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Azonos előfizetésben található virtuális hálózat csatlakoztatása egy kapcsolatcsoporthoz
Egy virtuális hálózatot, összekapcsolása egy ExpressRoute-kapcsolatcsoportot, az alábbi parancsmag használatával. Győződjön meg arról, hogy a virtuális hálózati átjáró jön létre, és készen áll a csatolás, a parancsmag futtatása előtt.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Virtuális hálózat bevonása egy kapcsolatcsoportba hivatkozás eltávolítása
Egy virtuális hálózati kapcsolat ExpressRoute-kapcsolatcsoporttal történő eltávolításához az alábbi parancsmag használatával. Győződjön meg arról, hogy az aktuális előfizetésben az adott virtuális hálózathoz van-e kiválasztva. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Egy másik előfizetéshez tartozó virtuális hálózat bevonása egy kapcsolatcsoportba
ExpressRoute-kapcsolatcsoport több előfizetésre kiterjedő megoszthatja. A következő ábrán látható egy egyszerű az ExpressRoute-Kapcsolatcsoportok hogyan megosztási alkotások sematikus több előfizetéshez.

A nagyméretű felhőbeli belül a kisebb felhők mindegyike egy szervezet különböző részlegei tartozó előfizetések megjelenítésére szolgál. A részlegek számára a szervezeten belül minden egyes használhatja a saját előfizetés, üzembe helyezése a szolgáltatásaik –, de a szervezeti egységek oszthatnak meg egyetlen ExpressRoute-kapcsolatcsoporthoz szeretne csatlakozni a helyszíni hálózat számára. Egyetlen részleg (ebben a példában: informatikai) is a saját ExpressRoute-kapcsolatcsoportot. Más előfizetésekre, a szervezeten belül használható az ExpressRoute-kapcsolatcsoporthoz.

> [!NOTE]
> Az ExpressRoute-kapcsolatcsoport tulajdonosát a kapcsolatot és a sávszélesség díjak a dedikált kapcsolatcsoport lépnek érvénybe. Minden virtuális hálózat ossza meg ugyanazt a sávszélesség.
> 
> 

![Az előfizetések közötti kapcsolat](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Adminisztráció
A *kapcsolatcsoport tulajdonosát* az előfizetés, amelyben az ExpressRoute-kapcsolatcsoport létrehozása rendszergazdai/coadministrator van. A kapcsolatcsoport tulajdonosát a rendszergazdák és társrendszergazdák más előfizetések néven adhatják *kapcsolatcsoport-felhasználó*ahhoz, hogy a saját dedikált Kapcsolatcsoportok. Kapcsolatcsoport felhasználók, akik a szervezet ExpressRoute-kapcsolatcsoport használatára jogosult kapcsolat előfizetésében a virtuális hálózat az ExpressRoute-kapcsolatcsoport után jogosultak.

A kapcsolatcsoport tulajdonosát a rendelkezik módosítja, és bármikor engedélyek visszavonása. Az engedély visszavonása azt eredményezi, hogy törli az előfizetést, amelynek hozzáférését visszavonták. az összes hivatkozást.

### <a name="circuit-owner-operations"></a>Kapcsolatcsoport-tulajdonos műveletek

**Az engedély létrehozása**

A kapcsolatcsoport tulajdonosát a megadott expressroute-kapcsolatcsoport használandó más előfizetések rendszergazdái engedélyezi. A következő példában a rendszergazda a kapcsolatcsoport (Contoso IT) lehetővé teszi a rendszergazda egy másik előfizetés (Dev-Test) legfeljebb két virtuális hálózat összekapcsolása a kapcsolatcsoportot. A Contoso informatikai rendszergazda lehetővé teszi, hogy ez adja meg a Microsoft fejlesztési-tesztelési azonosítója. A parancsmag nem küld e-mailt a megadott Microsoft-azonosító. A kapcsolatcsoport tulajdonosát kell, hogy helyesek-e az engedélyezési explicit módon értesítse az előfizetés tulajdonosa.

    New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'

    Description         : Dev-Test Links
    Limit               : 2
    LinkAuthorizationId : **********************************
    MicrosoftIds        : devtest@contoso.com
    Used                : 0

**Engedélyek ellenőrzése**

A kapcsolatcsoport tulajdonosát a következő parancsmag futtatásával egy adott kapcsolatcsoportban kiállított összes engedélyek tekintheti át:

    Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"

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


**Engedélyek frissítése**

A kapcsolatcsoport tulajdonosát a következő parancsmag használatával módosíthatja a engedélyek:

    Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5

    Description         : Dev-Test Links
    Limit               : 5
    LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    MicrosoftIds        : devtest@contoso.com
    Used                : 0


**Engedélyek törlése**

A kapcsolatcsoport tulajdonosát is visszavonása/törlése engedélyek a felhasználónak a következő parancsmagot:

    Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"


### <a name="circuit-user-operations"></a>Kapcsolatcsoport felhasználói műveletek

**Engedélyek ellenőrzése**

A kapcsolatcsoport-felhasználó engedélyeinek tekintse át a következő parancsmag használatával:

    Get-AzureAuthorizedDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : ContosoIT
    Location                         : Washington DC
    MaximumAllowedLinks              : 2
    ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled
    UsedLinks                        : 0

**Kapcsolatok hitelesítéseinek váltja be**

A kapcsolatcsoport felhasználói beváltása egy kapcsolat a következő parancsmag futtatásával:

    New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'

    State VnetName
    ----- --------
    Provisioned SalesVNET1

Ez a parancs futtatása az újonnan társított előfizetés, a virtuális hálózat:

    New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"

## <a name="next-steps"></a>További lépések
További információ az ExpressRoute-tal kapcsolatban: [ExpressRoute – Gyakori kérdések](expressroute-faqs.md).


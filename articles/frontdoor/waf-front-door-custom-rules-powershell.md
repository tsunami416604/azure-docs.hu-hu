---
title: A webes házirendek alkalmazása tűzfal (WAF) konfigurálása egyéni szabályok és Ruse beállított alapértelmezett bejárati ajtajának – Azure PowerShell-lel
description: Ismerje meg, hogyan konfigurálhatja a WAF házirend állnak, amelyek egyéni és a felügyelt a szabályok egy meglévő bejárati ajtajának végpontot.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud;tyao
ms.openlocfilehash: 7d024dd958e6b29b52f095a9a55a67154bf6cde6
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362996"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Azure PowerShell-lel webes alkalmazás tűzfalházirend konfigurálása
Azure-alapú webes alkalmazás tűzfal (WAF) szabályzat határozza meg a szükséges, amikor kérelem érkezik a bejárati ajtajának ellenőrzések.
Ez a cikk bemutatja, hogyan konfigurálhatja a WAF-szabályzatot, amely tartalmaz néhány egyéni szabályok és az Azure által felügyelt alapértelmezett Ruse beállítása engedélyezve van.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Egy Sebességkorlát-szabályzat beállítása előtt állítsa be a PowerShell-környezetet, és a bejárati ajtajának profil létrehozása.
### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az utasításokat az oldalon, jelentkezzen be Azure hitelesítő adataival, és Az PowerShell-modul telepítéséhez.

#### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
```
Connect-AzAccount

```
A Front Door-modul telepítése előtt győződjön meg arról, hogy telepítve van a PowerShellGet aktuális verziója. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor modul telepítése 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Bejárati ajtajának profil létrehozása
Hozzon létre egy bejárati ajtajának profilt leírt utasítások alapján [a rövid útmutató: Bejárati ajtajának profil létrehozása](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>A http-paraméterek alapján egyéni szabály

Az alábbi példa bemutatja, hogyan állítson be egy egyéni szabály használatával két egyezési feltételei [New-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject). Kérelmek adott helyről történő hivatkozó által meghatározott, és lekérdezési karakterlánc nem tartalmaz "password". 

```powershell-interactive
$referer = New-AzFrontDoorMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Http-kérési metódust alapuló egyéni szabály
Hozzon létre egy szabályt, blokkolja a "PUT" módszer használatával [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject) módon:

```powershell-interactive
$put = New-AzFrontDoorMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Hozzon létre egy egyéni szabályt megkötés mérete alapján

A következő példában létrehozunk egy szabály blokkolja a kérelmeket az Azure PowerShell-lel 100 karakternél hosszabb URL-cím:
```powershell-interactive
$url = New-AzFrontDoorMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Felügyelt alapértelmezett szabálykészletet hozzáadása

Az alábbi példa létrehoz egy felügyelt alapértelmezett szabálykészletet Azure PowerShell-lel:
```powershell-interactive
$managedRules = New-AzFrontDoorManagedRuleObject -Type DefaultRuleSet -Version "preview-0.1"
```
## <a name="configure-a-security-policy"></a>Biztonsági szabályzat konfigurálása

Keresse meg az erőforráscsoport, amely tartalmazza a bejárati ajtajának profil használatával `Get-AzResourceGroup`. Ezután konfigurálja a biztonsági szabályzatot az előző lépésben létrehozott szabályok [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) , amely tartalmazza a bejárati ajtajának profil megadott erőforráscsoportban.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorFireWallPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Hivatkozás a házirend bejárati ajtajának előtér-gazdagéphez
A biztonsági csoportházirend-objektum csatolása meglévő bejárati ajtajának előtér-gazdagép és a bejárati ajtajának tulajdonságainak frissítése. Először lekérni a bejárati ajtajának objektum használatával [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Következő lépésként állítsa az előtér- *WebApplicationFirewallPolicyLink* tulajdonságot a *resourceId* az előző lépésben használatával létrehozott "$myWAFPolicy $" [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

Az alábbi példában az erőforráscsoport nevét használja *myResourceGroupFD1* feltételezve, hogy létrehozta a bejárati ajtó profil szereplő utasítások segítségével a [a rövid útmutató: Hozzon létre egy bejárati ajtajának](quickstart-create-front-door.md) cikk. Emellett az alábbi példában cserélje le $frontDoorName a bejárati ajtajának profil nevét. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Csak be kell állítani *WebApplicationFirewallPolicyLink* biztonsági szabályzat csatolása egy előtér-bejárati ajtajának egyszer tulajdonság. További frissítései a rendszer automatikusan alkalmazza az előtér-a.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [bejárati ajtajának](front-door-overview.md) 
- Tudjon meg többet [bejárati ajtó a WAF](waf-overview.md)

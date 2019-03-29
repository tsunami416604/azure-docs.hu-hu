---
title: Oktatóanyag – a földrajzi szűrés beállítása webes alkalmazás tűzfal-házirend Azure bejárati ajtajának szolgáltatás
description: Ebben az oktatóanyagban elsajátíthatja, hogyan hozhat létre egy egyszerű geoszűrési szabályzatot, és társíthatja azt a meglévő előtérbeli Front Door-gazdagéphez
services: frontdoor
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: bdbf0d78b45291e7482c1af3999c8ce3980ef36f
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578492"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Számára a bejárati ajtó a földrajzi szűrés WAF szabályzat beállítása
Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy egyszerű geoszűrési szabályzatot, és társíthatja azt a meglévő előtérbeli Front Door-gazdagéphez az Azure PowerShell-lel. Ez a minta geoszűrési szabályzat az Egyesült Államok kivételével minden más országból érkező kérelmeket letiltja.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek
Geoszűrő szabályzat beállítása előtt állítsa be a PowerShell környezetet, és bejárati ajtajának profil létrehozásához.
### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az utasításokat az oldalon, jelentkezzen be Azure hitelesítő adataival, és Az PowerShell-modul telepítéséhez.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Az Azure-bA összekapcsolása a bejelentkezéshez egy interaktív párbeszédpanel
```
Connect-AzAccount
Install-Module -Name Az
```
Ellenőrizze, hogy telepítve van a PowerShellGet aktuális verziójával rendelkezik. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor modul telepítése 

```
Install-Module -Name Az.FrontDoor -AllowPrerelease
```

### <a name="create-a-front-door-profile"></a>Bejárati ajtajának profil létrehozása
Hozzon létre egy bejárati ajtajának profilt leírt utasítások alapján [a rövid útmutató: Hozzon létre egy bejárati ajtajának profilt](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Adja meg a földrajzi szűrés feltételnek megfelelő

Hozzon létre egy minta az egyezési feltétellel, ami nem jelenik meg az "US" kérelmek [New-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject) paraméterekkel egyeztetési feltételt létrehozásakor. Az ország-lel két betű országkódok biztosított [Itt](front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>A geoszűrési egyeztetési feltétel hozzáadása egy szabályhoz egy művelettel és egy prioritással

Hozzon létre egy tűzfalhoz objektumot `nonUSBlockRule` az egyezési feltétellel, a művelet és a egy prioritását az alapján [New-AzFrontDoorCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorcustomruleobject).  Egy CustomRule (egyéni szabály) objektum több MatchCondition (egyeztetési feltétel) objektummal is rendelkezhet.  Ebben a példában a Művelet a Blokkolás értékre, a Prioritás pedig az 1 értékre, a legmagasabb prioritásra van állítva.

```
$nonUSBlockRule = New-AzFrontDoorCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Szabályok hozzáadása egy házirend
Keresse meg az erőforráscsoport, amely tartalmazza a bejárati ajtajának profil használatával `Get-AzResourceGroup`. Ezután hozzon létre egy `geoPolicy` csoportházirend-objektumot tartalmazó `nonUSBlockRule` használatával [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) , amely tartalmazza a bejárati ajtajának profil megadott erőforráscsoportban. Meg kell adnia egy egyedi nevet a földrajzi házirend. 

Az alábbi példában az erőforráscsoport nevét használja *myResourceGroupFD1* feltételezve, hogy létrehozta a bejárati ajtó profil szereplő utasítások segítségével a [a rövid útmutató: Hozzon létre egy bejárati ajtajának](quickstart-create-front-door.md) cikk. Az az alábbi példában cserélje le a szabályzat neve *geoPolicyAllowUSOnly* egy egyedi házirend neve.

```
$geoPolicy = New-AzFrontDoorFireWallPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>Hivatkozás WAF házirend bejárati ajtajának előtérbeli gazdagépre
A WAF-objektum csatolása a meglévő bejárati ajtajának előtérbeli gazdagép és a bejárati ajtajának tulajdonságainak frissítése. 

Ehhez először kérjen le a bejárati ajtajának objektum használatával [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Ezután állítsa a frontend WebApplicationFirewallPolicyLink tulajdonságot az erőforrás-azonosító, a `geoPolicy`használatával [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Csak egyszer WebApplicationFirewallPolicyLink tulajdonság beállítása mutató hivatkozást a WAF-házirend bejárati ajtajának előtérbeli gazdagépre kell. További frissítései a rendszer automatikusan alkalmazza az előtér-gazdagépre.

## <a name="next-steps"></a>További lépések

- További információ az [Azure Front Door alkalmazási rétegbeli biztonságáról](front-door-application-security.md).
- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.

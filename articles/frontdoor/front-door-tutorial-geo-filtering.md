---
title: Oktatóanyag – Geoszűrő WAF-házirend konfigurálása – Azure bejárati ajtó
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre geoszűrési szabályzatot, és társíthatja a szabályzatot a meglévő Bejárati ajtajának előtér-gazdagépével.
services: frontdoor
documentationcenter: ''
author: teresayao
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: tyao
ms.openlocfilehash: e3119745e35140d0344d25f34f54b63939d2542d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79471455"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Geoszűrési WAF-szabályzat beállítása a Front Doorhoz
Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy egyszerű geoszűrési szabályzatot, és társíthatja azt a meglévő előtérbeli Front Door-gazdagéphez az Azure PowerShell-lel. Ez a minta geoszűrési házirend blokkolja az Egyesült Államok kivételével az összes többi országból/régióból érkező kérelmeket.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené beállítani a geoszűrő szabályzatot, állítsa be a PowerShell-környezetet, és hozzon létre egy bejárati ajtó-profilt.
### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az utasításokat a lapon, az Azure-hitelesítő adatokkal való bejelentkezéshez, és telepítse az Az PowerShell-modult.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Csatlakozás az Azure-hoz egy interaktív párbeszédpanellel a bejelentkezéshez
```
Install-Module -Name Az
Connect-AzAccount
```
Győződjön meg arról, hogy a PowerShellJelenlegi verziója telepítve van. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Az Az.FrontDoor modul telepítése 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Bejárati ajtó profil létrehozása
Bejárati ajtó profil létrehozása a [Rövid útmutató: Bejárati ajtó profil létrehozása](quickstart-create-front-door.md)című részben leírt utasításokat követve.

## <a name="define-geo-filtering-match-condition"></a>Geoszűrési egyezési feltétel meghatározása

Hozzon létre egy mintaegyezési feltételt, amely kiválasztja a [new-azfrontdoorwafMatchConditionobject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) paraméterekkel nem az "US" -ból érkező kérelmeket az egyezési feltétel létrehozásakor. [Itt](front-door-geo-filtering.md)két betűs országkód szerepel az országtérképezéshez .

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>A geoszűrési egyeztetési feltétel hozzáadása egy szabályhoz egy művelettel és egy prioritással

Hozzon létre `nonUSBlockRule` egy CustomRule objektumot az egyezési feltétel, egy művelet és egy prioritás alapján a [New-AzFrontDoorWafCustomRuleObject használatával.](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)  Egy CustomRule (egyéni szabály) objektum több MatchCondition (egyeztetési feltétel) objektummal is rendelkezhet.  Ebben a példában a Művelet a Blokkolás értékre, a Prioritás pedig az 1 értékre, a legmagasabb prioritásra van állítva.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Szabályok hozzáadása házirendhez
Keresse meg annak az erőforráscsoportnak a `Get-AzResourceGroup`nevét, amely a Bejárati ajtó profilt tartalmazza a használatával. Ezután hozzon létre `geoPolicy` `nonUSBlockRule` egy házirend-objektumot, amely a [New-AzFrontDoorWafPolicy használatával](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) található a megadott erőforráscsoportban, amely tartalmazza a Bejárati ajtó profilt. Meg kell adnia egy egyedi nevet a geo-szűrési házirend. 

Az alábbi példa a *myResourceGroupFD1* erőforráscsoport nevet használja azzal a feltételezéssel, hogy a Bejárati ajtó profilt a [Rövid útmutató: Bejárati ajtó létrehozása](quickstart-create-front-door.md) című cikkben található utasítások alapján hozta létre. Az alábbi példában cserélje le a házirend nevét *geoPolicyAllowUSOnly* egy egyedi házirend nevét.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>WAF-házirend összekapcsolása frontajtó-előtér-állomással
Kapcsolja össze a WAF-házirendobjektumot a meglévő bejárati ajtajának előtér-állomásával, és frissítse a Bejárati ajtó tulajdonságait. 

Ehhez először olvassa be a Bejárati ajtó objektumot a [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor)használatával. 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Ezután állítsa be a frontend WebApplicationFirewallPolicyLink tulajdonságot a `geoPolicy` [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor)használatával használt erőforrásazonosítóra.

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Csak egyszer kell beállítania a WebApplicationFirewallPolicyLink tulajdonságot, hogy a WAF-házirendet egy bejárati ajtó előtér-állomáshoz csatolja. A további házirend-frissítések automatikusan az előtér-állomásra lesznek alkalmazva.

## <a name="next-steps"></a>További lépések
- További információ az [Azure webalkalmazás-tűzfalról.](waf-overview.md)
- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).

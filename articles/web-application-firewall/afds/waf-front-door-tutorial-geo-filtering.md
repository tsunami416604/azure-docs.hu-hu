---
title: A Geo-szűrési webalkalmazási tűzfal szabályzatának konfigurálása az Azure-beli előtérben szolgáltatáshoz
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy geo-szűrési házirendet, és hogyan társíthatja a házirendet a meglévő előtér-gazdagéphez.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: abcef61d478eccb4e979b60eb845ac8d398a49f9
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79135870"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Geo-szűrési WAF szabályzat beállítása a bejárati ajtóhoz

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy egyszerű geoszűrési szabályzatot, és társíthatja azt a meglévő előtérbeli Front Door-gazdagéphez az Azure PowerShell-lel. Ez a minta geo-szűrési házirend letiltja az összes többi országból/régiótól érkező kéréseket Egyesült Államok kivételével.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdi a Geo-szűrő házirend beállítását, állítsa be a PowerShell-környezetet, és hozzon létre egy bejárati profilt.
### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az oldalon megjelenő utasításokat, és jelentkezzen be az Azure-beli hitelesítő adataival, és telepítse az az PowerShell-modult.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Kapcsolódás az Azure-hoz egy interaktív párbeszédablak a bejelentkezéshez

```
Install-Module -Name Az
Connect-AzAccount
```
Győződjön meg arról, hogy a PowerShellGet aktuális verziója telepítve van. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Telepítés az. FrontDoor modul 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Bejárati ajtó profiljának létrehozása

Hozzon létre egy bevezető ajtót a következő témakörben ismertetett utasításokat követve [: első ajtós profil létrehozása](../../frontdoor/quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Geo-szűrési egyeztetési feltétel meghatározása

Hozzon létre egy minta egyeztetési feltételt, amely kiválasztja a "US" helyett a [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) paramétert az egyeztetési feltétel létrehozásakor. A [tartományon belüli, az Azure-beli bejárati](waf-front-door-geo-filtering.md)időpontokra való szűréssel kapcsolatban két betűs országkód van megadva.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>A geoszűrési egyeztetési feltétel hozzáadása egy szabályhoz egy művelettel és egy prioritással

Hozzon létre egy CustomRule `nonUSBlockRule` objektumot az egyeztetési feltétel, a művelet és a [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)használatával megadott prioritás alapján.  Egy CustomRule (egyéni szabály) objektum több MatchCondition (egyeztetési feltétel) objektummal is rendelkezhet.  Ebben a példában a Művelet a Blokkolás értékre, a Prioritás pedig az 1 értékre, a legmagasabb prioritásra van állítva.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Szabályok hozzáadása egy szabályzathoz

Keresse meg annak az erőforráscsoportnak a nevét, amely az `Get-AzResourceGroup`használatával tartalmazza a bejárati ajtót. Ezt követően hozzon létre egy `geoPolicy` házirend-objektumot, amely a megadott erőforráscsoport [új-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) használatával `nonUSBlockRule`t tartalmaz. Meg kell adnia a Geo-házirend egyedi nevét. 

Az alábbi példa a *myResourceGroupFD1* nevű erőforráscsoport-nevet használja azzal a feltételezéssel, hogy létrehozta az első ajtó profilt a gyors útmutató [: Create a bevezető ajtóról](../../frontdoor/quickstart-create-front-door.md) szóló cikk utasításai alapján. Az alábbi példában cserélje le a *geoPolicyAllowUSOnly* szabályzat nevét egy egyedi házirend-névre.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>WAF házirend csatolása egy előtér-gazdagéphez

Csatolja a WAF házirend-objektumot a meglévő előtér-gazdagéphez, és frissítse a bejárati ajtó tulajdonságait. 

Ehhez először a [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor)használatával olvassa be az előtérben lévő objektumot. 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Ezután állítsa a WebApplicationFirewallPolicyLink tulajdonságot a `geoPolicy`resourceId a [set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor)használatával.

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Csak egyszer kell beállítania a WebApplicationFirewallPolicyLink tulajdonságot, ha egy WAF-szabályzatot szeretne csatlakoztatni egy előtér-előtérben lévő gazdagéphez. A következő házirend-frissítéseket a rendszer automatikusan alkalmazza a előtér-gazdagépre.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg az [Azure webalkalmazási tűzfalat](../overview.md).
- [Frontdoor létrehozására](../../frontdoor/quickstart-create-front-door.md) vonatkozó információk.

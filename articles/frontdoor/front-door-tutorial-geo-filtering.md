---
title: Oktatóanyag – geo-szűrési WAF házirend konfigurálása – Azure bejárati ajtó
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy geo-szűrési házirendet, és hogyan társíthatja a házirendet a meglévő előtér-gazdagéphez.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: duau
ms.openlocfilehash: 31892232d5483bd2cb99d27c4672dbf347b904ef
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399021"
---
# <a name="how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Geoszűrési WAF-szabályzat beállítása a Front Doorhoz
Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy egyszerű geoszűrési szabályzatot, és társíthatja azt a meglévő előtérbeli Front Door-gazdagéphez az Azure PowerShell-lel. Ez a minta geo-szűrési házirend letiltja az összes többi országból/régiótól érkező kéréseket Egyesült Államok kivételével.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdi a Geo-szűrő házirend beállítását, állítsa be a PowerShell-környezetet, és hozzon létre egy bejárati profilt.
### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az oldalon megjelenő utasításokat, és jelentkezzen be az Azure-beli hitelesítő adataival, és telepítse az az PowerShell-modult.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Kapcsolódás az Azure-hoz interaktív párbeszédablak a bejelentkezéshez
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
Hozzon létre egy bevezető ajtót a következő témakörben ismertetett utasításokat követve [: első ajtós profil létrehozása](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Geo-szűrési egyeztetési feltétel meghatározása

Hozzon létre egy minta egyeztetési feltételt, amely kiválasztja a "US" helyett a [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) paramétert az egyeztetési feltétel létrehozásakor. [Itt](front-door-geo-filtering.md)jelennek meg az ország/régió-hozzárendelések két betűs országa/régiója.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>A geoszűrési egyeztetési feltétel hozzáadása egy szabályhoz egy művelettel és egy prioritással

Hozzon létre egy CustomRule objektumot `nonUSBlockRule` az egyeztetési feltétel, egy művelet és egy prioritás alapján a [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)használatával.  Egy CustomRule (egyéni szabály) objektum több MatchCondition (egyeztetési feltétel) objektummal is rendelkezhet.  Ebben a példában a Művelet a Blokkolás értékre, a Prioritás pedig az 1 értékre, a legmagasabb prioritásra van állítva.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Szabályok hozzáadása egy szabályzathoz
Keresse meg annak az erőforráscsoportnak a nevét, amely az első ajtó profilját tartalmazza a használatával `Get-AzResourceGroup` . Ezután hozzon létre egy olyan `geoPolicy` házirend-objektumot, amely a `nonUSBlockRule` [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) -t használja a megadott erőforráscsoporthoz, amely az előtérben profilt tartalmazza. Meg kell adnia a Geo-szűrési házirend egyedi nevét. 

Az alábbi példa a *myResourceGroupFD1* nevű erőforráscsoport-nevet használja azzal a feltételezéssel, hogy létrehozta a bevezető ajtó profilt a rövid útmutatóban található utasításokkal [: hozzon létre egy bejárati ajtót](quickstart-create-front-door.md) . Az alábbi példában cserélje le a *geoPolicyAllowUSOnly* szabályzat nevét egy egyedi házirend-névre.

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

Ezután állítsa a WebApplicationFirewallPolicyLink tulajdonságot a `geoPolicy` [set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor)resourceId.

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> Csak egyszer kell beállítania a WebApplicationFirewallPolicyLink tulajdonságot, ha egy WAF-szabályzatot szeretne csatlakoztatni egy előtér-előtérben lévő gazdagéphez. A következő házirend-frissítéseket a rendszer automatikusan alkalmazza a előtér-gazdagépre.

## <a name="next-steps"></a>Következő lépések
- Ismerje meg az [Azure webalkalmazási tűzfalat](waf-overview.md).
- Útmutató a [Front Door létrehozásához](quickstart-create-front-door.md).

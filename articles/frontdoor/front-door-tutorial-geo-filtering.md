---
title: Oktatóanyag – Tartomány geoszűrésének konfigurálása az Azure Front Door Service-ben | Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan hozhat létre egy egyszerű geoszűrési szabályzatot, és társíthatja azt a meglévő előtérbeli Front Door-gazdagéphez
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2018
ms.author: sharadag
ms.openlocfilehash: 68da9a0255cde6cbad5c675901c80193888bf255
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214878"
---
# <a name="how-to-set-up-a-geo-filtering-policy-for-your-front-door"></a>Geoszűrési szabályzat beállítása a Front Doorhoz
Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy egyszerű geoszűrési szabályzatot, és társíthatja azt a meglévő előtérbeli Front Door-gazdagéphez az Azure PowerShell-lel. Ez a minta geoszűrési szabályzat az Egyesült Államok kivételével minden más országból érkező kérelmeket letiltja.

## <a name="1-set-up-your-powershell-environment"></a>1. A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az oldalon megjelenő utasításokat az Azure-beli hitelesítő adataival való bejelentkezéshez és az AzureRM telepítéséhez.
```
# Connect to Azure with an interactive dialog for sign-in
Connect-AzureRmAccount
Install-Module -Name AzureRM
```
> [!NOTE]
>  Az [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) támogatása hamarosan elérhető lesz.

A Front Door-modul telepítése előtt győződjön meg arról, hogy telepítve van a PowerShellGet aktuális verziója. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

Telepítse az AzureRM.FrontDoor modult. 

```
Install-Module -Name AzureRM.FrontDoor -AllowPrerelease
```

## <a name="2-define-geo-filtering-match-conditions"></a>2. A geoszűrési egyeztetési feltétel vagy feltételek megadása
Először hozzon létre egy minta egyeztetési feltételt, amely kijelöli azokat a kérelmeket, melyek nem a „US” országból érkeznek. Az egyeztetési feltételek létrehozásakor használandó paraméterekről a PowerShell [útmutatójában](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoormatchconditionobject) találhat további információt. A kétbetűs országkódok országoknak való megfeleltetése [itt](front-door-geo-filtering.md) van megadva.

```
$nonUSGeoMatchCondition = New-AzureRmFrontDoorMatchConditionObject -MatchVariable RemoteAddr -OperatorProperty GeoMatch -NegateCondition $true -MatchValue "US"
```
 
## <a name="3-add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>3. A geoszűrési egyeztetési feltétel hozzáadása egy szabályhoz egy művelettel és egy prioritással

Ezután hozza létre a `nonUSBlockRule` CustomRule (egyéni szabály) objektumot az egyeztetési feltétel, egy Művelet és egy Prioritás alapján.  Egy CustomRule (egyéni szabály) objektum több MatchCondition (egyeztetési feltétel) objektummal is rendelkezhet.  Ebben a példában a Művelet a Blokkolás értékre, a Prioritás pedig az 1 értékre, a legmagasabb prioritásra van állítva.

```
$nonUSBlockRule = New-AzureRmFrontDoorCustomRuleObject -Name "geoFilterRule" -RuleType MatchRule -MatchCondition $nonUSGeoMatchCondition -Action Block -Priority 1
```

A CustomRule (egyéni szabály) objektumok létrehozásakor használandó paraméterekről a PowerShell [útmutatójában](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorcustomruleobject) találhat további információt.

## <a name="4-add-rules-to-a-policy"></a>4. Szabályok hozzáadása egy szabályzathoz
Ez a lépés létrehozza a `geoPolicy` szabályzatobjektumot, amely tartalmazza az előző lépésben létrehozott és a megadott erőforráscsoportban található `nonUSBlockRule` szabályt. A `Get-AzureRmResourceGroup` parancsmaggal keresheti meg a $resourceGroup erőforráscsoport-nevet.

```
$geoPolicy = New-AzureRmFrontDoorFireWallPolicy -Name "geoPolicyAllowUSOnly" -resourceGroupName $resourceGroup -Customrule $nonUSBlockRule  -Mode Prevention -EnabledState Enabled
```

A szabályzatok létrehozásakor használandó paraméterekről a PowerShell [útmutatójában](https://docs.microsoft.com/azure/frontdoor/new-azurermfrontdoorfirewallpolicy) találhat további információt.

## <a name="5-link-policy-to-a-front-door-frontend-host"></a>5. A szabályzat csatolása egy előtérbeli Front Door-gazdagéphez
Az utolsó lépések arra szolgálnak, hogy a védelmiszabályzat-objektumot egy meglévő előtérbeli Front Door-gazdagéphez csatolják, és frissítsék a Front Door tulajdonságait. Először be kell olvasnia a Front Door-objektumot a [Get-AzureRmFrontDoor](https://docs.microsoft.com/azure/frontdoor/get-azurermfrontdoor) parancsmaggal, majd be kell állítania annak előtérbeli WebApplicationFirewallPolicyLink tulajdonságát a `geoPolicy` erőforrás-azonosítójára.

```
$geoFrontDoorObjectExample = Get-AzureRmFrontDoor -ResourceGroupName $resourceGroup
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

A következő [paranccsal](https://docs.microsoft.com/azure/frontdoor/set-azurermfrontdoor) frissítse a Front Door-objektumot.

```
Set-AzureRmFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> A WebApplicationFirewallPolicyLink tulajdonságot csak egyszer kell beállítani a védelmi szabályzat előtérbeli Front Door-gazdagéphez való csatolásához. A későbbi szabályzatfrissítések automatikusan alkalmazva lesznek az előtérbeli gazdagépre.

## <a name="next-steps"></a>További lépések

- További információ az [Azure Front Door alkalmazási rétegbeli biztonságáról](front-door-application-security.md).
- [Frontdoor létrehozására](quickstart-create-front-door.md) vonatkozó információk.

---
title: Waf egyéni szabályok konfigurálása & Alapértelmezett szabálykészlet az Azure bejárati ajtajához
description: Megtudhatja, hogyan konfigurálhatja a WAF-szabályzat egyéni és felügyelt szabályok egy meglévő bejárati ajtajának végpontja.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186632"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Webalkalmazás-tűzfal házirend konfigurálása az Azure PowerShell használatával

Az Azure Web Application Firewall (WAF) szabályzat határozza meg a szükséges ellenőrzéseket, amikor egy kérelem érkezik a bejárati ajtót.
Ez a cikk bemutatja, hogyan konfigurálhat egy WAF-szabályzatot, amely néhány egyéni szabályból és az Azure által felügyelt alapértelmezett szabálykészletből áll.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené beállítani a díjkorlát-szabályzatot, állítsa be a PowerShell-környezetet, és hozzon létre egy bejárati ajtó-profilt.

### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása

Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az utasításokat a lapon, az Azure-hitelesítő adatokkal való bejelentkezéshez, és telepítse az Az PowerShell-modult.

#### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

```
Connect-AzAccount

```
A Front Door-modul telepítése előtt győződjön meg arról, hogy telepítve van a PowerShellGet aktuális verziója. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Az Az.FrontDoor modul telepítése 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Bejárati ajtó profil létrehozása

Bejárati ajtó profil létrehozása a [Rövid útmutató: Bejárati ajtó profil létrehozása című](../../frontdoor/quickstart-create-front-door.md) részben leírt utasítások szerint

## <a name="custom-rule-based-on-http-parameters"></a>Egyéni szabály http-paraméterek alapján

A következő példa bemutatja, hogyan konfigurálhat egy egyéni szabályt két egyezési feltétellel a [New-AzFrontDoorWafMatchConditionObject használatával.](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) A kérelmek egy megadott helyről származnak, a hogy a hivatkozó konkretinó konkretin, a lekérdezési karakterlánc pedig nem tartalmaz "jelszót". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Egyéni szabály http-kérelem metódus alapján

Hozzon létre egy szabályblokkoló "PUT" metódust a [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) használatával az alábbiak szerint:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Egyéni szabály létrehozása méretmegkötés alapján

A következő példa létrehoz egy szabályblokkoló kérelmek url-cím, amely hosszabb, mint 100 karakter az Azure PowerShell használatával:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Felügyelt alapértelmezett szabálykészlet hozzáadása

A következő példa létrehoz egy felügyelt alapértelmezett szabálykészletet az Azure PowerShell használatával:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Biztonsági házirend konfigurálása

Keresse meg annak az erőforráscsoportnak a `Get-AzResourceGroup`nevét, amely a Bejárati ajtó profilt tartalmazza a használatával. Ezután konfiguráljon egy biztonsági házirendet létrehozott szabályokkal az előző lépésekben a [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) használatával a megadott erőforráscsoportban, amely tartalmazza a Bejárati ajtó profilt.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Házirend csatolása bejárati ajtó előtér-állomásához

Kapcsolja össze a biztonsági házirend-objektumot egy meglévő bejárati ajtó előtér-állomásával, és frissítse a Bejárati ajtó tulajdonságait. Először olvassa be a Bejárati ajtó objektumot a [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)használatával.
Ezután állítsa be az *előtér-WebApplicationFirewallPolicyLink* tulajdonságot az előző lépésben létrehozott "$myWAFPolicy$" *erőforrásazonosítójára* a [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)segítségével. 

Az alábbi példa a *myResourceGroupFD1* erőforráscsoport nevet használja azzal a feltételezéssel, hogy a Bejárati ajtó profilt a [Rövid útmutató: Front Door cikkben](../../frontdoor/quickstart-create-front-door.md) megadott utasítások alapján hozta létre. Az alábbi példában cserélje le $frontDoorName a bejárati ajtó profiljának nevére. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Csak egyszer kell beállítania a *WebApplicationFirewallPolicyLink* tulajdonságot, hogy biztonsági házirendet csatoljon a bejárati ajtó előtéréhez. A további házirend-frissítések automatikusan az előtérre lesznek alkalmazva.

## <a name="next-steps"></a>További lépések

- További információ a [Bejárati ajtóról](../../frontdoor/front-door-overview.md) 
- További információ a [WAF-ról a bejárati ajtóval](afds-overview.md)

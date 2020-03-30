---
title: WaF-sebességkorlátozási szabály konfigurálása a Bejárati ajtóhoz – Azure PowerShell
description: Ismerje meg, hogyan konfigurálhat sebességkorlát-szabályt egy meglévő bejárati ajtajának végponthoz.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: b034159c3d12927f6425b3dc3c5b5609af9b0b76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649364"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Webalkalmazás-tűzfal sebességkorlátozási szabályának konfigurálása az Azure PowerShell használatával
Az Azure Web Application Firewall (WAF) sebességkorlát szabály az Azure Bejárati ajtó szabályozza az ügyfelek től egy perces időtartam alatt engedélyezett kérelmek számát.
Ez a cikk bemutatja, hogyan konfigurálhat egy WAF-díjkorlát-szabályt, amely szabályozza az ügyfelek által engedélyezett kérelmek számát egy olyan webalkalmazáshoz, amely az Azure PowerShell használatával tartalmazza a */promo* könyvtárat az URL-címben.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené beállítani a díjkorlát-szabályzatot, állítsa be a PowerShell-környezetet, és hozzon létre egy bejárati ajtó-profilt.
### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az utasításokat a lapon, az Azure-hitelesítő adatokkal való bejelentkezéshez, és telepítse az Az PowerShell-modult.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Csatlakozás az Azure-hoz egy interaktív párbeszédpanellel a bejelentkezéshez
```
Connect-AzAccount

```
A Bejárati ajtó modul telepítése előtt győződjön meg arról, hogy a PowerShellGet jelenlegi verziója telepítve van. Futtassa a következő parancsot, és nyissa meg újra a PowerShellt.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Az Az.FrontDoor modul telepítése 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Bejárati ajtó profil létrehozása
Bejárati ajtó profil létrehozása a [Rövid útmutató: Bejárati ajtó profil létrehozása című](../../frontdoor/quickstart-create-front-door.md) részben leírt utasítások szerint

## <a name="define-url-match-conditions"></a>URL-egyezési feltételek megadása
Adjon meg url-egyezési feltételt (URL-tartalmat tartalmaz /promo) a [New-AzFrontDoorWafMatchConditionObject segítségével.](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)
A következő példa a */promo-t* a *RequestUri* változó értékeként egyezteti:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Egyéni díjkorlát-szabály létrehozása
Állítsa be a sebességkorlátot a [New-AzFrontDoorWafCustomRuleObject objektummal.](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) A következő példában a korlát 1000-re van állítva. Az egyik percben bármely ügyféltől a promóciós oldalra érkező kérések blokkolva vannak, amíg a következő perc el nem kezdődik.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Biztonsági házirend konfigurálása

Keresse meg annak az erőforráscsoportnak a `Get-AzureRmResourceGroup`nevét, amely a Bejárati ajtó profilt tartalmazza a használatával. Ezután konfiguráljon egy egyéni sebességkorlát-szabályt tartalmazó biztonsági házirendet a Bejárati ajtó profilt tartalmazó megadott erőforráscsoport [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) használatával.

Az alábbi példa a *myResourceGroupFD1* erőforráscsoport nevet használja azzal a feltételezéssel, hogy a Bejárati ajtó profilt a [Rövid útmutató: Front Door cikkben](../../frontdoor/quickstart-create-front-door.md) megadott utasítások alapján hozta létre.

 [a New-AzFrontDoorWafPolicy használatával.](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Házirend csatolása bejárati ajtó előtér-állomásához
Kapcsolja össze a biztonsági házirend-objektumot egy meglévő bejárati ajtó előtér-állomásával, és frissítse a Bejárati ajtó tulajdonságait. Először olvassa be a Bejárati ajtó objektumot a [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) paranccsal.
Ezután állítsa be az *előtér-WebApplicationFirewallPolicyLink* tulajdonságot az előző lépésben létrehozott "$ratePolicy" *erőforrásazonosítójára* a [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) paranccsal. 

Az alábbi példa a *myResourceGroupFD1* erőforráscsoport nevet használja azzal a feltételezéssel, hogy a Bejárati ajtó profilt a [Rövid útmutató: Front Door cikkben](../../frontdoor/quickstart-create-front-door.md) megadott utasítások alapján hozta létre. Az alábbi példában cserélje le $frontDoorName a bejárati ajtó profiljának nevére. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Csak egyszer kell beállítania a *WebApplicationFirewallPolicyLink* tulajdonságot, hogy biztonsági házirendet csatoljon a bejárati ajtó előtéréhez. A további házirend-frissítések automatikusan az előtérre lesznek alkalmazva.

## <a name="next-steps"></a>További lépések

- További információ a [Bejárati ajtóról.](../../frontdoor/front-door-overview.md) 



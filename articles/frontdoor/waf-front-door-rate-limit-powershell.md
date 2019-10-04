---
title: Webalkalmazási tűzfal díjszabási szabályának konfigurálása a bejárati ajtók számára – Azure PowerShell
description: Megtudhatja, hogyan konfigurálhat egy kamatláb-korlátozási szabályt egy meglévő bejárati végponthoz.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 99af39e996aaadd572603f63d019ff929b679550
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846249"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Webalkalmazási tűzfal sebességének korlátozási szabályának konfigurálása Azure PowerShell használatával
Az Azure-beli Azure webalkalmazási tűzfal (WAF) díjszabási szabálya az Azure-beli bejárati ajtónál az egyperces időtartam alatt egyetlen ügyfél-IP-címről engedélyezett kérelmek számát szabályozza.
Ez a cikk bemutatja, hogyan konfigurálhat egy WAF-korlátozási szabályt, amely az adott ügyfél által az Azure PowerShell használatával az URL-címen található */promo* tartalmazó webalkalmazások számára engedélyezett kérelmek számát vezérli.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdi a díjszabási szabályzat beállítását, állítsa be a PowerShell-környezetet, és hozzon létre egy bejárati profilt.
### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az oldalon megjelenő utasításokat, és jelentkezzen be az Azure-beli hitelesítő adataival, és telepítse az az PowerShell-modult.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Kapcsolódás az Azure-hoz interaktív párbeszédablak a bejelentkezéshez
```
Connect-AzAccount

```
A Front Door-modul telepítése előtt győződjön meg arról, hogy telepítve van a PowerShellGet aktuális verziója. Futtassa az alábbi parancsot, majd nyissa meg a PowerShellt újból.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Telepítés az. FrontDoor modul 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Bejárati ajtó profiljának létrehozása
A rövid útmutatóban ismertetett [utasításokat követve hozzon létre egy bejárati ajtót. Bejárati ajtó profiljának létrehozása](quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>URL-egyeztetési feltételek meghatározása
Definiáljon egy URL-egyeztetési feltételt (az URL-cím/promo tartalmaz) a [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)használatával.
A következő példa a *RequestUri* változó értékének megfelelő */promo* illeszkedik:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Egyéni díjszabási szabály létrehozása
Adja meg a díjszabási korlátot a [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)használatával. A következő példában a korlát értéke 1000. Minden ügyféltől a promóciós oldalra érkező, a 1000-es időszakot meghaladó, egy percen belül érkező kéréseket a rendszer a következő percben letiltva

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Biztonsági házirend konfigurálása

Keresse meg annak az erőforráscsoportnak a nevét, amely az első ajtó profilját tartalmazza a használatával `Get-AzureRmResourceGroup`. Ezután állítson be egy biztonsági házirendet egyéni díjszabási szabályként a [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) használatával a megadott erőforráscsoport, amely tartalmazza az előtérben profilt.

Az alábbi példa a *myResourceGroupFD1* nevű erőforráscsoport-nevet használja azzal a feltételezéssel, hogy létrehozta az előtérben profilt a gyors útmutatóban megadott [utasításokkal: Hozzon létre egy](quickstart-create-front-door.md) bejárati ajtót.

 a [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)használata.

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Házirend csatolása egy előtér-előtéri gazdagéphez
Csatolja a biztonsági házirend objektumot egy meglévő előtér-előtéri gazdagéphez, és frissítse a bejárati ajtó tulajdonságait. Először a [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) parancs használatával olvassa be az előtérben lévő objektumot.
Ezután állítsa be az előtér- *WebApplicationFirewallPolicyLink* tulajdonságot az előző lépésben létrehozott "$RatePolicy" *resourceId* a [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) parancs használatával. 

Az alábbi példa a *myResourceGroupFD1* nevű erőforráscsoport-nevet használja azzal a feltételezéssel, hogy létrehozta az előtérben profilt a gyors útmutatóban megadott [utasításokkal: Hozzon létre egy](quickstart-create-front-door.md) bejárati ajtót. Emellett az alábbi példában cserélje le a $frontDoorName nevet a bejárati ajtó profiljának nevére. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> A *WebApplicationFirewallPolicyLink* tulajdonságot csak egyszer kell beállítania, hogy egy biztonsági szabályzatot egy előtér-előtérben csatoljon. A következő házirend-frissítéseket a rendszer automatikusan alkalmazza az előtérben.

## <a name="next-steps"></a>További lépések

- További információ a [](front-door-overview.md) bejárati ajtóról 



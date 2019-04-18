---
title: Webes alkalmazás arány korlát tűzfalszabály konfigurálása a bejárati ajtajának – Azure PowerShell-lel
description: Ismerje meg, hogy egy meglévő bejárati ajtajának végpontot arány korlát szabály konfigurálása.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2019
ms.author: kumud;tyao
ms.openlocfilehash: e0ad1e85a4cd47de823bc4f224b5a8834b1068b9
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685828"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Webes alkalmazás arány korlát tűzfalszabály konfigurálása az Azure PowerShell-lel
Az Azure-alapú webes alkalmazás tűzfal (WAF) arány korlátozó szabály Azure bejárati ajtajának szabályozza az egyetlen ügyfél IP-cím egy egy perces időszakra engedélyezett kérelmek száma.
Ez a cikk bemutatja, hogyan konfigurálhatja a WAF arány korlát szabály, amely szabályozza a webalkalmazásnak, amely tartalmazza az egyetlen ügyfél engedélyezett kérések számát */promo* az Azure PowerShell-lel URL-címben.

> [!IMPORTANT]
> A WAF forgalmi szabály funkció Azure bejárati ajtajának jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Egy Sebességkorlát-szabályzat beállítása előtt állítsa be a PowerShell-környezetet, és a bejárati ajtajának profil létrehozása.
### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az utasításokat az oldalon, jelentkezzen be Azure hitelesítő adataival, és Az PowerShell-modul telepítéséhez.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Az Azure-bA összekapcsolása a bejelentkezéshez egy interaktív párbeszédpanel
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

## <a name="define-url-match-conditions"></a>Adja meg az URL-cím egyezési feltételei
Adja meg az URL-címe (URL-címet tartalmaz /promo) egyeztetési feltételt használatával [New-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject).
A következő példa megegyezik */promo* értékeként a *RequestUri* változó:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Hozzon létre egy egyéni arány korlát szabályt
Nastavit pomocí arány korlátot [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject). A következő példában a korlát beállításé pedig 1000 van beállítva. Bármely ügyfél kéréseit, a promóciós oldalra, egy perc alatt 1000 meghaladó le lesznek tiltva, amíg el nem kezdi a következő percben.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Biztonsági szabályzat konfigurálása

Keresse meg az erőforráscsoport, amely tartalmazza a bejárati ajtajának profil használatával `Get-AzureRmResourceGroup`. Ezután konfigurálja a biztonsági szabályzat szabállyal egy egyéni arány korlát [New-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) , amely tartalmazza a bejárati ajtajának profil megadott erőforráscsoportban.

Az alábbi példában az erőforráscsoport nevét használja *myResourceGroupFD1* feltételezve, hogy létrehozta a bejárati ajtó profil szereplő utasítások segítségével a [a rövid útmutató: Hozzon létre egy bejárati ajtajának](quickstart-create-front-door.md) cikk.

 using [New-AzFrontDoorFireWallPolicy](/powershell/module/Az.FrontDoor/New-AzFrontDoorFireWallPolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorFireWallPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Hivatkozás a házirend bejárati ajtajának előtér-gazdagéphez
A biztonsági csoportházirend-objektum csatolása meglévő bejárati ajtajának előtér-gazdagép és a bejárati ajtajának tulajdonságainak frissítése. Először kérjen le a bejárati ajtajának objektum használatával [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) parancsot.
Következő lépésként állítsa az előtér- *WebApplicationFirewallPolicyLink* tulajdonságot a *resourceId* az előző lépésben használatával létrehozott "$ratePolicy" [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) a parancs. 

Az alábbi példában az erőforráscsoport nevét használja *myResourceGroupFD1* feltételezve, hogy létrehozta a bejárati ajtó profil szereplő utasítások segítségével a [a rövid útmutató: Hozzon létre egy bejárati ajtajának](quickstart-create-front-door.md) cikk. Emellett az alábbi példában cserélje le $frontDoorName a bejárati ajtajának profil nevét. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Csak be kell állítani *WebApplicationFirewallPolicyLink* biztonsági szabályzat csatolása egy előtér-bejárati ajtajának egyszer tulajdonság. További frissítései a rendszer automatikusan alkalmazza az előtér-a.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [bejárati ajtajának](front-door-overview.md) 



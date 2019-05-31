---
title: Webes alkalmazás tűzfalszabályt az IP-korlátozási szabály konfigurálása Azure bejárati ajtó
description: Megtudhatja, hogyan IP cím korlátozás WAF szabály meglévő bejárati ajtajának végpontok konfigurálása.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/21/2019
ms.author: kumud;tyao
ms.openlocfilehash: dae2bb8ece9ef56c0999e0f89abbf6f8d8e950e2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242928"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>Az IP-korlátozási szabály konfigurálása webalkalmazási tűzfallal rendelkező Azure bejárati ajtajának (előzetes verzió)
 Ez a cikk bemutatja, hogyan bejárati ajtajának Azure webalkalmazási tűzfal (WAF) az IP-korlátozási szabályok konfigurálása az Azure CLI, Azure PowerShell-lel vagy Azure Resource Manager-sablon használatával.

Az IP cím-alapú hozzáférés-vezérlési szabály egy egyéni WAF-szabályt, amely lehetővé teszi, hogy ki férhet hozzá a webalkalmazások Classless Inter-Domain Routing (CIDR) képernyőn IP-cím vagy IP-címtartományok listájának megadásával.

Alapértelmezés szerint a webalkalmazás az internetről érhető el. Ha szeretné korlátozni a hozzáférést a webes alkalmazásokhoz csak az ügyfelek számára egy listából ismert IP-címek vagy IP-címtartományok, hozzon létre két IP-megfeleltetési szabályoknak szeretné. Első IP-illeszkedő szabály egyező értékek, IP-címek listáját tartalmazza, és állítsa be a műveletet az "Engedélyezés". A másikat az alacsonyabb prioritásúak, ha más IP-címek blokkolása az "All" operátor használatával, és a "LETILTÁS" műveletet. Az IP-korlátozási szabály alkalmazása után minden olyan engedélyezési listán szereplő címek érkező kérések a 403-as (tiltott) választ kap.  

> [!IMPORTANT]
> A WAF IP-korlátozás szolgáltatás Azure bejárati ajtajának jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="configure-waf-policy-with-azure-cli"></a>WAF-szabályzat konfigurálása az Azure CLI-vel

### <a name="prerequisites"></a>Előfeltételek
Egy IP-korlátozási házirend konfigurálása előtt állítsa be a parancssori környezetet és bejárati ajtajának profil létrehozása.

#### <a name="set-up-azure-cli-environment"></a>Azure CLI-környezet beállítása
1. Telepítse a [Azure CLI-vel](/cli/azure/install-azure-cli), vagy az Azure Cloud Shellt használja. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. Az Azure CLI előre telepítve és konfigurálva a fiókjával való használat rendelkezik. Válassza ki a **kipróbálás** gombot a parancssori felület parancsai olvashat. Kiválasztásával **kipróbálás** hív meg, amely az Azure-fiókjával bejelentkezhet a Cloud Shellben. Miután a cloud shell a munkamenet akkor kezdődik, adja meg a `az extension add --name front-door` az előtér-ajtó bővítmény hozzáadása.
 2. Ha a parancssori felület helyileg használja a Bash, jelentkezzen be az Azure-bA `az login`.

#### <a name="create-front-door-profile"></a>Bejárati ajtajának profil létrehozása
Hozzon létre egy bejárati ajtajának profilt leírt utasítások alapján [a rövid útmutató: Bejárati ajtajának profil létrehozása](quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>A WAF-szabályzat létrehozása

A WAF-házirend létrehozása a [az hálózati waf-házirend létrehozása](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) parancsot. Az az alábbi példában cserélje le a szabályzat neve *IPAllowPolicyExampleCLI* egy egyedi házirend neve.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>Egyéni IP hozzáférés-vezérlési szabály hozzáadása

A WAF-házirendet az előző lépésben létrehozott egy egyéni IP hozzáférés-vezérlési szabály hozzáadása a [az egyéni-szabály hálózati waf-szabályzat létrehozása](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) parancsot. 

Az az alábbi példában:
-  cserélje le *IPAllowPolicyExampleCLI* a korábban létrehozott egyedi szabályzattal.
-  cserélje le *ip-cím-tartomány-1*, *ip-cím-tartomány-2* a saját tartománnyal.

Először hozza létre a IP-cím a megadott címek szabály engedélyezése.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Ezután hozzon létre egy letiltása az összes IP-szabály alacsonyabb prioritású, mint a korábbi IP-cím engedélyezése a szabály. Cserélje le a *IPAllowPolicyExampleCLI* a korábban létrehozott egyedi szabályzattal.

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
 ```

### <a name="find-waf-policy-id"></a>Keresse meg a WAF házirend-azonosító
Az azonosító a WAF-házirend található a [az hálózati waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) parancsot. Cserélje le a *IPAllowPolicyExampleCLI* a korábban létrehozott egyedi szabályzattal.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Hivatkozás WAF házirend bejárati ajtajának előtér-gazdagéphez

Állítsa be a bejárati ajtó *WebApplicationFirewallPolicyLink* a házirend-azonosító, az Azonosítót a [az hálózati előtér-ajtó frissítés](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) parancsot. Cserélje le a *IPAllowPolicyExampleCLI* a korábban létrehozott egyedi szabályzattal.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Ebben a példában a WAF-házirend vonatkozik FrontendEndpoints [0]. A kezelőfelületek valamelyik előfordulhat, hogy rendelje hozzá WAF szabályzatot.
> [!Note]
> Csak be kell állítani a **WebApplicationFirewallPolicyLink** WAF szabályzat csatolása egy előtér-bejárati ajtajának egyszer tulajdonság. További frissítései a rendszer automatikusan alkalmazza az előtér-a.

## <a name="configure-waf-policy-with-azure-powershell"></a>WAF-szabályzat konfigurálása az Azure PowerShell használatával

### <a name="prerequisites"></a>Előfeltételek
Egy IP-korlátozási házirend konfigurálása előtt állítsa be a PowerShell környezetet, és a bejárati ajtajának profil létrehozása.

#### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagok készletét kínálja, amelyek az [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez. 

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az utasításokat az oldalon, jelentkezzen be Azure hitelesítő adataival, és Az PowerShell-modul telepítéséhez.

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Az Azure-bA összekapcsolása a bejelentkezéshez egy interaktív párbeszédpanel
```
Connect-AzAccount

```
Bejárati ajtajának modul telepítése, előtt győződjön meg arról, hogy telepítve van a PowerShellGet jelenlegi verziója. Futtassa az alábbi parancsot, és nyissa meg újra PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>Az.FrontDoor modul telepítése 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Bejárati ajtajának profil létrehozása
Hozzon létre egy bejárati ajtajának profilt leírt utasítások alapján [a rövid útmutató: Bejárati ajtajának profil létrehozása](quickstart-create-front-door.md)

### <a name="define-ip-match-condition"></a>Adja meg az IP-egyezési feltételei
Használja a [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) parancs egy IP-egyeztetési feltételt meghatározásához. Az az alábbi példában cserélje le a *ip-cím-tartomány-1*, *ip-cím-tartomány-2* a saját tartománnyal.

```powershell
  $IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Hozzon létre egy IP-egyezés a feltétel az összes szabály
```powershell
  $IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty Any
    
```

### <a name="create-a-custom-ip-allow-rule"></a>Hozzon létre egyéni IP szabály engedélyezése
   Használja a [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) parancsot határozza meg a műveletet, és a prioritás értéke. A következő példában a kérelmeket az ügyfél IP-címek, amelyek megfelelnek a lista engedélyezett lesz. 

```powershell
  $IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
    -Name "IPAllowRule" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchCondition `
    -Action Allow -Priority 1
```
Hozzon létre egy letiltása az összes IP-szabály alacsonyabb prioritású, mint a korábbi IP-cím engedélyezése a szabály.

```powershell
  $IPBlockAll = New-AzFrontDoorWafCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>WAF-házirend konfigurálása
Keresse meg az erőforráscsoport, amely tartalmazza a bejárati ajtajának profil használatával `Get-AzResourceGroup`. Ezután konfigurálja az IP letiltása a szabály használata a WAF-házirend [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Hivatkozás WAF házirend bejárati ajtajának előtér-gazdagéphez

A WAF-objektum csatolása meglévő bejárati ajtajának előtér-gazdagép és a bejárati ajtajának tulajdonságainak frissítése. Először kérjen le a bejárati ajtajának objektum használatával [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Következő lépésként állítsa az előtér- *WebApplicationFirewallPolicyLink* tulajdonságot az erőforrás-azonosító, a *$IPAllowPolicyExamplePS* az előző lépésben létrehozott a [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) parancsot.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Ebben a példában a WAF-házirend vonatkozik FrontendEndpoints [0]. A kezelőfelületek valamelyik előfordulhat, hogy rendelje hozzá WAF szabályzatot. Csak be kell állítani *WebApplicationFirewallPolicyLink* WAF szabályzat csatolása egy előtér-bejárati ajtajának egyszer tulajdonság. További frissítései a rendszer automatikusan alkalmazza az előtér-a.


## <a name="configure-waf-policy-with-resource-manager-template"></a>WAF-házirend konfigurálása a Resource Manager-sablonnal
A sablon, amely egy bejárati ajtajának és a egy WAF szabályzatot hoz létre egyéni IP-korlátozási szabályok megtekintése [Itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [bejárati ajtajának profil létrehozása](quickstart-create-front-door.md).

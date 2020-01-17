---
title: IP-korlátozási WAF szabály konfigurálása az Azure bejárati ajtó szolgáltatásához
description: Megtudhatja, hogyan konfigurálhat egy webalkalmazási tűzfalszabály az IP-címek korlátozására egy meglévő Azure bejárati ajtó szolgáltatási végpontján.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 6b5793408545c2a61a30b5d89bc41d35460ed3eb
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76119465"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>IP-korlátozási szabály konfigurálása a webalkalmazási tűzfallal az Azure bejárati ajtó szolgáltatáshoz
Ez a cikk bemutatja, hogyan konfigurálhat IP-korlátozási szabályokat egy webalkalmazási tűzfalban (WAF) az Azure bejárati ajtó szolgáltatásához az Azure CLI, Azure PowerShell vagy egy Azure Resource Manager sablon használatával.

Az IP-cím alapú hozzáférés-vezérlési szabály egy egyéni WAF-szabály, amely lehetővé teszi a webalkalmazásokhoz való hozzáférés szabályozását. Ezt úgy végezheti el, hogy az IP-címek listáját vagy az IP-címtartományok osztály nélküli Inter-domain Routing (CIDR) formátumban van megadva.

Alapértelmezés szerint a webalkalmazás elérhető az internetről. Ha szeretné korlátozni az ügyfelek hozzáférését az ismert IP-címek vagy IP-címtartományok listájáról, létrehozhat egy olyan IP-megfeleltetési szabályt, amely az IP-címek listáját tartalmazza egyező értékként, és beállítja az operátort a "not" (tagadás igaz) és a **blokkolt**művelet számára. Az IP-korlátozási szabály alkalmazása után az ezen az engedélyezett listán kívüli címekről származó kérelmek 403-es tiltott választ kapnak.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>WAF szabályzat konfigurálása az Azure CLI-vel

### <a name="prerequisites"></a>Előfeltételek
Mielőtt megkezdené az IP-korlátozási szabályzat konfigurálását, állítsa be a CLI-környezetet, és hozzon létre egy Azure-beli bejárati szolgáltatás-profilt.

#### <a name="set-up-the-azure-cli-environment"></a>Az Azure CLI-környezet beállítása
1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli)-t, vagy használja a Azure Cloud shell. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Válassza ki az alábbi CLI-parancsok **kipróbálása** gombot, majd jelentkezzen be az Azure-fiókjába a megnyíló Cloud Shell-munkamenetben. A munkamenet elindítása után adja meg az `az extension add --name front-door` az Azure bejárati szolgáltatás bővítményének hozzáadásához.
 2. Ha a parancssori felületet a Bashben helyileg használja, jelentkezzen be az Azure-ba `az login`használatával.

#### <a name="create-an-azure-front-door-service-profile"></a>Azure-beli előtérben lévő szolgáltatás profiljának létrehozása
Hozzon létre egy Azure-beli bejárati szolgáltatás profilját a rövid útmutató [: bevezető ajtó létrehozása a globálisan elérhető globális webalkalmazásokhoz](../../frontdoor/quickstart-create-front-door.md)című témakörben leírtak szerint.

### <a name="create-a-waf-policy"></a>WAF szabályzat létrehozása

Hozzon létre egy WAF szabályzatot az az [Network elsőfékes-Door WAF-Policy Create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) parancs használatával. Az alábbi példában cserélje le a szabályzat neve *IPAllowPolicyExampleCLI* egyedi házirend-névre.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Egyéni IP-hozzáférés-vezérlési szabály hozzáadása

Az az [Network elülső WAF-Policy Custom-Rule Create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) paranccsal adhat hozzá egyéni IP-hozzáférés-vezérlési szabályt az IMÉNT létrehozott WAF-szabályzathoz.

Az alábbi példákban:
-  Cserélje le a *IPAllowPolicyExampleCLI* -t a korábban létrehozott egyedi szabályzatra.
-  Cserélje le az *IP-cím-Range-1*, *IP-cím-Range-2* tartományt a saját tartományára.

Először hozzon létre egy IP-engedélyezési szabályt az előző lépésben létrehozott házirendhez. Megjegyzés **– a késleltetés** megadása kötelező, mert egy szabálynak meg kell egyeznie a következő lépésben felvenni kívánt egyeztetési feltétellel.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Ezután adja hozzá az egyeztetési feltételt a szabályhoz:

```azurecli
az network front-door waf-policy rule match-condition add\
--match-variable RemoteAddr \
--operator IPMatch
--values "ip-address-range-1" "ip-address-range-2"
--negate true\
--name IPAllowListRule\
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>WAF szabályzat AZONOSÍTÓjának megkeresése 
A WAF szabályzat AZONOSÍTÓjának megkereséséhez használja az az [Network elsőfékes-Door WAF-Policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) parancsot. Cserélje le a *IPAllowPolicyExampleCLI* a következő példában a korábban létrehozott egyedi szabályzatra.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>WAF-szabályzat összekapcsolása egy Azure bejárati ajtó szolgáltatás előtér-gazdagépével

Állítsa be az Azure bejárati ajtó szolgáltatás *WebApplicationFirewallPolicyLink* azonosítóját a házirend-azonosítóra az az [Network elsőfékes-Door Update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) paranccsal. Cserélje le a *IPAllowPolicyExampleCLI* -t a korábban létrehozott egyedi szabályzatra.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Ebben a példában a WAF szabályzat a **FrontendEndpoints [0]** értékre van alkalmazva. A WAF-szabályzatot bármelyik előtér-végponthoz csatolhatja.
> [!Note]
> A **WebApplicationFirewallPolicyLink** tulajdonságot csak egyszer kell beállítania ahhoz, hogy egy WAF-házirendet egy Azure-beli előtér-szolgáltatás előtéréhez lehessen kapcsolni. A következő házirend-frissítéseket a rendszer automatikusan alkalmazza az előtérben.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>WAF szabályzat konfigurálása Azure PowerShell

### <a name="prerequisites"></a>Előfeltételek
Mielőtt megkezdené az IP-korlátozási szabályzat konfigurálását, állítsa be a PowerShell-környezetet, és hozzon létre egy Azure-beli bejárati szolgáltatási profilt.

#### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Azure PowerShell olyan parancsmagokat biztosít, amelyek a [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellt használják az Azure-erőforrások kezeléséhez.

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az oldalon megjelenő utasításokat, és jelentkezzen be a PowerShellbe az Azure-beli hitelesítő adataival, majd telepítse az az modult.

1. Kapcsolódjon az Azure-hoz az alábbi paranccsal, majd jelentkezzen be egy interaktív párbeszédablak használatával.
    ```
    Connect-AzAccount
    ```
 2. Mielőtt telepítené az Azure bejárati szolgáltatásának modulját, győződjön meg arról, hogy a PowerShellGet modul aktuális verziója van telepítve. Futtassa a következő parancsot, majd nyissa meg újra a PowerShellt.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Telepítse az az. FrontDoor modult az alábbi parancs használatával. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Azure-beli előtérben lévő szolgáltatás profiljának létrehozása
Hozzon létre egy Azure-beli bejárati szolgáltatás profilját a rövid útmutató [: bevezető ajtó létrehozása a globálisan elérhető globális webalkalmazásokhoz](../../frontdoor/quickstart-create-front-door.md)című témakörben leírtak szerint.

### <a name="define-an-ip-match-condition"></a>IP-egyeztetési feltétel definiálása
A [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) parancs használatával határozza meg az IP-egyeztetés feltételeit.
Az alábbi példában cserélje le az *IP-cím-tartomány-1*, *IP-cím-tartomány-2* értékét a saját tartományára.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Egyéni IP-engedélyezési szabály létrehozása

A [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) parancs használatával Definiáljon egy műveletet, és állítsa be a prioritást. A következő példában a listán nem szereplő ügyfelek IP-címeitől érkező kérések le lesznek tiltva.

```powershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>WAF házirend konfigurálása
A `Get-AzResourceGroup`használatával keresse meg az Azure bejárati ajtót szolgáltató profilt tartalmazó erőforráscsoport nevét. Ezután konfigurálja a WAF szabályzatot az IP-szabállyal a [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)használatával.

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>WAF-szabályzat összekapcsolása egy Azure bejárati ajtó szolgáltatás előtér-gazdagépével

WAF házirend-objektum csatolása meglévő előtér-gazdagéphez és az Azure előtér-szolgáltatás tulajdonságainak frissítése. Először a [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)használatával kérje le az Azure bejárati ajtó szolgáltatás objektumát. Ezután állítsa a **WebApplicationFirewallPolicyLink** tulajdonságot az előző lépésben létrehozott *$IPALLOWPOLICYEXAMPLEPS*erőforrás-azonosítóra a [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) parancs használatával.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Ebben a példában a WAF szabályzat a **FrontendEndpoints [0]** értékre van alkalmazva. WAF-szabályzatot bármelyik előtér-végponthoz csatolhat. A **WebApplicationFirewallPolicyLink** tulajdonságot csak egyszer kell beállítania ahhoz, hogy egy WAF-házirendet egy Azure-beli előtér-szolgáltatás előtéréhez lehessen kapcsolni. A következő házirend-frissítéseket a rendszer automatikusan alkalmazza az előtérben.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>WAF házirend konfigurálása Resource Manager-sablonnal
Ha meg szeretné tekinteni azt a sablont, amely létrehoz egy Azure bejárati ajtót, valamint egy WAF szabályzatot egyéni IP-korlátozási szabályokkal, lépjen a [githubra](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [hozhat létre Azure-beli bejárati profilt](../../frontdoor/quickstart-create-front-door.md).

---
title: Az IP-korlátozási szabály egy webes alkalmazás tűzfalszabályt az Azure bejárati ajtajának szolgáltatás konfigurálása
description: Ismerje meg, hogy egy webes alkalmazás egy meglévő Azure bejárati ajtajának szolgáltatási végpont IP-címek korlátozásához tűzfalszabály konfigurálása.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 73ef16aeb9a6014e98c0d40314bc174c6b5bf307
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66808350"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Az IP-korlátozási szabály egy webalkalmazási tűzfallal rendelkező Azure bejárati ajtajának szolgáltatás konfigurálása
Ez a cikk bemutatja, hogyan konfigurálása IP-korlátozási szabályok a webalkalmazási tűzfal (WAF) Azure bejárati ajtajának szolgáltatás az Azure CLI, Azure PowerShell vagy az Azure Resource Manager-sablon használatával.

Az IP-cím-alapú hozzáférés-vezérlési szabály egy egyéni WAF-szabályt, amely lehetővé teszi a webes alkalmazásokhoz való hozzáférés szabályozása. Ezt nem Classless Inter-Domain Routing (CIDR) formátumú IP-cím vagy IP-címtartományok listájának megadásával.

Alapértelmezés szerint a webalkalmazás az internetről érhető el. Ha azt szeretné, ismert IP-címek és IP-címtartományok listáját ügyfelek korlátozni, létre kell hoznia két IP-megfeleltetési szabályoknak. Az első illeszkedő IP-szabály IP-címek, a megfelelő értékek listáját tartalmazza, és beállítja a művelet **engedélyezése**. A másikat az alacsonyabb prioritásúak letiltja a más IP-címek segítségével a **összes** operátor és a művelet állítja **blokk**. Egy IP-korlátozási szabály alkalmazásakor, az engedélyezési listán szereplő címek kérelmekkel kapnak a 403 Tiltott válasz.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>A WAF-szabályzat konfigurálása az Azure CLI-vel

### <a name="prerequisites"></a>Előfeltételek
Egy IP-korlátozási házirend konfigurálása előtt beállítása a parancssori felület környezetében, és hozzon létre egy Azure bejárati ajtajának Service profilt.

#### <a name="set-up-the-azure-cli-environment"></a>Az Azure CLI-környezet beállítása
1. Telepítse a [Azure CLI-vel](/cli/azure/install-azure-cli), vagy használja az Azure Cloud Shellben. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Válassza ki a **kipróbálás** a CLI-parancsok, amely kövesse, majd jelentkezzen be az Azure-fiókkal, amely megnyitja a Cloud Shell-munkamenetet a gombot. A munkamenet indítása után adja meg a `az extension add --name front-door` hozzáadása az Azure bejárati ajtajának bővítmény.
 2. Ha a parancssori felület helyi használ a Bash, jelentkezzen be az Azure-bA `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Az Azure bejárati ajtajának Service profil létrehozása
Az Azure bejárati ajtajának Service profil létrehozása leírt utasítások alapján [a rövid útmutató: Hozzon létre magas rendelkezésre állású globális webalkalmazás számára a bejárati ajtajának](quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>A WAF-szabályzat létrehozása

A WAF szabályzat létrehozása a [az hálózati waf-házirend létrehozása](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) parancsot. A példában, hogy követi, cserélje le a szabályzat neve *IPAllowPolicyExampleCLI* egy egyedi házirend neve.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Adjon hozzá egy egyéni IP hozzáférés-vezérlési szabály

Használja a [az egyéni-szabály hálózati waf-szabályzat létrehozása](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) parancs használatával adja hozzá egy egyéni IP hozzáférés-vezérlési szabály a WAF-házirendet az imént létrehozott.

A következő példákban:
-  cserélje le *IPAllowPolicyExampleCLI* a korábban létrehozott egyedi szabályzattal.
-  cserélje le *ip-cím-tartomány-1*, *ip-cím-tartomány-2* a saját tartománnyal.

Először hozza létre a IP-cím a megadott címek szabály engedélyezése.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ("<ip-address-range-1>","<ip-address-range-2>") \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Ezután hozzon létre egy **letiltása az összes** a korábbi, mint az alacsonyabb prioritású szabály **engedélyezése** szabály. Ezúttal is helyettesítse be *IPAllowPolicyExampleCLI* a következő példa a korábban létrehozott egyedi házirendjével.

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
    
### <a name="find-the-id-of-a-waf-policy"></a>Keresse meg a WAF-házirend azonosítója 
Keresse meg a WAF-házirend-azonosító használatával a [az hálózati waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) parancsot. Cserélje le *IPAllowPolicyExampleCLI* a következő példa a korábban létrehozott egyedi házirendjével.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>A WAF-szabályzat csatolása egy Azure bejárati ajtajának Service előtér-gazdagép

Állítsa be az Azure bejárati ajtajának Service *WebApplicationFirewallPolicyLink* Azonosítóját a házirend-azonosító használatával a [az hálózati előtér-ajtó frissítés](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) parancsot. Cserélje le *IPAllowPolicyExampleCLI* az egyedi szabályzat, amelyet korábban hozott létre.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Ebben a példában a WAF-házirend vonatkozik **FrontendEndpoints [0]** . Kapcsolat bármilyen az előtérrendszerek a WAF-házirendet.
> [!Note]
> Be kell állítania a **WebApplicationFirewallPolicyLink** tulajdonság csak egyszer WAF házirend összekapcsolása egy Azure bejárati ajtajának előtér. További frissítései a rendszer automatikusan alkalmazza a előtérrendszerhez.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>A WAF-szabályzat konfigurálása az Azure PowerShell használatával

### <a name="prerequisites"></a>Előfeltételek
Egy IP-korlátozási házirend konfigurálása előtt a PowerShell-környezet beállítása, és hozzon létre egy Azure bejárati ajtajának Service profilt.

#### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell számos új parancsmagot is, melyek a [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modell az Azure-erőforrások kezeléséhez.

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az utasításokat a weblapon, jelentkezzen be a PowerShell használatával Azure hitelesítő adatait, és telepítse a a Az modul.

1. Csatlakozás az Azure-bA a következő parancs használatával, és a egy interaktív párbeszédpanel használatával jelentkezzen be.
    ```
    Connect-AzAccount
    ```
 2. Mielőtt telepít egy Azure bejárati ajtajának Szolgáltatásmodul, ellenőrizze a PowerShellGet modul telepített aktuális verziót használja-e. Futtassa a következő parancsot, majd nyissa meg a PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. A Az.FrontDoor modul telepítése a következő parancs segítségével. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Az Azure bejárati ajtajának Service profil létrehozása
Az Azure bejárati ajtajának Service profil létrehozása leírt utasítások alapján [a rövid útmutató: Hozzon létre magas rendelkezésre állású globális webalkalmazás számára a bejárati ajtajának](quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Adja meg egy IP-egyezési feltételei
Használja a [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) parancs egy IP-egyeztetési feltételt meghatározásához.
A következő példában cserélje le a *ip-cím-tartomány-1*, *ip-cím-tartomány-2* a saját tartománnyal.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
```
Hozzon létre egy IP-cím *összes feltételnek megfelelő* szabály a következő paranccsal:
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>Hozzon létre egyéni IP szabály engedélyezése

Használja a [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) parancsot határozza meg a műveletet, és a prioritás értéke. A következő példában a kérelmeket az ügyfél IP-címek, amelyek megfelelnek a lista engedélyezett lesz.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
Hozzon létre egy **letiltása az összes** a korábbi IP-cím, alacsonyabb prioritású szabály **engedélyezése** szabály.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>A WAF-házirend konfigurálása
Keresse meg az erőforráscsoport, amely tartalmazza az Azure bejárati ajtajának profil használatával `Get-AzResourceGroup`. Ezután konfigurálja az IP-Címmel rendelkező WAF házirend **letiltása az összes** szabály használatával [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>A WAF-szabályzat csatolása egy Azure bejárati ajtajának Service előtér-gazdagép

A WAF csoportházirend-objektum csatolása egy meglévő előtér-gazdagép és a frissítés Azure bejárati ajtajának szolgáltatás tulajdonságait. Először kérje le az Azure bejárati ajtajának Service objektumot használatával [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Következő lépésként állítsa a **WebApplicationFirewallPolicyLink** tulajdonságot az erőforrás-Azonosítójára *$IPAllowPolicyExamplePS*használatával az előző lépésben létrehozott, a [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)parancsot.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Ebben a példában a WAF-házirend vonatkozik **FrontendEndpoints [0]** . Kapcsolat bármilyen az előtérrendszerek a WAF-házirendet. Be kell állítania a **WebApplicationFirewallPolicyLink** tulajdonság csak egyszer WAF házirend összekapcsolása egy Azure bejárati ajtajának előtér. További frissítései a rendszer automatikusan alkalmazza a előtérrendszerhez.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>A WAF-házirend konfigurálása a Resource Manager-sablonnal
A sablon, amely egy Azure bejárati ajtajának Service szabályzatot, és a WAF-házirendet hoz létre egyéni IP-korlátozási szabályok megtekintéséhez lépjen a [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozzon létre egy Azure bejárati ajtajának Service profilt](quickstart-create-front-door.md).

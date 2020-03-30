---
title: IP-korlátozáswaf-szabály konfigurálása az Azure Bejárati ajtajához
description: Megtudhatja, hogyan konfigurálhat egy webalkalmazás-tűzfal szabályt egy meglévő Azure Bejárati ajtó végpont IP-címeinek korlátozására.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336074"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>IP-korlátozási szabály konfigurálása webalkalmazás-tűzfallal az Azure Bejárati ajtajához

Ez a cikk bemutatja, hogyan konfigurálhatja az IP-korlátozási szabályokat egy webalkalmazás-tűzfal (WAF) az Azure Bejárati ajtajához az Azure Portal, az Azure CLI, az Azure PowerShell vagy egy Azure Resource Manager-sablon használatával.

Az IP-címalapú hozzáférés-vezérlési szabály egy egyéni WAF-szabály, amely lehetővé teszi a webalkalmazásokhoz való hozzáférés szabályozását. Ezt úgy éri el, hogy megadja az IP-címek vagy IP-címtartományok listáját osztálynélküli tartományok közötti útválasztás (CIDR) formátumban.

A webalkalmazás alapértelmezés szerint elérhető az internetről. Ha az ismert IP-címek vagy IP-címtartományok listájából korlátozni szeretné az ügyfelek hez való hozzáférést, létrehozhat egy IP-egyezési szabályt, amely az IP-címek listáját egyező értékként tartalmazza, és az operátort "Nem" értékre állítja (a negate igaz), valamint a **Blokk**műveletet. Ip-korlátozási szabály alkalmazása után az engedélyezett listán kívüli címekről származó kérelmek 403 Tiltott választ kapnak.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>WAF-szabályzat konfigurálása az Azure Portalon

### <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy Azure Bejárati ajtó profilt a rövid útmutatóban leírt utasításokat [követve: Bejárati ajtó létrehozása egy magas rendelkezésre állású globális webalkalmazáshoz.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>WAF-házirend létrehozása

1. Az Azure portalon válassza az **Erőforrás létrehozása**lehetőséget, írja be a **webalkalmazás tűzfalát** a keresőmezőbe, majd válassza a **WebApplication Firewall (WAF) lehetőséget.**
2. Kattintson a **Létrehozás** gombra.
3. A **WaF-házirend létrehozása** lapon az alábbi értékek segítségével **hajtsa** végre az Alapok lapot:
   
   |Beállítás  |Érték  |
   |---------|---------|
   |Házirend     |Globális WAF (bejárati ajtó)|
   |Előfizetés     |Válassza ki előfizetését.|
   |Erőforráscsoport     |Válassza ki azt az erőforráscsoportot, ahol a bejárati ajtó található.|
   |Házirend neve     |Írja be a házirend nevét|
   |Házirend állapota     |Engedélyezve|

   **Tovább: Házirend-beállítások**

1. A **Házirend-beállítások** lapon válassza a **Megelőzés**lehetőséget. A **Blokk válasz törzs**, írja *be A már blokkolt!* így láthatja, hogy az egyéni szabály érvényben van.
2. Válassza a **Tovább: Felügyelt szabályok**lehetőséget.
3. Válassza a **Tovább: Egyéni szabályok**lehetőséget.
4. Válassza **az Egyéni szabály hozzáadása**lehetőséget.
5. Az **Egyéni szabály hozzáadása** lapon a következő tesztértékek segítségével hozzon létre egyéni szabályt:

   |Beállítás  |Érték  |
   |---------|---------|
   |Egyéni szabály neve     |FdWafCustSzabály|
   |status     |Engedélyezve|
   |Szabály típusa     |Match|
   |Prioritás    |100|
   |Egyezés típusa     |IP-cím|
   |Változó egyeztetése|TávoliAddr|
   |Művelet|Nem tartalmazza|
   |IP-cím vagy -tartomány|10.10.10.0/24|
   |Akkor|Forgalom megtagadása|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Egyéni szabály":::

   Válassza a **Hozzáadás** lehetőséget.
6. Válassza a **Tovább: Társítás**lehetőséget.
7. Válassza **az Előtér-állomás hozzáadása**lehetőséget.
8. **Előtér-állomás**esetén jelölje ki az előtér-állomást, és válassza **a Hozzáadás lehetőséget.**
9. Válassza az **Áttekintés + létrehozás** lehetőséget.
10. A házirend-érvényesítés immár a dedikálás után válassza a **Létrehozás gombot.**

### <a name="test-your-waf-policy"></a>A WAF-szabályzat tesztelése

1. A WAF-házirend üzembe helyezése után keresse meg a bejárati ajtó előtér-állomásnevét.
2. Meg kell jelennie az egyéni blokk üzenetnek.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF-szabály tesztje":::

   > [!NOTE]
   > Egy privát IP-címet szándékosan használt az egyéni szabály, hogy garantálja a szabály aktiválódik. Egy tényleges központi telepítés, hozzon létre *engedélyezési* és *megtagadási* szabályok ip-címeket az adott helyzetben.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>WAF-szabályzat konfigurálása az Azure CLI-vel

### <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené konfigurálni az IP-korlátozási szabályzatot, állítsa be a CLI-környezetet, és hozzon létre egy Azure Bejárati ajtó profilt.

#### <a name="set-up-the-azure-cli-environment"></a>Az Azure CLI-környezet beállítása
1. Telepítse az [Azure CLI-t](/cli/azure/install-azure-cli), vagy használja az Azure Cloud Shellt. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Válassza a **Try it** gombot a következő CLI-parancsokban, majd jelentkezzen be az Azure-fiókjába a megnyíló Cloud Shell-munkamenetben. A munkamenet megkezdése `az extension add --name front-door` után adja meg az Azure Bejárati ajtó bővítmény hozzáadása.
 2. Ha a CLI-t helyileg használja a Bash-ben, jelentkezzen be az Azure-ba a használatával. `az login`

#### <a name="create-an-azure-front-door-profile"></a>Azure bejárati ajtó profil létrehozása
Hozzon létre egy Azure Bejárati ajtó profilt a rövid útmutatóban leírt utasításokat [követve: Bejárati ajtó létrehozása egy magas rendelkezésre állású globális webalkalmazáshoz.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>WAF-házirend létrehozása

Hozzon létre egy WAF-házirendet az [az hálózati bejárati waf-policy create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) paranccsal. Az alábbi példában cserélje le az *IPAllowPolicyExampleCLI* házirendnevet egy egyedi házirendnévre.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Egyéni IP-hozzáférés-vezérlési szabály hozzáadása

Az [az-hálózati bejárati waf-policy egyéni szabály létrehozási](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) parancs használatával egyéni IP-hozzáférés-vezérlési szabályt adhat hozzá az imént létrehozott WAF-házirendhez.

A következő példákban:
-  Cserélje le *az IPAllowPolicyExampleCLI-t* a korábban létrehozott egyedi házirendre.
-  Cserélje le *az ip-address-range-1*, *ip-address-range-2-t* a saját tartományára.

Először hozzon létre egy IP-engedélyezési szabályt az előző lépésből létrehozott házirendhez. 
> [!NOTE]
> **--halasztásra** van szükség, mert a szabálynak egyezési feltételsel kell rendelkeznie a következő lépésben.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Ezután adja hozzá az egyezési feltételt a szabályhoz:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>WAF-házirend azonosítójának megkeresése 
Keresse meg a WAF-házirend azonosítóját az [az hálózati bejárati waf-policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) parancs használatával. Cserélje le az *IPAllowPolicyExampleCLI-t* a következő példában a korábban létrehozott egyedi házirendre.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>WAF-szabályzat csatolása azure bejárati ajtó előtér-állomásához

Állítsa be az Azure Front Door *WebApplicationFirewallPolicyLink* id-t a házirend-azonosítóra az [az hálózati bejárati ajtó frissítési](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) parancs használatával. Cserélje le *az IPAllowPolicyExampleCLI-t* a korábban létrehozott egyedi házirendre.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
Ebben a példában a WAF-házirend et alkalmazza a **FrontendEndpoints[0]**. A WAF-házirendet bármelyik előtérhez csatolhatja.
> [!Note]
> A **WebApplicationFirewallPolicyLink** tulajdonságot csak egyszer kell beállítania ahhoz, hogy a WAF-szabályzatot egy Azure bejárati ajtó előtéréhez csatolja. A további házirend-frissítések automatikusan az előtérre lesznek alkalmazva.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>WAF-szabályzat konfigurálása az Azure PowerShell használatával

### <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené konfigurálni az IP-korlátozási szabályzatot, állítsa be a PowerShell-környezetet, és hozzon létre egy Azure Bejárati ajtó-profilt.

#### <a name="set-up-your-powershell-environment"></a>A PowerShell-környezet beállítása
Az Azure PowerShell olyan parancsmagkészletet biztosít, amely az [Azure Resource Manager-modellt](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) használja az Azure-erőforrások kezeléséhez.

Az [Azure PowerShellt](https://docs.microsoft.com/powershell/azure/overview) telepítheti a helyi számítógépen és bármely PowerShell-munkamenetben használhatja. Kövesse az utasításokat az oldalon a PowerShellbe való bejelentkezéshez az Azure-hitelesítő adatok használatával, majd telepítse az Az modult.

1. Csatlakozzon az Azure-hoz a következő paranccsal, majd jelentkezzen be egy interaktív párbeszédpanelen.
    ```
    Connect-AzAccount
    ```
 2. Az Azure Bejárati ajtajához modul telepítése előtt győződjön meg arról, hogy a PowerShellGet modul aktuális verziója telepítve van. Futtassa a következő parancsot, majd nyissa meg újra a PowerShellt.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Telepítse az Az.FrontDoor modult a következő paranccsal. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Azure bejárati ajtó profil létrehozása
Hozzon létre egy Azure Bejárati ajtó profilt a rövid útmutatóban leírt utasításokat [követve: Bejárati ajtó létrehozása egy magas rendelkezésre állású globális webalkalmazáshoz.](../../frontdoor/quickstart-create-front-door.md)

### <a name="define-an-ip-match-condition"></a>IP-egyezési feltétel definiálása
A [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) paranccsal ip-egyezési feltételt definiáljon.
A következő példában cserélje le *az ip-address-range-1*, *ip-address-range-2-t* a saját tartományára.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Egyéni IP-engedélyezési szabály létrehozása

A [New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) paranccsal definiáljon egy műveletet, és prioritást állítson be. A következő példában a lista-nak megfelelő ügyfélIP-ügyfelektől érkező kérelmek le lesznek tiltva.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>WAF-házirend konfigurálása
Keresse meg az Azure Bejárati ajtaját profilt `Get-AzResourceGroup`tartalmazó erőforráscsoport nevét a használatával. Ezután konfiguráljon egy WAF-házirendet az IP-szabállyal a [New-AzFrontDoorWafPolicy használatával.](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>WAF-szabályzat csatolása azure bejárati ajtó előtér-állomásához

Waf-szabályzatobjektum csatolása egy meglévő előtér-állomáshoz, és az Azure Front Door tulajdonságainak frissítése. Először olvassa be az Azure Bejárati ajtó objektumot a [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)használatával. Ezután állítsa a **WebApplicationFirewallPolicyLink** tulajdonságot az előző lépésben létrehozott *$IPAllowPolicyExamplePS*erőforrásazonosítójára a [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) paranccsal.

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> Ebben a példában a WAF-házirend et alkalmazza a **FrontendEndpoints[0]**. A WAF-házirendet bármelyik előtérhez csatolhatja. A **WebApplicationFirewallPolicyLink** tulajdonságot csak egyszer kell beállítania ahhoz, hogy a WAF-szabályzatot egy Azure bejárati ajtó előtéréhez csatolja. A további házirend-frissítések automatikusan az előtérre lesznek alkalmazva.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>WAF-házirend konfigurálása Erőforrás-kezelő sablonnal
Az Azure Bejárati ajtó szabályzatot és az egyéni IP-korlátozási szabályokkal rendelkező WAF-szabályzatot létrehozó sablon megtekintéséhez nyissa meg a [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip)című területet.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozhat létre Azure Bejárati ajtó profilt.](../../frontdoor/quickstart-create-front-door.md)

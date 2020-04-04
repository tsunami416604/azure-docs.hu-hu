---
title: 'Rövid útmutató: Hozzon létre egy Azure WAF v2-t az Application Gateway- Erőforrás-kezelő sablonon'
titleSuffix: Azure Application Gateway
description: Megtudhatja, hogyan hozhat létre egy Erőforrás-kezelő sablont webalkalmazás-tűzfal 2-es megoldáshoz az Azure Application Gateway-en.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 04/02/2020
ms.author: victorh
ms.openlocfilehash: 2a13d4ef440a75045c72f97db02d4be6f2e2b134
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656383"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway---resource-manager-template"></a>Rövid útmutató: Hozzon létre egy Azure WAF v2-t az Application Gateway- Erőforrás-kezelő sablonon

Ebben a rövid útmutatóban egy Erőforrás-kezelő sablon használatával hozzon létre egy Azure Web Application Firewall v2-t az Application Gateway-en.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-web-application-firewall"></a>Webalkalmazás-tűzfal létrehozása

Ez a sablon egy egyszerű webalkalmazás-tűzfal v2-t hoz létre az Azure Application Gateway-en. Ez magában foglalja a nyilvános IP-előtér-ip-címet, a HTTP-beállításokat, a 80-as porton egy alapszintű figyelővel rendelkező szabályt és egy háttérkészletet. Egy egyéni szabállyal rendelkező WAF-házirend jön létre, amely blokkolja a háttérkészlet felé irányuló forgalmat egy IP-címegyezési típus alapján.

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-wafv2/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json" range="001-404" highlight="314-358":::

A sablonban több Azure-erőforrás van definiálva:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : az egyik az alkalmazásátjáróhoz, a másik pedig a virtuális gépekhez.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : két virtuális gép
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : kettő a virtuális gépekhez
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : az IIS és a weblapok konfigurálása

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Erőforrás-kezelő sablon telepítése az Azure-ba:

1. Válassza **a Telepítés az Azure-ba** lehetőséget, ha be szeretne jelentkezni az Azure-ba, és meg szeretné nyitni a sablont. A sablon létrehoz egy alkalmazásátjárót, a hálózati infrastruktúrát és két virtuális gépet az IIS-t futtató háttérkészletben.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json"><img src="../media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Jelölje ki vagy hozza létre az erőforráscsoportot.
3. Válassza az **Elfogadom a fenti feltételeket,** majd válassza a **Vásárlás**lehetőséget. A központi telepítés 10 percet vagy hosszabb időt is igénybe vehet.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Bár az IIS nem szükséges az alkalmazásátjáró létrehozásához, a háttérkiszolgálókon telepítve van, hogy ellenőrizze, hogy az Azure sikeresen létrehozott-e egy WAF v2-t az alkalmazásátjárón. 

Az alkalmazásátjáró teszteléséhez használja az IIS-t:

1. Keresse meg az alkalmazásátjáró nyilvános IP-címét az **Áttekintés** lapon. ![Rekord alkalmazás átjáró](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) nyilvános IP-cím Vagy, akkor válassza ki **az Összes erőforrást**, írja *myAGPublicIPAddress* a keresőmezőbe, majd jelölje ki azt a keresési eredmények között. Az Azure megjeleníti a nyilvános IP-címet az **áttekintés** oldalon.
2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába az adott IP-cím böngészéséhez.
3. Ellenőrizze a választ. A **403-as tiltott** válasz ellenőrzi, hogy a WAF sikeresen létrejött-e, és blokkolja a háttérkészlethez való kapcsolatokat.
4. Módosítsa az egyéni szabályt a **Forgalom engedélyezése gombra.**
  Futtassa a következő Azure PowerShell-parancsfájlt, és cserélje le az erőforráscsoport nevét:
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   Frissítse a böngésző többször, és látnia kell kapcsolatokat mind myVM1 és myVM2.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az alkalmazásátjáróval létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja az alkalmazásátjárót és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez `Remove-AzResourceGroup` hívja meg a parancsmast:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Alkalmazásátjáró létrehozása webalkalmazás-tűzfallal az Azure Portal használatával](application-gateway-web-application-firewall-portal.md)
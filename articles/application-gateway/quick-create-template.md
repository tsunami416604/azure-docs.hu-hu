---
title: 'Rövid útmutató: Irányított webes forgalom Erőforrás-kezelő sablonnal'
titleSuffix: Azure Application Gateway
description: Megtudhatja, hogyan hozhat létre egy Azure Application Gateway-t egy Resource Manager-sablon használatával, amely a webes forgalmat egy háttérkészletben lévő virtuális gépekre irányítja.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/23/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0612371605f6b216eadeef49d9adb1497ba31591
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80135971"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---resource-manager-template"></a>Rövid útmutató: Közvetlen webes forgalom az Azure Application Gateway alkalmazással – Erőforrás-kezelő sablon

Ebben a rövid útmutatóban egy Resource Manager-sablon használatával hozzon létre egy Azure Application Gateway. Ezután tesztelje az alkalmazásátjárót, hogy megbizonyosodjon arról, hogy megfelelően működik.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ezt a rövid útmutatót az [Azure Portal](quick-create-portal.md), az [Azure PowerShell](quick-create-powershell.md)vagy az Azure CLI használatával is elvégezheti. [Azure CLI](quick-create-cli.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az egyszerűség kedvéért ez a sablon létrehoz egy egyszerű beállítást egy nyilvános előtér-IP-cím, egy alapvető figyelő egyetlen hely üzemeltetéséhez az alkalmazás átjárón, egy alapvető kérelem-útválasztási szabály, és két virtuális gép a háttérkészletben.

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json" range="001-343" highlight="197-297":::

A sablonban több Azure-erőforrás van definiálva:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : az egyik az alkalmazásátjáróhoz, a másik pedig a virtuális gépekhez.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : két virtuális gép
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : kettő a virtuális gépekhez
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : az IIS és a weblapok konfigurálása


### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Erőforrás-kezelő sablon telepítése az Azure-ba:

1. Válassza **a Telepítés az Azure-ba** lehetőséget, ha be szeretne jelentkezni az Azure-ba, és meg szeretné nyitni a sablont. A sablon létrehoz egy alkalmazásátjárót, a hálózati infrastruktúrát és két virtuális gépet az IIS-t futtató háttérkészletben.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Jelölje ki vagy hozza létre az erőforráscsoportot, írja be a virtuális gép rendszergazdájának felhasználónevét és jelszavát.
3. Válassza az **Elfogadom a fenti feltételeket,** majd válassza a **Vásárlás**lehetőséget. A központi telepítés 20 percet vagy hosszabb időt is igénybe vehet.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Bár az IIS nem szükséges az alkalmazásátjáró létrehozásához, telepítve van annak ellenőrzésére, hogy az Azure sikeresen létrehozta-e az alkalmazásátjárót. Az alkalmazásátjáró teszteléséhez használja az IIS-t:

1. Keresse meg az alkalmazásátjáró nyilvános IP-címét az **Áttekintés** lapon. ![Rekord alkalmazás átjáró](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) nyilvános IP-cím Vagy, akkor válassza ki **az Összes erőforrást**, írja *myAGPublicIPAddress* a keresőmezőbe, majd jelölje ki azt a keresési eredmények között. Az Azure megjeleníti a nyilvános IP-címet az **áttekintés** oldalon.
2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába az adott IP-cím böngészéséhez.
3. Ellenőrizze a választ. Egy érvényes válasz ellenőrzi, hogy az alkalmazásátjáró sikeresen létrejött-e, és sikeresen csatlakozhat-e a háttérrendszerhez.

   ![Az alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Frissítse a böngészőt többször, és látnia kell a myVM1 és a myVM2 kapcsolatokat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az alkalmazásátjáróval létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja az alkalmazásátjárót és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez `Remove-AzResourceGroup` hívja meg a parancsmast:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)

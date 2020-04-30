---
title: 'Gyors útmutató: webes forgalom irányítása Resource Manager-sablonnal'
titleSuffix: Azure Application Gateway
description: Ismerje meg, hogyan hozhat létre Resource Manager-sablonokat olyan Azure-Application Gateway létrehozásához, amely egy háttér-készletben lévő virtuális gépekre irányítja a webes forgalmat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/23/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: cce3ef20a93c6d7a24bfa312501d2f8cc8ed9273
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81604897"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---resource-manager-template"></a>Gyors útmutató: webes forgalom közvetlen továbbítása az Azure Application Gateway – Resource Manager-sablonnal

Ebben a rövid útmutatóban egy Resource Manager-sablont használ Azure-Application Gateway létrehozásához. Ezt követően tesztelje az Application Gatewayt, hogy megfelelően működjön.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ezt a rövid útmutatót a [Azure Portal](quick-create-portal.md), a [Azure PowerShell](quick-create-powershell.md)vagy az [Azure CLI](quick-create-cli.md)használatával is elvégezheti.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Az egyszerűség kedvéért ez a sablon egy egyszerű telepítőt hoz létre egy nyilvános előtér-IP-címmel, egy alapszintű figyelőt, amely egyetlen helyet üzemeltet az Application gatewayben, egy alapszintű kérelem-útválasztási szabályt és két virtuális gépet a háttér-készletben.

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-qs/azuredeploy.json) származik.

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json" range="001-343" highlight="197-297":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses) : egy az Application Gateway számára, és kettő a virtuális gépek számára.
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. számítási/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : két virtuális gép
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : kettő a virtuális gépekhez
- [**Microsoft. számítás/virtualMachine/bővítmények**](/azure/templates/microsoft.compute/virtualmachines/extensions) : az IIS és a weblapok konfigurálásához


### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Resource Manager-sablon üzembe helyezése az Azure-ban:

1. Válassza az **üzembe helyezés az Azure** -ban lehetőséget az Azure-ba való bejelentkezéshez és a sablon megnyitásához. A sablon létrehoz egy Application Gateway-t, a hálózati infrastruktúrát és két virtuális gépet az IIS-t futtató háttér-készletben.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json)

2. Válassza ki vagy hozza létre az erőforráscsoportot, írja be a virtuális gép rendszergazdájának felhasználónevét és jelszavát.
3. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket** , majd válassza a **vásárlás**lehetőséget. Az üzembe helyezés akár 20 percet vagy hosszabb időt is igénybe vehet.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Annak ellenére, hogy az IIS nem szükséges az Application Gateway létrehozásához, az telepítve van annak ellenőrzéséhez, hogy az Azure sikeresen létrehozta-e az Application Gatewayt. Az IIS használata az Application Gateway teszteléséhez:

1. Keresse meg az Application Gateway nyilvános IP-címét az **Áttekintés** oldalon. ![Az Application Gateway nyilvános IP-](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) címének rögzítése vagy a **minden erőforrás**lehetőség kiválasztásával megadhatja a *myAGPublicIPAddress* kifejezést a keresőmezőbe, majd kiválaszthatja azt a keresési eredmények között. Az Azure megjeleníti a nyilvános IP-címet az **Áttekintés** oldalon.
2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába az IP-cím megkereséséhez.
3. Keresse meg a választ. Egy érvényes válasz ellenőrzi, hogy az Application Gateway sikeresen létrejött-e, és sikeresen tud-e kapcsolatot létesíteni a háttérrel.

   ![Az alkalmazásátjáró tesztelése](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Többször is frissítse a böngészőt, és látnia kell a myVM1 és a myVM2 kapcsolatait.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az Application Gateway használatával létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja az Application Gatewayt és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` következő parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes forgalom kezelése Application Gatewayjel az Azure CLI segítségével](./tutorial-manage-web-traffic-cli.md)

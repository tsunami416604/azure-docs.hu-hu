---
title: 'Rövid útmutató: Azure Firewall és IP-csoportok létrehozása – Resource Manager-sablon'
description: Megtudhatja, hogyan hozhat létre Azure Firewall és IP-csoportokat egy Resource Manager-sablon használatával.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 53e5bf4f770ce986af2f3572bd6c1ef4cd9e3c2b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81605247"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---resource-manager-template"></a>Rövid útmutató: Azure Firewall és IP-csoportok létrehozása – Resource Manager-sablon

Ebben a rövid útmutatóban egy Resource Manager-sablont használ egy olyan Azure Firewall üzembe helyezéséhez, amely egy hálózati szabályban és alkalmazási szabályban használt minta IP-csoportokkal rendelkezik.

Az IP-csoport egy legfelső szintű erőforrás, amely lehetővé teszi, hogy az IP-címeket, tartományokat és alhálózatokat egyetlen objektumba definiálja és csoportosítsa. Ez hasznos az IP-címek Azure Firewall szabályokban való kezeléséhez. Manuálisan is megadhatja az IP-címeket, vagy importálhatja őket egy fájlból.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-and-ip-groups"></a>Azure Firewall és IP-csoportok létrehozása

Ez a sablon létrehoz egy Azure Firewall és IP-csoportokat, valamint a szükséges erőforrásokat a Azure Firewall támogatásához.

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json) származik.

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json" range="001-512" highlight="118-141":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)


### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Resource Manager-sablon üzembe helyezése az Azure-ban:

1. Válassza az **üzembe helyezés az Azure** -ban lehetőséget az Azure-ba való bejelentkezéshez és a sablon megnyitásához. A sablon létrehoz egy Azure Firewall, a hálózati infrastruktúrát és két virtuális gépet.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. A portálon az **Azure Firewall létrehozása a IpGroups** lapon írja be vagy válassza ki a következő értékeket:
   - Előfizetés: válasszon a meglévő előfizetések közül 
   - Erőforráscsoport: válasszon a meglévő erőforráscsoportok közül, vagy válassza az **új létrehozása**lehetőséget, majd kattintson **az OK gombra**.
   - Hely: válasszon egy helyet
   - Virtual Network neve: írja be az új virtuális hálózat (VNet) nevét. 
   - 1. IP-csoport neve: írja be az 1. IP-csoport nevét 
   - IP-csoportnév 2: írja be a 2. IP-csoport nevét 
   - Rendszergazdai Felhasználónév: írja be a felhasználónevet a rendszergazda felhasználói fiókhoz. 
   - Hitelesítés: válassza a sshPublicKey vagy a jelszó lehetőséget 
   - Rendszergazdai jelszó: írja be a rendszergazda jelszavát vagy kulcsát

3. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket** , majd válassza a **vásárlás**lehetőséget. Az üzembe helyezés akár 10 percet is igénybe vehet.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A Azure Portal tekintse át az üzembe helyezett erőforrásokat, különösen az IP-csoportokat használó tűzfalszabályok.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="IP-csoportok.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Hálózati szabályok.":::

A sablonban található tűzfal JSON-szintaxisáról és tulajdonságairól további információt a [Microsoft. Network azureFirewalls-sablon referenciája](https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls)című témakörben talál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a tűzfallal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a tűzfalat és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` következő parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure Firewall üzembe helyezése és konfigurálása hibrid hálózaton a Azure Portal használatával](tutorial-hybrid-portal.md)
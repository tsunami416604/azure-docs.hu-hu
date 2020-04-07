---
title: 'Rövid útmutató: Azure tűzfal és IP-csoportok létrehozása – Erőforrás-kezelő sablon'
description: Megtudhatja, hogyan hozhat létre egy Erőforrás-kezelő sablont az Azure tűzfal és az IP-csoportok létrehozásához.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/06/2020
ms.author: victorh
ms.openlocfilehash: 767e91af7fb5ff3de517a79ac4df55af5057c76f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679834"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---resource-manager-template"></a>Rövid útmutató: Azure tűzfal és IP-csoportok létrehozása – Erőforrás-kezelő sablon

Ebben a rövid útmutatóban egy Resource Manager-sablon használatával telepítheti az Azure tűzfalat a hálózati szabályban és alkalmazásszabályban használt minta IP-csoportokkal.

Az IP-csoport egy legfelső szintű erőforrás, amely lehetővé teszi az IP-címek, tartományok és alhálózatok egyetlen objektumba történő definiálását és csoportosítását. Ez az Azure Firewall-szabályok IP-címeinek kezeléséhez hasznos. Manuálisan is beírhatja az IP-címeket, vagy importálhatja őket egy fájlból.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-and-ip-groups"></a>Azure-tűzfal és IP-csoportok létrehozása

Ez a sablon létrehoz egy Azure tűzfal és IP-csoportok, valamint az Azure tűzfal támogatásához szükséges erőforrásokat.

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json" range="001-512" highlight="118-141":::

A sablonban több Azure-erőforrás van definiálva:

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)


### <a name="deploy-the-template"></a>A sablon üzembe helyezése

Erőforrás-kezelő sablon telepítése az Azure-ba:

1. Válassza **a Telepítés az Azure-ba** lehetőséget, ha be szeretne jelentkezni az Azure-ba, és meg szeretné nyitni a sablont. A sablon létrehoz egy Azure tűzfalat, a hálózati infrastruktúrát és két virtuális gépet.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json"><img src="./media/quick-create-ipgroup-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. A portálon az **Azure-tűzfal létrehozása Ip-csoportokkal** lapon írja be vagy válassza ki a következő értékeket:
   - Előfizetés: Válasszon a meglévő előfizetések közül 
   - Erőforráscsoport: Válasszon a meglévő erőforráscsoportok közül, vagy válassza **az Új létrehozása**lehetőséget, és válassza az **OK**gombot.
   - Hely: Hely kiválasztása
   - Virtuális hálózat neve: Írja be az új virtuális hálózat (VNet) nevét. 
   - 1. IP-csoport neve: Az 1. 
   - IP-csoport neve 2: Típusnév az IP-csoport 2 
   - Rendszergazdai felhasználónév: Írja be a rendszergazdai felhasználói fiók felhasználónevét 
   - Hitelesítés: SshPublicKey vagy jelszó kiválasztása 
   - Rendszergazdai jelszó: Írjon be rendszergazdai jelszót vagy kulcsot

3. Válassza az **Elfogadom a fenti feltételeket,** majd válassza a **Vásárlás**lehetőséget. A központi telepítés 10 percet vagy hosszabb időt is igénybe vehet.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Az Azure Portalon tekintse át a telepített erőforrásokat, különösen az IP-csoportokat használó tűzfalszabályokat.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="IP-csoportok.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Hálózati szabályok.":::

A sablonokban lévő tűzfal JSON-szintaxisáról és tulajdonságairól a [Microsoft.Network azureFirewalls sablonhivatkozás című témakörben olvashat.](https://docs.microsoft.com/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a tűzfallal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a tűzfalat és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez `Remove-AzResourceGroup` hívja meg a parancsmast:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Tűzfal telepítése és konfigurálása hibrid hálózatban az Azure Portal használatával](tutorial-hybrid-portal.md)
---
title: 'Gyors útmutató: Azure Firewall létrehozása több nyilvános IP-címmel – Resource Manager-sablon'
description: Megtudhatja, hogyan használhat Resource Manager-sablonokat több nyilvános IP-címmel rendelkező Azure Firewall létrehozásához.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: victorh
ms.openlocfilehash: 3d58173d239e7a9249b588ff038ea46cfedb27a3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81605203"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Gyors útmutató: Azure Firewall létrehozása több nyilvános IP-címmel – Resource Manager-sablon

Ebben a rövid útmutatóban egy Resource Manager-sablonnal telepít egy Azure Firewall több nyilvános IP-címmel.

Az üzembe helyezett tűzfal NAT-szabályok gyűjtési szabályaival rendelkezik, amelyek engedélyezik az RDP-kapcsolatokat két Windows Server 2019 rendszerű virtuális géphez.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

További információ a több nyilvános IP-címmel rendelkező Azure Firewallről: [Azure Firewall üzembe helyezése több nyilvános IP-címmel Azure PowerShell használatával](deploy-multi-public-ip-powershell.md).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall"></a>Azure Firewall létrehozása

Ez a sablon két nyilvános IP-címmel rendelkező Azure Firewall hoz létre, valamint a szükséges erőforrásokat a Azure Firewall támogatásához.

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://github.com/Azure/azure-quickstart-templates/blob/master/fw-docs-qs/azuredeploy.json) származik.

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

Több Azure-erőforrás van definiálva a sablonban:

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

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json)

2. A portálon, az **Azure Firewall létrehozása több IP-cím nyilvános címmel** lapon írja be vagy válassza ki a következő értékeket:
   - Előfizetés: válasszon a meglévő előfizetések közül 
   - Erőforráscsoport: válasszon a meglévő erőforráscsoportok közül, vagy válassza az **új létrehozása**lehetőséget, majd kattintson **az OK gombra**.
   - Hely: válasszon egy helyet
   - Rendszergazdai Felhasználónév: írja be a felhasználónevet a rendszergazda felhasználói fiókhoz. 
   - Rendszergazdai jelszó: írja be a rendszergazda jelszavát vagy kulcsát

3. Jelölje be **az Elfogadom a fenti feltételeket és kikötéseket** , majd válassza a **vásárlás**lehetőséget. Az üzembe helyezés akár 10 percet is igénybe vehet.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A Azure Portal tekintse át az üzembe helyezett erőforrásokat. Jegyezze fel a tűzfal nyilvános IP-címeit.  

Távoli asztali kapcsolat használata a tűzfal nyilvános IP-címeihez való kapcsolódáshoz. A sikeres kapcsolatok olyan tűzfal NAT-szabályokat mutatnak be, amelyek engedélyezik a kapcsolódást a háttér-kiszolgálókhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a tűzfallal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a tűzfalat és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez hívja meg a `Remove-AzResourceGroup` következő parancsmagot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure Firewall üzembe helyezése és konfigurálása hibrid hálózaton a Azure Portal használatával](tutorial-hybrid-portal.md)
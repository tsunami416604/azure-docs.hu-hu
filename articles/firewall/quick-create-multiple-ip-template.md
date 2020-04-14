---
title: 'Rövid útmutató: Hozzon létre egy Azure-tűzfalat több nyilvános IP-címmel – Erőforrás-kezelő sablon'
description: Megtudhatja, hogyan hozhat létre egy Azure-tűzfalat több nyilvános IP-címmel.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/13/2020
ms.author: victorh
ms.openlocfilehash: b14ff2e94e9df25186a13ae8fbd63d8d4044b538
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274241"
---
# <a name="quickstart-create-an-azure-firewall-with-multiple-public-ip-addresses---resource-manager-template"></a>Rövid útmutató: Hozzon létre egy Azure-tűzfalat több nyilvános IP-címmel – Erőforrás-kezelő sablon

Ebben a rövid útmutatóban egy Erőforrás-kezelő sablon használatával több nyilvános IP-címmel rendelkező Azure-tűzfalat telepíthet.

A Hálózati attaszabály-gyűjtési szabályok két Windows Server 2019 virtuális géppel létesíthetnek RDP-kapcsolatokat.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall"></a>Azure-tűzfal létrehozása

Ez a sablon létrehoz egy Azure-tűzfal két nyilvános IP-cím, valamint az Azure tűzfal támogatásához szükséges erőforrásokat.

### <a name="review-the-template"></a>A sablon áttekintése

A rövid útmutatóban használt sablon az [Azure rövid útmutató sablonjaiból származik](https://github.com/Azure/azure-quickstart-templates/blob/master/fw-docs-qs/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/fw-docs-qs/azuredeploy.json" range="001-391" highlight="238-370":::

A sablonban több Azure-erőforrás van definiálva:

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

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Ffw-docs-qs%2Fazuredeploy.json"><img src="./media/quick-create-multiple-ip-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. A portálon a **Több IP-nyilvános címmel rendelkező Azure tűzfal létrehozása** lapon írja be vagy válassza ki a következő értékeket:
   - Előfizetés: Válasszon a meglévő előfizetések közül 
   - Erőforráscsoport: Válasszon a meglévő erőforráscsoportok közül, vagy válassza **az Új létrehozása**lehetőséget, és válassza az **OK**gombot.
   - Hely: Hely kiválasztása
   - Rendszergazdai felhasználónév: Írja be a rendszergazdai felhasználói fiók felhasználónevét 
   - Rendszergazdai jelszó: Írjon be rendszergazdai jelszót vagy kulcsot

3. Válassza az **Elfogadom a fenti feltételeket,** majd válassza a **Vásárlás**lehetőséget. A központi telepítés 10 percet vagy hosszabb időt is igénybe vehet.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

Az Azure Portalon tekintse át a üzembe helyezett erőforrásokat. Jegyezze fel a tűzfal nyilvános IP-címeit.  

A Távoli asztali kapcsolat segítségével csatlakozzon a tűzfal nyilvános IP-címeihez. A sikeres kapcsolatok a tűzfal NAT-szabályait mutatják be, amelyek lehetővé teszik a háttérkiszolgálókkal való kapcsolatot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a tűzfallal létrehozott erőforrásokra, törölje az erőforráscsoportot. Ezzel eltávolítja a tűzfalat és az összes kapcsolódó erőforrást.

Az erőforráscsoport törléséhez `Remove-AzResourceGroup` hívja meg a parancsmast:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Tűzfal telepítése és konfigurálása hibrid hálózatban az Azure Portal használatával](tutorial-hybrid-portal.md)
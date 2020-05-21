---
title: 'Gyors útmutató: Azure Firewall létrehozása Availability Zones-Resource Manager-sablonnal'
description: Azure Firewall üzembe helyezése sablon használatával. A virtuális hálózat egy VNet rendelkezik három alhálózattal. Két Windows Server rendszerű virtuális gép van üzembe helyezve; egy Jump Box és egy kiszolgáló.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/30/2020
ms.author: victorh
ms.openlocfilehash: 9b9b7926caa717c1a02988ac7a927bd9bd39d52a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683705"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---resource-manager-template"></a>Gyors útmutató: Azure Firewall üzembe helyezése Availability Zones-Resource Manager-sablonnal

Ebben a rövid útmutatóban egy Resource Manager-sablonnal telepít egy Azure Firewall három Availability Zones. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

A sablon létrehoz egy teszt hálózati környezetet egy tűzfallal. A hálózat egy virtuális hálózattal (VNet) rendelkezik, amely három alhálózattal rendelkezik: *AzureFirewallSubnet*, *ServersSubnet*és *JumpboxSubnet*. A *ServersSubnet* és a *JumpboxSubnet* alhálózatnak egyetlen, kétmagos Windows Server rendszerű virtuális géppel kell rendelkeznie.

A tűzfal a *AzureFirewallSubnet* alhálózatban található, és egy olyan szabály-gyűjteményt tartalmaz, amely lehetővé teszi a hozzáférését a alkalmazáshoz `www.microsoft.com` .

A felhasználó által megadott útvonal hálózati forgalmat végez a *ServersSubnet* alhálózatról a tűzfalon keresztül, ahol a tűzfalszabályok érvényesek.

További információ a Azure Firewallről: [Azure Firewall központi telepítése és konfigurálása a Azure Portal használatával](tutorial-firewall-deploy-portal.md).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-with-availability-zones"></a>Azure Firewall létrehozása Availability Zones

Ez a sablon létrehoz egy Azure Firewall a Availability Zones, valamint a szükséges erőforrásokat a Azure Firewall támogatásához.

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-azurefirewall-with-zones-sandbox)származik.

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json" range="001-444" highlight="369-442":::

Több Azure-erőforrás van definiálva a sablonban:

- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft. Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

A Resource Manager-sablon üzembe helyezése az Azure-ban:

1. Válassza az **üzembe helyezés az Azure** -ban lehetőséget az Azure-ba való bejelentkezéshez és a sablon megnyitásához. A sablon létrehoz egy Azure Firewall, a hálózati infrastruktúrát és két virtuális gépet.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. A portálon, a **Azure Firewall és zónák létrehozása a sandbox létrehozásához** lapon írja be vagy válassza ki a következő értékeket:
   - **Erőforráscsoport**: válassza az **új létrehozása**elemet, írja be az erőforráscsoport nevét, majd kattintson **az OK gombra**. 
   - **Virtual Network neve**: írja be az új VNet nevét. 
   - Rendszergazdai **Felhasználónév**: adjon meg egy felhasználónevet a rendszergazda felhasználói fiókhoz.
   - Rendszergazdai **jelszó**: adjon meg egy rendszergazdai jelszót. 

3. Olvassa el a használati feltételeket, majd válassza az **Elfogadom a fenti feltételeket és kikötéseket** , majd válassza a **vásárlás**lehetőséget. Az üzembe helyezés akár 10 percet is igénybe vehet.

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

Ismerkedjen meg a tűzfallal létrehozott erőforrásokkal.

A sablonban található tűzfal JSON-szintaxisáról és tulajdonságairól a [Microsoft. Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)című témakörben olvashat bővebben.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs szüksége rájuk, eltávolíthatja az erőforráscsoportot, a tűzfalat és az összes kapcsolódó erőforrást a PowerShell- `Remove-AzResourceGroup` parancs futtatásával. A *MyResourceGroup*nevű erőforráscsoport eltávolításához futtassa a következőt: 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Ha folytatni szeretné a tűzfal-figyelési oktatóanyagot, ne távolítsa el az erőforráscsoportot és a tűzfalat. 

## <a name="next-steps"></a>Következő lépések

A következő lépésben monitorozhatja az Azure Firewall naplóit.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](tutorial-diagnostics.md)
---
title: Rövid útmutató – Azure Databricks munkaterület létrehozása a PowerShell használatával
description: Ez a rövid útmutató bemutatja, hogyan használható a PowerShell egy Azure Databricks munkaterület létrehozásához.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 06/02/2020
ms.custom: mvc
ms.openlocfilehash: 445832119a113f92dc1da51eb5b5e77dff176fa3
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485729"
---
# <a name="quickstart-create-an-azure-databricks-workspace-using-powershell"></a>Gyors útmutató: Azure Databricks munkaterület létrehozása a PowerShell használatával

Ez a rövid útmutató azt ismerteti, hogyan használható a PowerShell egy Azure Databricks munkaterület létrehozásához. A PowerShell használatával interaktív módon vagy parancsfájlokban hozhat létre és kezelhet Azure-erőforrásokat.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/Connect-AzAccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Az az. Databricks PowerShell-modul előzetes verzióban érhető el, és a következő paranccsal külön kell telepítenie az az PowerShell-modulból: `Install-Module -Name Az.Databricks -AllowPrerelease` .
> Amint az az. Databricks PowerShell-modul általánosan elérhetővé válik, az a PowerShell modul kiadásainak része lesz, és natív módon elérhető a Azure Cloud Shellon belülről.

Ha első alkalommal használja a Azure Databricks, regisztrálnia kell a **Microsoft. Databricks** erőforrás-szolgáltatót.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Databricks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon egy adott előfizetés-azonosítót a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

A következő példában létrehozunk egy **myresourcegroup** nevű ERŐFORRÁSCSOPORTOT az **USA 2. nyugati** régiójában.

```azurepowershell-interactive
New-AzResourceGroup -Name myresourcegroup -Location westus2
```

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks-munkaterület létrehozása

Ebben a szakaszban egy Azure Databricks munkaterületet hoz létre a PowerShell használatával.

```azurepowershell-interactive
New-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup -Location westus2 -ManagedResourceGroupName databricks-group -Sku standard
```

Adja meg a következő értékeket:

|       **Tulajdonság**       |                                                                                **Leírás**                                                                                 |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Name                     | Adja meg a Databricks-munkaterület nevét.                                                                                                                                   |
| ResourceGroupName        | Adjon meg egy meglévő erőforráscsoport-nevet                                                                                                                                        |
| Hely                 | Válassza az **USA 2. nyugati régióját**. Más elérhető régiók esetében tekintse meg a régiók [által elérhető Azure-szolgáltatások](https://azure.microsoft.com/regions/services/) című témakört.                                     |
| ManagedResourceGroupName | Itt adhatja meg, hogy új felügyelt erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni.                                                                                        |
| SKU                      | Válassza a **standard**, **prémium**vagy **próbaverzió**lehetőséget. További információ ezekről a csomagokról: [Databricks díjszabása](https://azure.microsoft.com/pricing/details/databricks/) |

A munkaterület létrehozása eltarthat néhány percig. A folyamat befejezése után a rendszer automatikusan hozzáadja a felhasználói fiókot a munkaterületen rendszergazdai felhasználóként.

Ha a munkaterület telepítése meghiúsul, a munkaterület még sikertelen állapotban jön létre. Törölje a sikertelen munkaterületet, és hozzon létre egy új munkaterületet, amely feloldja a telepítési hibákat. A sikertelen munkaterület törlésekor a felügyelt erőforráscsoport és a sikeresen telepített erőforrások is törlődnek.

## <a name="determine-the-provisioning-state-of-a-databricks-workspace"></a>Databricks-munkaterület kiépítési állapotának meghatározása

Annak megállapításához, hogy egy Databricks-munkaterület sikeresen lett-e kiépítve, használhatja a `Get-AzDatabricksWorkspace` parancsmagot.

```azurepowershell-interactive
Get-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup |
  Select-Object -Property Name, SkuName, Location, ProvisioningState
```

```Output
Name            SkuName   Location  ProvisioningState
----            -------   --------  -----------------
mydatabricksws  standard  westus2   Succeeded
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az ebben a rövid útmutatóban létrehozott erőforrások nem szükségesek egy másik gyors útmutatóhoz vagy oktatóanyaghoz, a következő példa futtatásával törölheti őket.

> [!CAUTION]
> A következő példa törli a megadott erőforráscsoportot és a benne található összes erőforrást.
> Ha a rövid útmutató hatókörén kívüli erőforrások szerepelnek a megadott erőforráscsoporthoz, akkor azokat is törli a rendszer.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myresourcegroup
```

Ha csak az ebben a rövid útmutatóban létrehozott kiszolgálót szeretné törölni az erőforráscsoport törlése nélkül, használja a `Remove-AzDatabricksWorkspace` parancsmagot.

```azurepowershell-interactive
Remove-AzDatabricksWorkspace -Name mydatabricksws -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Spark-fürt létrehozása a Databricks használatával](quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks)

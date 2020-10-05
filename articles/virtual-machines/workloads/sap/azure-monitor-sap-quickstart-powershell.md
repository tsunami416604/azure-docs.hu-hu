---
title: SAP-megoldások Azure Monitor üzembe helyezése Azure PowerShell
description: SAP-megoldások Azure Monitor üzembe helyezése Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines
ms.devlang: azurepowershell
ms.date: 09/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ab64f3a5ee00296ca7e1d53f8a4c84542e322d71
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89671081"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Gyors útmutató: Azure Monitor üzembe helyezése SAP-megoldásokhoz Azure PowerShell

Ez a cikk azt ismerteti, hogyan hozhat létre Azure Monitor SAP-megoldások erőforrásaihoz az az [. HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) PowerShell-modul használatával.

> [!CAUTION]
> Az SAP-megoldások Azure Monitor jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk. Éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Követelmények

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell](/powershell/azure/install-az-ps). Ha a Cloud Shell használatát választja, további információt [a Azure Cloud Shell áttekintése](https://docs.microsoft.com/azure/cloud-shell/overview) című témakörben talál.

> [!IMPORTANT]
> Míg az az **. HanaOnAzure** PowerShell-modul előzetes verzióban érhető el, a parancsmaggal külön kell telepítenie `Install-Module` . Miután a PowerShell-modul általánosan elérhetővé válik, az a PowerShell-modul kiadásainak részévé válik, és natív módon elérhető lesz a Azure Cloud Shellon belülről.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon ki egy adott előfizetést a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](../../../azure-resource-manager/management/overview.md) a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

A következő példa egy erőforráscsoportot hoz létre a megadott névvel és a megadott helyen.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>SAP-figyelő

SAP-figyelő létrehozásához használja a [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor) parancsmagot. Az alábbi példa egy SAP-figyelőt hoz létre a megadott előfizetéshez, erőforráscsoporthoz és erőforrás nevéhez.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

Egy SAP-figyelő tulajdonságainak lekéréséhez használja a [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor) parancsmagot. A következő példa egy SAP-figyelő tulajdonságait olvassa be a megadott előfizetés, erőforráscsoport és erőforrás neve számára.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Szolgáltatói példány

Szolgáltatói példány létrehozásához használja a [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) parancsmagot. A következő példa egy szolgáltatói példányt hoz létre a megadott előfizetés, erőforráscsoport és erőforrás neve számára.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

Egy szolgáltatói példány tulajdonságainak lekéréséhez használja a [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) parancsmagot. A következő példa egy szolgáltatói példány tulajdonságait olvassa be a megadott előfizetés, Erőforráscsoport, SapMonitor neve és erőforrás neve számára.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a cikkben létrehozott erőforrások nem szükségesek, az alábbi példák futtatásával törölheti őket.

### <a name="delete-the-provider-instance"></a>Szolgáltatói példány törlése

A szolgáltatói példányok eltávolításához használja a [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) parancsmagot. A következő példa egy szolgáltatói példányt töröl a megadott előfizetés, Erőforráscsoport, SapMonitor neve és erőforrás neve számára.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Az SAP-figyelő törlése

Az SAP-figyelő eltávolításához használja a [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor) parancsmagot. A következő példa egy SAP-figyelőt töröl a megadott előfizetés, erőforráscsoport és figyelő neve számára.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

> [!CAUTION]
> A következő példa törli a megadott erőforráscsoportot és a benne található összes erőforrást.
> Ha a cikk hatókörén kívüli erőforrások szerepelnek a megadott erőforráscsoporthoz, akkor azokat is törli a rendszer.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

További információ az [SAP-megoldások Azure monitoráról](azure-monitor-overview.md).

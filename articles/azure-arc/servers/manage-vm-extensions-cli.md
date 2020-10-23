---
title: VM-bővítmény engedélyezése az Azure CLI-vel
description: Ez a cikk bemutatja, hogyan telepíthet virtuálisgép-bővítményeket hibrid felhőalapú környezetekben futó Azure arc-kompatibilis kiszolgálókra az Azure CLI használatával.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 8f09914f246635f07b3c51c682bd67591c706732
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92462961"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure VM-bővítmények engedélyezése az Azure CLI használatával

Ez a cikk bemutatja, hogyan telepítheti és távolíthatja el az Azure arc-kompatibilis kiszolgálók által támogatott Azure-beli virtuálisgép-bővítményeket egy linuxos vagy Windowsos hibrid gépre az Azure CLI használatával.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="prerequisites"></a>Előfeltételek

[Telepítse az Azure CLI](/cli/azure/install-azure-cli)-t.

Mielőtt az Azure CLI-t használja az arc-kompatibilis kiszolgálók által felügyelt hibrid kiszolgálón lévő virtuálisgép-bővítmények kezeléséhez, telepítenie kell a `ConnectedMachine` CLI-bővítményt. Futtassa a következő parancsot az ív használatára képes kiszolgálón:

```azurecli
az extension add connectedmachine
```

A telepítés befejeztével a következő üzenet jelenik meg:

`The installed extension `connectedmachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Bővítmény engedélyezése

Ha engedélyezni szeretné a virtuálisgép-bővítményt az ív használatára képes kiszolgálón, használja az [az connectedmachine Machine-Extension Create](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_create) , a,,,, `--machine-name` `--extension-name` `--location` `--type` `settings` és `--publisher` paraméterek használatával.

Az alábbi példa engedélyezi a Log Analytics virtuálisgép-bővítményt egy arc-kompatibilis Linux-kiszolgálón:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "OmsAgentforLinux" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.EnterpriseCloud.Monitoring" --settings "{\"workspaceId\":\"workspaceId"}" --protected-settings "{\workspaceKey\":"\workspaceKey"} --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Az alábbi példa engedélyezi az egyéni szkriptek bővítményét egy arc-kompatibilis kiszolgálón:

```azurecli
az connectedmachine machine-extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

## <a name="list-extensions-installed"></a>Telepített bővítmények listája

Az ív használatára képes kiszolgálón található virtuálisgép-bővítmények listájának lekéréséhez használja az [az connectedmachine Machine-Extension listát](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_list) a `--machine-name` és a `--resource-group` paraméterekkel.

Példa:

```azurecli
az connectedmachine machine-extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Alapértelmezés szerint az Azure CLI-parancsok kimenete JSON (JavaScript Object Notation). Az alapértelmezett kimenet listára vagy táblára való módosításához használja például az [az configure--output](/cli/azure/reference-index)értéket. Bármely parancshoz hozzáadhatja azt is, ha `--output` a kimeneti formátum egy alkalommal változik.

A következő példa a parancs részleges JSON-kimenetét mutatja `az connectedmachine machine-extension -list` :

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Telepített bővítmény eltávolítása

Ha el szeretné távolítani egy telepített virtuálisgép-bővítményt az ív használatára képes kiszolgálón, használja az [az connectedmachine Machine-Extension delete](/cli/azure/ext/connectedmachine/connectedmachine/machine-extension#ext_connectedmachine_az_connectedmachine_machine_extension_delete) parancsot a (z `--extension-name` `--machine-name` `--resource-group` ) és paraméterrel.

Ha például el szeretné távolítani a linuxos Log Analytics virtuálisgép-bővítményt, futtassa a következő parancsot:

```azurecli
az connectedmachine machine-extension delete --machine-name "myMachineName" --name "OmsAgentforLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

- Virtuálisgép-bővítményeket telepíthet, kezelhet és eltávolíthat a [PowerShell](manage-vm-extensions-powershell.md)használatával, a [Azure Portal](manage-vm-extensions-portal.md)vagy [Azure Resource Manager sablonokból](manage-vm-extensions-template.md).

- A hibaelhárítási információk a virtuálisgép- [bővítmények hibaelhárítási útmutatójában](troubleshoot-vm-extensions.md)találhatók.

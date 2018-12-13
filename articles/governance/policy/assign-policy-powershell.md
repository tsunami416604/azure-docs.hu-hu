---
title: Rövid útmutató – Szabályzat-hozzárendelés létrehozása PowerShell-lel az Azure-környezet nem megfelelő erőforrásainak azonosításához
description: Ebben a gyors útmutatóban a PowerShell használatával hozhat létre egy Azure szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 79e192bae8a1d9ac3e27f35dc2f64befc5a75a84
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078811"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>Gyors útmutató: Szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához az Azure RM PowerShell modullal

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Ebben a rövid útmutatóban egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést hoz létre. Amikor végzett, azonosíthatja a szabályzat-hozzárendelés szempontjából *nem megfelelő* virtuális gépeket.

Az AzureRM PowerShell-modul az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Az útmutató bemutatja, hogyan hozhat létre szabályzat-hozzárendelést az AzureRM használatával. A szabályzat azonosítja az Azure környezetben lévő nem megfelelő erőforrásokat.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem tette meg, telepítse az [ARMClient](https://github.com/projectkudu/ARMClient) eszközt. Ez egy olyan segédprogram, amely HTTP-kéréseket küld az Azure Resource Manager-alapú API-khoz.
- A kezdés előtt ellenőrizze, hogy a PowerShell legújabb verziója van-e telepítve, Részletes információt [az Azure PowerShell telepítését és konfigurálását](/powershell/azureps-cmdlets-docs) ismertető cikkben talál.
- Frissítse az AzureRM PowerShell-modult a legújabb verzióra. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.
- Regisztrálja a Policy Insights erőforrás-szolgáltatót az Azure PowerShell használatával. Az erőforrás-szolgáltató regisztrálásával biztosítja, hogy az előfizetése működni fog vele. Erőforrás-szolgáltató regisztrálásához rendelkeznie kell engedéllyel az erőforrás-szolgáltató regisztrálása művelet. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Az erőforrás-szolgáltató regisztrálásához futtassa az alábbi parancsot:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Az erőforrás-szolgáltatók regisztrálásával és megtekintésével kapcsolatos további információért tekintse meg az [erőforrás-szolgáltatókat és típusaikat](../../azure-resource-manager/resource-manager-supported-services.md) ismertető cikket.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelés létrehozása és hozzárendelése a *felügyelt lemezek nélküli virtuális gépek naplózása* definíciója. Ez a szabályzat-definíció erőforrásokat azonosít, amelyek nem megfelelő a szabályzat-definícióban meghatározott feltételeknek.

Futtassa a következő parancsokat egy új szabályzat-hozzárendelés létrehozásához:

```azurepowershell-interactive
$rg = Get-AzureRmResourceGroup -Name '<resourceGroupName>'
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }
New-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Az előző parancsok a következő információkat használják:

- **Name** – A hozzárendelés tényleges neve.  A fenti példában az *audit-vm-manageddisks* nevet használtuk.
- **DisplayName** – A szabályzat-hozzárendelés megjelenített neve. Ebben az esetben használ *hozzárendelés felügyelt lemezek nélküli virtuális gépek naplózása*.
- **Definíció** – A szabályzatdefiníció, amely alapján létre fogja hozni a hozzárendelést. Ebben az esetben a szabályzatdefiníció azonosítója *felügyelt lemezeket nem használó virtuális gépek naplózása*.
- **Hatókör** – A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ez egyetlen előfizetéstől teljes erőforráscsoportokig terjedhet. Győződjön meg arról, hogy a &lt;scope&gt; helyett az erőforráscsoport neve szerepel.

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelési állapotának megismerése céljából.

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

A következő információkkal azonosíthatja a létrehozott szabályzat-hozzárendelés szempontjából nem megfelelő erőforrásokat. Futtassa az alábbi parancsot:

```azurepowershell-interactive
$policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

A szabályzat-hozzárendelés azonosítóival kapcsolatos további tudnivalókért lásd: [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Ezután futtassa a következő parancsot a JSON-fájlba kerülő nem megfelelő erőforrások azonosítójának lekéréséhez:

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Az eredmények a következő példához hasonlók:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Az eredmények hasonlók ahhoz, amit általában az Azure Portal nézetében a **Nem megfelelő erőforrások** területen láthat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelést eltávolításához használja a következő parancsot:

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

További információ a szabályzatok ellenőrzése, hogy az új erőforrások megfelelnek hozzárendeléséről, folytassa a következő oktatóanyagban:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)
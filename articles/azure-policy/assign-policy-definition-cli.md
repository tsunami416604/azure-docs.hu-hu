---
title: Szabályzat-hozzárendelés létrehozása Azure környezetben a nem megfelelő erőforrások azonosításához az Azure CLI használatával | Microsoft Docs
description: A PowerShell használatával létrehozhat egy Azure szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 1ff1240073e25bf406e7da6b79135264376a5b3f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Szabályzat-hozzárendelés létrehozása Azure környezetben a nem megfelelő erőforrások azonosításához az Azure CLI használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést.

A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából *nem megfelelőnek* minősülnek.

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató az Azure CLI használatával hoz létre szabályzat-hozzárendelést az Azure környezetben lévő nem megfelelő erőforrások azonosításához.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).



## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és hozzárendeli a Felügyelt lemezek nélküli virtuális gépek naplózása definíciót. Ez a szabályzat-definíció olyan erőforrásokat azonosít, amelyek nem felelnek meg a szabályzat-definícióban meghatározott feltételeknek.

Futtassa a következő parancsot egy szabályzat-hozzárendelés létrehozásához:

```
az policy assignment create --name 'Audit Virtual Machines without Managed Disks Assignment' --scope '<scope>' --policy '<policy definition ID>' --sku 'standard'
```

Az előző parancs a következő információkat használja:

- **Név** – A szabályzat-hozzárendeléshez tartozó megjelenített név. Ebben az esetben a *Felügyelt lemezeket nem használó virtuális gépek naplózása – hozzárendelés* nevet használja.
- **Szabályzat** – A szabályzatdefiníció azonosítója, amely alapján létre fogja hozni a hozzárendelést. Ezúttal a *Felügyelt lemezeket nem használó virtuális gépek naplózása* szabályzatdefiníciót fogja használni. A szabályzatdefiníció azonosítójának lekéréséhez futtassa ezt a parancsot: `az policy definition show --name 'Audit Virtual Machines without Managed Disks Assignment'`
- **Hatókör** – A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ez egyetlen előfizetéstől teljes erőforráscsoportokig terjedhet. Győződjön meg arról, hogy a &lt;scope&gt; helyett az erőforráscsoport neve szerepel.
- **Sku** – Ez a parancs standard szinten hoz létre szabályzat-hozzárendelést. A standard szinten tömeges felügyeletet, megfelelőségértékelést és kijavítást végezhet. A díjszabással kapcsolatos további részletek: [Azure Policy – díjszabás](https://azure.microsoft.com/pricing/details/azure-policy).


## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

Az új hozzárendelésnek nem megfelelő erőforrások megtekintéséhez kérje le a szabályzat-hozzárendelés azonosítóját a következő parancsok futtatásával:

```
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```
$policyAssignment.PolicyAssignmentId
```

További tudnivalók a szabályzat-hozzárendelés azonosítóiról: [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Ezután futtassa a következő parancsot a JSON-fájlba kerülő nem megfelelő erőforrások azonosítójának lekéréséhez:

```
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Az eredmények a következő példához hasonlók:

```
{
"@odata.context":"https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
"@odata.count": 3,
"value": [
{
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

Az ebben a gyűjteményben lévő többi útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, törölje a létrehozott hozzárendelést a következő parancs futtatásával:

```azurecli
az policy assignment delete –name Audit Virtual Machines without Managed Disks Assignment --scope /subscriptions/ <subscriptionID> / <resourceGroupName>
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

A szabályzatok hozzárendeléséről, és a **jövőben** létrehozott erőforrások megfelelőségének biztosításáról a következő oktatóanyagban találhat több információt:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./create-manage-policy.md)

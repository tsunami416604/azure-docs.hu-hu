---
title: Gyors útmutató – Szabályzat-hozzárendelés létrehozása PowerShell-lel az Azure-környezetben a nem megfelelő erőforrások azonosításához | Microsoft Docs
description: Ebben a gyors útmutatóban a PowerShell használatával hozhat létre egy Azure szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/10/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 5fa6db380d790e002aa43b19fe77f4fc3c39b7da
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-the-azure-rm-powershell-module"></a>Gyors útmutató: Szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához az Azure RM PowerShell modullal

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Ebben a gyors útmutatóban létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést. Amikor végzett, azonosíthatja a szabályzat-hozzárendelés szempontjából *nem megfelelő* virtuális gépeket.

Az AzureRM PowerShell-modul az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Az útmutató bemutatja, hogyan hozhat létre szabályzat-hozzárendelést az AzureRM használatával. A szabályzat azonosítja az Azure környezetben lévő nem megfelelő erőforrásokat.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

- A kezdés előtt ellenőrizze, hogy a PowerShell legújabb verziója van-e telepítve, Részletes információt [az Azure PowerShell telepítését és konfigurálását](/powershell/azureps-cmdlets-docs) ismertető cikkben talál.
- Frissítse az AzureRM PowerShell-modult a legújabb verzióra. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.
- Regisztrálja a Policy Insights erőforrás-szolgáltatót az Azure PowerShell használatával. Az erőforrás-szolgáltató regisztrálásával biztosítja, hogy az előfizetése működni fog vele. Egy erőforrás-szolgáltató regisztrálásához rendelkeznie kell az erőforrás-szolgáltató regisztrálási műveletének elvégzésére vonatkozó engedéllyel. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Az erőforrás-szolgáltató regisztrálásához futtassa az alábbi parancsot:

  ```
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.PolicyInsights
  ```

  Az erőforrás-szolgáltatók regisztrálásával és megtekintésével kapcsolatos további információért tekintse meg az [erőforrás-szolgáltatókat és típusaikat](../azure-resource-manager/resource-manager-supported-services.md) ismertető cikket.

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és hozzárendeli a *Felügyelt lemezek nélküli virtuális gépek naplózása* definíciót. Ez a szabályzat-definíció olyan erőforrásokat azonosít, amelyek nem felelnek meg a szabályzat-definícióban meghatározott feltételeknek.

Futtassa a következő parancsokat egy új szabályzat-hozzárendelés létrehozásához:

```powershell
$rg = Get-AzureRmResourceGroup -Name "<resourceGroupName>"
$definition = Get-AzureRmPolicyDefinition | where {$_.properties.displayName -eq "Audit VMs that do not use managed disks"}
New-AzureRMPolicyAssignment -Name "Audit Virtual Machines without Managed Disks" -Scope $rg.ResourceId -PolicyDefinition $definition -Sku @{Name='A1';Tier='Standard'}
```

Az előző parancsok a következő információkat használják:

- **Név** – A szabályzat-hozzárendeléshez tartozó megjelenített név. Ebben az esetben a *Felügyelt lemezeket nem használó virtuális gépek naplózása – hozzárendelés* nevet használja.
- **Definíció** – A szabályzatdefiníció, amely alapján létre fogja hozni a hozzárendelést. Ezúttal a *Felügyelt lemezeket nem használó virtuális gépek naplózása* szabályzatdefiníciót fogja használni.
- **Hatókör** – A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ez egyetlen előfizetéstől teljes erőforráscsoportokig terjedhet. Győződjön meg arról, hogy a &lt;scope&gt; helyett az erőforráscsoport neve szerepel.
- **Sku** – Ez a parancs standard szinten hoz létre szabályzat-hozzárendelést. A standard szinten tömeges felügyeletet, megfelelőségértékelést és kijavítást végezhet. A díjszabással kapcsolatos további részletek: [Azure Policy – díjszabás](https://azure.microsoft.com/pricing/details/azure-policy).


Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelési állapotának megismerése céljából.

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

A következő információkkal azonosíthatja a létrehozott szabályzat-hozzárendelés szempontjából nem megfelelő erőforrásokat. Futtassa az alábbi parancsot:

```powershell
$policyAssignment = Get-AzureRmPolicyAssignment | where {$_.properties.displayName -eq "Audit Virtual Machines without Managed Disks"}
```

```powershell
$policyAssignment.PolicyAssignmentId
```

További tudnivalók a szabályzat-hozzárendelés azonosítóiról: [Get-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/get-azurermpolicyassignment).

Ezután futtassa a következő parancsot a JSON-fájlba kerülő nem megfelelő erőforrások azonosítójának lekéréséhez:

```powershell
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

Az ebben a gyűjteményben található további útmutatók erre a gyors útmutatóra épülnek. Ha azt tervezi, hogy a többi oktatóanyaggal dolgozik tovább, akkor ne törölje az ebben a gyors útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, törölje a létrehozott hozzárendelést a következő parancs futtatásával:

```powershell
Remove-AzureRmPolicyAssignment -Name "Audit Virtual Machines without Managed Disks Assignment" -Scope /subscriptions/<subscriptionID>/<resourceGroupName>
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

A szabályzatok hozzárendeléséről és a **jövőben** létrehozott erőforrások megfelelőségének biztosításáról a következő oktatóanyagban találhat több információt:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./create-manage-policy.md)

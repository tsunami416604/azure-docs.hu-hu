---
title: "Szabályzat-hozzárendelés létrehozása PowerShell-lel az Azure-környezetben a nem megfelelő erőforrások azonosításához | Microsoft Docs"
description: "A PowerShell használatával létrehozhat egy Azure szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 6a9b7cff1341bd898b76a226ca413b8135eec408
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>Szabályzat-hozzárendelés létrehozása PowerShell-lel az Azure-környezetben a nem megfelelő erőforrások azonosításához

Az Azure-ral való megfelelőség megértéséhez szükséges első szempont a saját jelenlegi erőforrásainak ismerete. Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést.

A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó, azaz *nem megfelelő* virtuális gépeket.


A PowerShell használatával Azure-erőforrások hozhatók létre és kezelhetők a parancssorból vagy szkriptekben. Ez az útmutató részletesen bemutatja, hogyan használható a PowerShell a szabályzat-hozzárendelés létrehozására az Azure-környezetben a nem megfelelő erőforrások azonosításához.

Ehhez az útmutatóhoz az Azure PowerShell-modul 4.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt:  ```Get-Module -ListAvailable AzureRM``` . Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

A kezdés előtt ellenőrizze, hogy a PowerShell legújabb verziója van-e telepítve, Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.


## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hozunk létre, és hozzárendeljük a *Felügyelt lemezeket nem használó virtuális gépek naplózása* definíciót. Ez a szabályzat-definíció olyan erőforrásokat azonosít, amelyek nem felelnek meg a szabályzatdefinícióban meghatározott feltételeknek.

Kövesse az alábbi lépéseket egy új szabályzat-hozzárendelés létrehozásához.

Futtassa az alábbi parancsot az összes szabályzatdefiníció megjelenítéséhez, majd keresse meg azt, amelyiket hozzá szeretne rendelni:

```powershell
$definition = Get-AzureRmPolicyDefinition
```

Az Azure Policy beépített szabályzatdefiníciókat tartalmaz, amelyeket felhasználhat. Többek között a következő beépített szabályzat-definíciókat láthatja:

- Címke és a hozzá tartozó érték kényszerítése
- Címke és a hozzá tartozó érték alkalmazása
- SQL Server 12.0-ás verziójának megkövetelése

Ezt követően rendelje hozzá a szabályzatdefiníciót a kívánt hatókörhöz a `New-AzureRmPolicyAssignment` parancsmaggal.

Ebben az útmutatóban az alábbi adatokat adjuk meg a parancshoz:
- A szabályzatdefinícióhoz tartozó megjelenített **név**. Ebben az esetben a név legyen Felügyelt lemezeket nem használó virtuális gépek naplózása.
- **Szabályzat** – Ez az a szabályzatdefiníció, amely alapján létre fogja hozni a hozzárendelést. Ezúttal a *Felügyelt lemezeket nem használó virtuális gépek naplózása* szabályzatdefiníciót fogjuk használni
- **Hatókör** – A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ez egyetlen előfizetéstől teljes erőforráscsoportokig terjedhet. Ebben a példában a szabályzatdefiníciót a **FabrikamOMS** erőforráscsoporthoz rendeljük.
- **$definition** – Meg kell adnia a szabályzatdefiníció erőforrás-azonosítóját – Ezúttal a *Felügyelt lemezeket nem használó virtuális gépek naplózása* szabályzatdefiníciót fogjuk használni.

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelési állapotának megismerése céljából.

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

1. Lépjen vissza az Azure Policy kezdőlapjára.
2. Válassza a **Megfelelőség** elemet a bal oldali panelen, majd keresse meg az imént létrehozott **Szabályzat-hozzárendelést**.

   ![Szabályzatmegfelelőség](media/assign-policy-definition/policy-compliance.png)

   Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, azok a **Nem megfelelő erőforrások** lapon jelennek meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, törölje a létrehozott hozzárendelést a következő parancs futtatásával:

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

A szabályzatok a **jövőben** létrehozott erőforrások megfelelőségének biztosítása érdekében történő hozzárendeléséről a következő oktatóanyagban találhat több információt:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./create-manage-policy.md)

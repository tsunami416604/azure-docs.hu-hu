---
title: Az első lekérdezés futtatása a PowerShell használatával
description: Ez a cikk végigvezeti az Azure PowerShellhez szükséges Resource Graph modul aktiválásának lépésein és az első lekérdezése futtatásán.
ms.date: 10/18/2019
ms.topic: quickstart
ms.openlocfilehash: 6aaddcfc6cae16501ba6544f9789aa3ac1d13136
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958786"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Rövid útmutató: az első Resource Graph-lekérdezés futtatása a Azure PowerShell használatával

Az Azure Resource Graph használatához először meg kell győződnie arról, hogy az Azure PowerShellhez szükséges modul telepítve van. E rövid útmutató segítségével hozzáadhatja a modult a telepített Azure PowerShellhez.

Ezen folyamatban a modult hozzáadja az Azure PowerShell-telepítéshez, és futtatja az első Resource Graph-lekérdezést.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="add-the-resource-graph-module"></a>A Resource Graph modul hozzáadása

Ahhoz, hogy az Azure PowerShell lekérdezhesse az Azure Resource Graphot, el kell végezni a modul hozzáadását. Ez a modul helyileg telepített PowerShell-lel, [Azure Cloud Shell](https://shell.azure.com)vagy a [PowerShell Docker-lemezképpel](https://hub.docker.com/_/microsoft-powershell)is használható.

### <a name="base-requirements"></a>Alapszintű követelmények

Az Azure Resource Graph modulhoz az alábbi szoftverek szükségesek:

- Azure PowerShell 1.0.0 vagy újabb. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1-es vagy újabb verzió. Ha nincs telepítve vagy frissítve, kövesse [ezeket az utasításokat](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>A modul telepítése

A PowerShellhez készült Resource Graph-modul az **az. ResourceGraph**.

1. **Rendszergazdai** PowerShell-parancssorból futtassa a következő parancsot:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Ellenőrizze, hogy a modul importálása megtörtént-e, és a legújabb verzió-e (0.7.5):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Miután az Azure PowerShell modul hozzá lett adva a választott környezethez, ideje futtatni egy egyszerű Resource Graph-lekérdezést. A lekérdezés az első öt Azure-erőforrást fogja visszaadni az egyes erőforrások **nevével** és **erőforrástípusával**.

1. Futtassa az első Azure Resource Graph-lekérdezését a `Search-AzGraph` parancsmag használatával:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Ez a lekérdezési példa nem biztosít olyan rendezési módosítót, mint az `order by`, tehát ha többször is futtatja, valószínűleg minden kéréssel eltérő erőforráslistát fog kapni.

1. Frissítse a lekérdezést úgy, hogy a rendezési szempont (`order by`) a **Name** tulajdonság legyen:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Így először korlátozza a lekérdezés eredményeit, majd rendezi őket.

1. Először frissítse a lekérdezést, hogy a `order by`Name **tulajdonság szerint legyen rendezve (** ), majd korlátozza (`limit`) az első öt találatra:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Miután a végső lekérdezés többször is futott, és feltéve, hogy a környezetben semmi sem változik, a visszaadott találatok konzisztensek és a vártnak megfelelőek lesznek – a **Name** tulajdonság szerint lesznek rendezve, és csak az első öt eredmény jelenik meg.

> [!NOTE]
> Ha a lekérdezés nem ad vissza olyan előfizetésből származó eredményeket, amelyhez már van hozzáférése, vegye figyelembe, hogy `Search-AzGraph` parancsmag alapértelmezett környezetében az előfizetések alapértelmezés szerint szerepelnek. Ha szeretné megtekinteni az alapértelmezett környezet részét képező előfizetési azonosítók listáját, futtassa ezt a `(Get-AzContext).Account.ExtendedProperties.Subscriptions`, ha az összes olyan előfizetést keresni kívánja, amelyhez hozzáféréssel rendelkezik, az `Search-AzGraph` parancsmag PSDefaultParameterValues a futtatásával állíthatja be `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a Resource Graph modult az Azure PowerShell-környezetből, ezt a következő paranccsal teheti meg:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Ezzel nem törli a korábban letöltött modulfájlt. Csak eltávolítja azt a futó PowerShell-munkamenetből.

## <a name="next-steps"></a>Következő lépések

- További információ a [lekérdezési nyelvről](./concepts/query-language.md).
- További információ az [erőforrások feltárásáról](./concepts/explore-resources.md).
- Futtassa az első lekérdezést a [Azure Portal](first-query-portal.md)használatával.
- Futtassa az első lekérdezést az [Azure CLI](first-query-azurecli.md)-vel.
- Tekintse meg az [alapszintű lekérdezések](./samples/starter.md)mintáit.
- Lásd a [speciális lekérdezések](./samples/advanced.md)mintáit.
- Visszajelzés küldése a [UserVoice](https://feedback.azure.com/forums/915958-azure-governance).
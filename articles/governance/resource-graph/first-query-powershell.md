---
title: 'Rövid útmutató: Az első PowerShell-lekérdezés'
description: Ebben a rövid útmutatóban kövesse a lépéseket az Azure PowerShell Resource Graph moduljának engedélyezéséhez, és futtassa az első lekérdezést.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: dd96324671f46f98d5b6c8bae1839a5b02d38b23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240659"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Rövid útmutató: Futtassa az első Resource Graph-lekérdezést az Azure PowerShell használatával

Az Azure Resource Graph használatához először meg kell győződnie arról, hogy az Azure PowerShellhez szükséges modul telepítve van. E rövid útmutató segítségével hozzáadhatja a modult a telepített Azure PowerShellhez.

Ezen folyamatban a modult hozzáadja az Azure PowerShell-telepítéshez, és futtatja az első Resource Graph-lekérdezést.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>A Resource Graph modul hozzáadása

Ahhoz, hogy az Azure PowerShell lekérdezhesse az Azure Resource Graphot, el kell végezni a modul hozzáadását. Ez a modul használható a helyileg telepített PowerShell, [az Azure Cloud Shell,](https://shell.azure.com)vagy a [PowerShell Docker-rendszerkép.](https://hub.docker.com/_/microsoft-powershell)

### <a name="base-requirements"></a>Alapszintű követelmények

Az Azure Resource Graph modulhoz az alábbi szoftverek szükségesek:

- Azure PowerShell 1.0.0 vagy újabb. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1-es vagy újabb verzió. Ha nincs telepítve vagy frissítve, kövesse [ezeket az utasításokat](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>A modul telepítése

A PowerShell Erőforrásgráf modulja az **Az.ResourceGraph.**

1. Egy **felügyeleti** PowerShell-parancssorból futtassa a következő parancsot:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Annak ellenőrzése, hogy a modul importálása megtörtént-e, és a legújabb verzió (0.7.5):

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

1. Először frissítse a lekérdezést, hogy a **Name** tulajdonság szerint legyen rendezve (`order by`), majd korlátozza (`limit`) az első öt találatra:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Miután a végső lekérdezés többször is futott, és feltéve, hogy a környezetben semmi sem változik, a visszaadott találatok konzisztensek és a vártnak megfelelőek lesznek – a **Name** tulajdonság szerint lesznek rendezve, és csak az első öt eredmény jelenik meg.

> [!NOTE]
> Ha a lekérdezés nem ad vissza eredményeket egy olyan `Search-AzGraph` előfizetésből, amelyhez már rendelkezik hozzáféréssel, vegye figyelembe, hogy a parancsmag alapértelmezés szerint az alapértelmezett környezetben az előfizetések. Az alapértelmezett környezet részét képező előfizetés-azonosítók listájának `(Get-AzContext).Account.ExtendedProperties.Subscriptions` megtekintéséhez futtassa ezt a lehetőséget: Ha az összes olyan előfizetésben `Search-AzGraph` szeretne keresni, amelyhez hozzáférése van, beállíthatja a PSDefaultParameterValues parancsot a parancsmaghoz a`$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
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

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban hozzáadta a Resource Graph modult az Azure PowerShell-környezethez, és futtatja az első lekérdezést. Ha többet szeretne megtudni az Erőforrás-diagram nyelvéről, folytassa a lekérdezésnyelv részletei lappal.

> [!div class="nextstepaction"]
> [További információ a lekérdezés nyelvéről](./concepts/query-language.md)
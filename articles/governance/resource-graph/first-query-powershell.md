---
title: Azure PowerShell-lel az első lekérdezés futtatása
description: Ez a cikk végigvezeti az Azure PowerShellhez szükséges Resource Graph modul aktiválásának lépésein és az első lekérdezése futtatásán.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/27/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: bf23dcdb212c1d7e052b12179084df9ccc941664
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434475"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Az első Resource Graph-lekérdezés futtatása az Azure PowerShell használatával

Az Azure Resource Graph használatához először meg kell győződnie arról, hogy az Azure PowerShellhez szükséges modul telepítve van. E rövid útmutató segítségével hozzáadhatja a modult a telepített Azure PowerShellhez.

Ezen folyamatban a modult hozzáadja az Azure PowerShell-telepítéshez, és futtatja az első Resource Graph-lekérdezést.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="add-the-resource-graph-module"></a>A Resource Graph modul hozzáadása

Ahhoz, hogy az Azure PowerShell lekérdezhesse az Azure Resource Graphot, el kell végezni a modul hozzáadását. Ez a modult a helyileg telepített Windows PowerShell-lel és PowerShell Core-ral, valamint az [Azure PowerShell Docker-rendszerképével](https://hub.docker.com/r/azuresdk/azure-powershell/) használható.

### <a name="base-requirements"></a>Alapszintű követelmények

Az Azure Resource Graph modulhoz az alábbi szoftverek szükségesek:

- Az Azure PowerShell 6.3.0 vagy újabb verziója. Ha még nincs telepítve, kövesse [ezeket az utasításokat](/powershell/azure/azurerm/install-azurerm-ps).

  - A PowerShell Core esetében használja az Azure PowerShell modul **Az** verzióját.

  - A Windows PowerShell esetében használja az Azure PowerShell modul **AzureRm** verzióját.

- PowerShellGet 2.0.1-es vagy újabb verzió. Ha nincs telepítve vagy frissítve, kövesse [ezeket az utasításokat](/powershell/gallery/installing-psget).

### <a name="cloud-shell"></a>Cloud Shell

Az Azure Resource Graph modul Cloud Shellbe való felvételéhez kövesse a PowerShell Core-ra vonatkozó alábbi utasításokat.

### <a name="powershell-core"></a>PowerShell Core

A PowerShell Core Resource Graph modulja az **Az.ResourceGraph**.

1. Egy **felügyeleti** PowerShell Core-parancssorban futtassa a következő parancsot:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Győződjön meg róla, hogy a modul importálva lett, és a megfelelő verziójú (0.3.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

1. Engedélyezze a fordított aliasokat az **Az** és az **AzureRm** számára a következő paranccsal:

   ```azurepowershell-interactive
   # Enable backwards alias compatibility
   Enable-AzureRmAlias
   ```

### <a name="windows-powershell"></a>Windows PowerShell

A Windows PowerShell Resource Graph modulja az **AzureRm.ResourceGraph**.

1. Egy **felügyeleti** Windows PowerShell-parancssorban futtassa a következő parancsot:

   ```powershell
   # Install the Resource Graph (prerelease) module from PowerShell Gallery
   Install-Module -Name AzureRm.ResourceGraph -AllowPrerelease
   ```

1. Győződjön meg róla, hogy a modul importálva lett, és a megfelelő verziójú (0.1.1-preview):

   ```powershell
   # Get a list of commands for the imported AzureRm.ResourceGraph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Miután az Azure PowerShell modul hozzá lett adva a választott környezethez, ideje futtatni egy egyszerű Resource Graph-lekérdezést. A lekérdezés az első öt Azure-erőforrást fogja visszaadni az egyes erőforrások **nevével** és **erőforrástípusával**.

1. Futtassa az első Azure Resource Graph-lekérdezését a `Search-AzureRmGraph` parancsmag használatával:

   ```azurepowershell-interactive
   # Login first with Connect-AzureRmAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Ez a lekérdezési példa nem biztosít olyan rendezési módosítót, mint az `order by`, tehát ha többször is futtatja, valószínűleg minden kéréssel eltérő erőforráslistát fog kapni.

1. Frissítse a lekérdezést úgy, hogy a rendezési szempont (`order by`) a **Name** tulajdonság legyen:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Így először korlátozza a lekérdezés eredményeit, majd rendezi őket.

1. Először frissítse a lekérdezést, hogy a **Name** tulajdonság szerint legyen rendezve (`order by`), majd korlátozza (`limit`) az első öt találatra:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Miután a végső lekérdezés többször is futott, és feltéve, hogy a környezetben semmi sem változik, a visszaadott találatok konzisztensek és a vártnak megfelelőek lesznek – a **Name** tulajdonság szerint lesznek rendezve, és csak az első öt eredmény jelenik meg.

## <a name="cleanup"></a>Felesleges tartalmak törlése

Ha el szeretné távolítani a Resource Graph modult az Azure PowerShell-környezetből, ezt a következő paranccsal teheti meg:

```powershell
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'AzureRm.ResourceGraph'
```

> [!NOTE]
> Ezzel nem törli a korábban letöltött modulfájlt. Csak eltávolítja azt a futó PowerShell-munkamenetből.

## <a name="next-steps"></a>További lépések

- További információ a [lekérdezés nyelvéről](./concepts/query-language.md)
- Információ az [erőforrások felfedezéséről](./concepts/explore-resources.md)
- Az első lekérdezés futtatása az [Azure CLI-vel](first-query-azurecli.md)
- Az [Alapszintű lekérdezések](./samples/starter.md) példáinak megtekintése
- A [Speciális lekérdezések](./samples/advanced.md) példáinak megtekintése
- Visszajelzés küldése a [UserVoice-ról](https://feedback.azure.com/forums/915958-azure-governance)
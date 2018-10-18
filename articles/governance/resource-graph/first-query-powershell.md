---
title: Az első Resource Graph-lekérdezés futtatása az Azure PowerShell használatával
description: Ez a cikk végigvezeti az Azure PowerShellhez szükséges Resource Graph modul aktiválásának lépésein és az első lekérdezése futtatásán.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: resource-graph
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 1a2bc5626e94f5fcb0ec8c2be8d91c8fc6484e0b
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224562"
---
# <a name="run-your-first-resource-graph-query-using-azure-powershell"></a>Az első Resource Graph-lekérdezés futtatása az Azure PowerShell használatával

Az Azure Resource Graph használatához először is meg kell győződnie arról, hogy az Azure PowerShellhez szükséges modul telepítve van. E rövid útmutató segítségével hozzáadhatja a modult a telepített Azure PowerShellhez.

E folyamat végén a modul hozzá lesz adva a kiválasztott Azure PowerShell-telepítéshez, és máris futtathatja az első Resource Graph-lekérdezését.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="add-the-resource-graph-module"></a>A Resource Graph modul hozzáadása

Ahhoz, hogy az Azure PowerShell lekérdezhesse az Azure Resource Graphot, el kell végezni a modul hozzáadását. Ezt a modult a helyileg telepített Windows PowerShell és PowerShell Core szolgáltatással, valamint az [Azure PowerShell Docker-rendszerképével](https://hub.docker.com/r/azuresdk/azure-powershell/) használhatja.

### <a name="base-requirements"></a>Alapszintű követelmények

Az Azure Resource Graph modulhoz az alábbi szoftverek szükségesek:

- Az Azure PowerShell 6.3.0 vagy újabb verziója. Ha még nem telepítette, kövesse [ezeket az utasításokat](/powershell/azure/install-azurerm-ps).

  - A PowerShell Core esetében használja az Azure PowerShell modul **Az** verzióját.

  - A Windows PowerShell esetében használja az Azure PowerShell modul **AzureRm** verzióját.

  > [!NOTE]
  > A modul a Cloud Shellben való telepítése jelenleg nem javasolt.

- PowerShellGet. Ha még nem telepítette vagy frissítette, kövesse [ezeket az utasításokat](/powershell/gallery/installing-psget).

### <a name="powershell-core"></a>PowerShell Core

A PowerShell Core Resource Graph modulja az **Az.ResourceGraph**.

1. Egy **felügyeleti** PowerShell Core-parancssorban futtassa a következő parancsot:

   ```powershell
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Ellenőrizze, hogy importálta-e a modult, és a megfelelő verzióval működik-e (0.2.0):

   ```powershell
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

1. Engedélyezze a fordított aliasokat az **Az** és az **AzureRm** számára a következő paranccsal:

   ```powershell
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

1. Ellenőrizze, hogy importálta-e a modult, és a megfelelő verzióval működik-e (0.1.0 – előzetes verzió):

   ```powershell
   # Get a list of commands for the imported AzureRm.ResourceGraph module
   Get-Command -Module 'AzureRm.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Most, hogy az Azure PowerShell modult hozzáadta a választott környezethez, már meg is próbálhat futtatni egy egyszerű Resource Graph-lekérdezést. A lekérdezés az első öt Azure-erőforrást fogja visszaadni az egyes erőforrások **nevével** és **erőforrástípusával**.

1. Futtassa az első Azure Resource Graph-lekérdezését a `Search-AzureRmGraph` parancsmag használatával:

   ```powershell
   # Login first with Connect-AzureRmAccount

   # Run Azure Resource Graph query
   Search-AzureRmGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Ez a lekérdezési példa nem biztosít olyan rendezési módosítót, mint az `order by`, tehát ha többször is futtatja, valószínűleg minden kéréssel eltérő erőforráslistát fog kapni.

1. Frissítse a lekérdezést úgy, hogy a rendezési szempont (`order by`) a **Name** tulajdonság legyen:

   ```powershell
   # Run Azure Resource Graph query with 'order by'
   Search-AzureRmGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

  > [!NOTE]
  > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Így először korlátozza a lekérdezés eredményeit, majd rendezi őket.

1. Frissítse a lekérdezést először úgy, hogy az `order by` a **Name** tulajdonság legyen, majd korlátozza (`limit`) a top 5 eredményre:

   ```powershell
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzureRmGraph -Query 'project name, type | order by name asc | limit 5'
   ```

Ha az utolsó lekérdezés többször is lefutott, és feltéve, hogy a környezetben semmi sem változik, a visszaadott eredmények konzisztensek és a vártnak megfelelőek lesznek – a **Name** tulajdonság szerint rendezve, de csak a top 5 eredményt megjelenítve.

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
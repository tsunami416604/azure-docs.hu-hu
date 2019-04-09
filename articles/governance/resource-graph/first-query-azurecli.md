---
title: Azure CLI-vel az első lekérdezés futtatása
description: Ez a cikk végigvezeti az Azure CLI-hez szükséges Resource Graph bővítmény aktiválásának lépésein és az első lekérdezése futtatásán.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/22/2018
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 188733266c15bf214c0177762b9d10aecd05223b
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266477"
---
# <a name="run-your-first-resource-graph-query-using-azure-cli"></a>Az első Resource Graph-lekérdezés futtatása az Azure CLI használatával

Az Azure Resource Graph használatához először is meg kell győződnie arról, hogy az [Azure CLI-hez](/cli/azure/) szükséges bővítmény telepítve van. E rövid útmutató segítségével hozzáadhatja a bővítményt a telepített Azure CLI-hez. A bővítményt helyileg telepített Azure CLI-vel vagy az [Azure Cloud Shellen](https://shell.azure.com) keresztül is használhatja.

A folyamat végére a bővítmény hozzá lesz adva a kiválasztott Azure CLI-telepítéshez, és máris futtathatja az első Resource Graph-lekérdezését.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="add-the-resource-graph-extension"></a>A Resource Graph bővítmény hozzáadása

Ahhoz, hogy az Azure CLI lekérdezhesse az Azure Resource Graph-ot, el kell végezni a bővítmény hozzáadását. Ez a bővítmény mindenhol működik, ahol az Azure CLI használható, beleértve a [Basht Windows 10-en](/windows/wsl/install-win10), a [Cloud Shellt](https://shell.azure.com) (különállón és portálon belülin egyaránt), az [Azure CLI Docker-rendszerképet](https://hub.docker.com/r/microsoft/azure-cli/), vagy akár helyileg telepítve is.

1. Győződjön meg arról, hogy a legfrissebb Azure CLI van telepítve (legalább a **2.0.45-ös** verzió). Ha még nincs telepítve, kövesse [ezeket az utasításokat](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

1. A választott Azure CLI környezetben importálja a bővítményt a következő paranccsal:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Ellenőrizze, hogy a bővítmény telepítve van-e, és a várt verziójú-e (legalább **0.1.7-es**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

Miután az Azure CLI-bővítmény hozzá lett adva a választott környezethez, ideje futtatni egy egyszerű Resource Graph-lekérdezést. A lekérdezés az első öt Azure-erőforrást fogja visszaadni az egyes erőforrások **nevével** és **erőforrástípusával**.

1. Futtassa az első Azure Resource Graph-lekérdezését a `graph` bővítmény és a `query` parancs használatával:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Ez a lekérdezési példa nem biztosít olyan rendezési módosítót, mint az `order by`, tehát ha többször is futtatja, valószínűleg minden kéréssel eltérő erőforráslistát fog kapni.

1. Frissítse a lekérdezést úgy, hogy a rendezési szempont (`order by`) a **Name** tulajdonság legyen:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Így először korlátozza a lekérdezés eredményeit, majd rendezi őket.

1. Először frissítse a lekérdezést, hogy a **Name** tulajdonság szerint legyen rendezve (`order by`), majd korlátozza (`limit`) az első öt találatra:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'project name, type | order by name asc | limit 5'
   ```

Miután a végső lekérdezés többször is futott, és feltéve, hogy a környezetben semmi sem változik, a visszaadott találatok konzisztensek és a vártnak megfelelőek lesznek – a **Name** tulajdonság szerint lesznek rendezve, és csak az első öt eredmény jelenik meg.

## <a name="cleanup"></a>Felesleges tartalmak törlése

Ha el szeretné távolítani a Resource Graph bővítményt az Azure CLI környezetből, ezt a következő paranccsal teheti meg:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

> [!NOTE]
> Ezzel nem törli a korábban letöltött bővítményfájlt, csak eltávolítja a futó Azure CLI-környezetből.

## <a name="next-steps"></a>További lépések

- További információ a [lekérdezés nyelvéről](./concepts/query-language.md)
- Információk az [erőforrások felfedezéséről](./concepts/explore-resources.md)
- Az első lekérdezés futtatása az [Azure PowerShell-lel](first-query-powershell.md)
- Az [Alapszintű lekérdezések](./samples/starter.md) példáinak megtekintése
- A [Speciális lekérdezések](./samples/advanced.md) példáinak megtekintése
- Visszajelzés küldése a [UserVoice-ról](https://feedback.azure.com/forums/915958-azure-governance)
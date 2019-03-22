---
title: Ellenőrizze az Azure-erőforrások használatára korlátozások |} A Microsoft Docs
description: Ismerje meg, hogyan ellenőrizheti az Azure-előfizetés korlátozások az Azure-erőforrások használatára.
services: networking
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: jdial
ms.openlocfilehash: 54050c4c20b6ebb35f198775448f51ee8cdc533b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117642"
---
# <a name="check-resource-usage-against-limits"></a>Ellenőrizze az erőforrás-használati korlátozások

Ebből a cikkből elsajátíthatja, hogyan tekintsük meg minden egyes hálózati erőforrás típusa, amely az előfizetésben, és mi üzembe helyezte a [előfizetési korlátozásait](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) vannak. Erőforrás-használati korlátozások megtekintése hasznos aktuális használat nyomon követése, és tervezze meg jövőbeli használatra. Használhatja a [az Azure Portal](#azure-portal), [PowerShell](#powershell), vagy a [Azure CLI-vel](#azure-cli) használat nyomon követésére.

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az Azure [portál](https://portal.azure.com).
2. Az Azure portal bal felső sarokban, válassza **minden szolgáltatás**.
3. Adja meg *előfizetések* a a **szűrő** mezőbe. Amikor **előfizetések** megjelenik a keresési eredmények között, válassza ki.
4. Válassza ki az előfizetés nevét adja meg szeretné tekinteni a kapcsolódó használati adatok.
5. A **beállítások**válassza **használat + kvóta**.
6. Választhat a következő beállításokat:
   - **Erőforrástípusok**: Válassza ki az összes erőforrástípus, vagy válassza ki az adott típusú erőforrás meg szeretné jeleníteni.
   - **Szolgáltatók**: Válassza ki az összes erőforrás-szolgáltató, vagy válasszon **számítási**, **hálózati**, vagy **tárolási**.
   - **Helyek**: Válassza ki az összes Azure-helyen, vagy válassza ki az adott helyeken.
   - Kiválaszthatja a megjelenítendő összes erőforrását, vagy csak az erőforrások közül legalább egy telepítési helyét.

     A következő képen látható a példa bemutatja, hogy legalább egy erőforrás üzembe helyezve, az USA keleti régiójában és hálózati erőforrásokat:

       ![Használati adatok megtekintése](./media/check-usage-against-limits/view-usage.png)

     Az oszlopok az oszlopok fejlécére kattintva rendezhető. A korlátok látható korlátok vonatkoznak az előfizetésében. Ha szeretne egy alapértelmezett korlát növeléséhez, válassza ki a **kérelem növelése**, majd végezze el, és a támogatási kérelem elküldéséhez. Összes erőforrásnak rendelkeznie kell az Azure-ban szereplő maximális korlát [korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). Ha az aktuális korlát már a maximális számát, a korlát nem növelhető.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő parancsokat futtathat a [Azure Cloud Shell](https://shell.azure.com/powershell), vagy a számítógépről futtatja a Powershellt. Az Azure Cloud Shell olyan ingyenes interaktív kezelőfelület. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a számítógépről futtatja a PowerShell, szüksége van-e az Azure PowerShell-modult, 1.0.0-s verziójának vagy újabb. Futtatás `Get-Module -ListAvailable Az` a számítógépen, a telepített verzió azonosításához. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja PowerShell, is futtatni szeretné `Login-AzAccount` bejelentkezni az Azure-bA.

A korlátozások a használati adatok megtekintéséhez [Get-AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage). Az alábbi példa lekéri a használati erőforrásokhoz, amennyiben legalább egy erőforrás üzembe van helyezve az USA keleti régiójában:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Kimeneti kap formázott ugyanaz, mint az alábbi példa kimenetében:

```powershell
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

Ha használja az Azure parancssori felület (CLI) parancsai cikkben leírt feladatok elvégzéséhez, vagy futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/bash), vagy a parancssori felület futtatásával a számítógépről. Ehhez a cikkhez az Azure CLI 2.0.32 verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. Ha helyileg futtatja az Azure CLI, is futtatni szeretné `az login` bejelentkezni az Azure-bA.

A korlátozások a használati adatok megtekintéséhez [az network list-usages](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). Az alábbi példa lekéri az erőforrások felhasználását, az USA keleti régiójában:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Kimeneti kap formázott ugyanaz, mint az alábbi példa kimenetében:

```azurecli
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```

---
title: Ellenőrizze az Azure erőforrás-használati korlátozások |} Microsoft Docs
description: Útmutató az Azure-előfizetés korlátozások az Azure erőforrás-felhasználás ellenőrzése.
services: networking
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: networking
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: jdial
ms.openlocfilehash: 0c51f48576c665fbe67f2f18198d6422fe872895
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34811736"
---
# <a name="check-resource-usage-against-limits"></a>Ellenőrizze az erőforrás-használati korlátozások

Ebből a cikkből megtanulhatja, az előfizetését és mi telepítése után minden egyes hálózati erőforrástípust száma a [előfizetési korlátozásait](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) vannak. Az erőforrás-használati korlátozások megtekintésének képességét akkor hasznos, a jelenlegi használat követése, és tervezze meg későbbi felhasználás céljából. Használhatja a [Azure Portal](#azure-portal), [PowerShell](#powershell), vagy a [Azure CLI](#azure-cli) használat nyomon követéséhez.

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be a Azure [portal](https://portal.azure.com).
2. Az Azure-portálon bal felső sarokban, válassza **minden szolgáltatás**.
3. Adja meg *előfizetések* a a **szűrő** mezőbe. Ha **előfizetések** megjelenik a keresési eredmények között, jelölje be.
4. Válassza ki, hogy meg szeretné tekinteni a kapcsolódó használati adatok az előfizetés nevét.
5. A **beállítások**, jelölje be **használati + kvóta**.
6. Választhat, hogy a következő beállításokat:
    - **Erőforrástípus**: válassza ki az összes erőforrástípus, vagy válassza ki a megtekinteni kívánt erőforrások megadott típusú.
    - **Szolgáltatók**: válassza ki az összes erőforrás-szolgáltató, vagy válasszon **számítási**, **hálózati**, vagy **tárolási**.
    - **Helyek**: válassza ki az összes Azure helyét, vagy válassza ki az adott helyeken.
    - Kiválaszthatja, hogy minden erőforrás vagy a csak az erőforrások közül legalább egy telepítési helyét.

    Az alábbi képen látható példa látható minden olyan a hálózati erőforrások, az USA keleti régiója telepítve legalább egy erőforráshoz:

        ![View usage data](./media/check-usage-against-limits/view-usage.png)

    Az oszlopok az oszlopok fejlécét választva rendezheti. Megadott előfizetési korlátai. Ha egy alapértelmezett korlát növeléséhez van szüksége, válassza ki a **kérelem növelése**, majd végezze el, és a támogatási kérelem elküldéséhez. Minden erőforrás legyen az Azure-ban felsorolt felső korlátja [korlátok](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits). Ha az aktuális korlát miatt már a maximális számát, a korlát nem növelhető.

## <a name="powershell"></a>PowerShell

A parancsok futtatása a [Azure Cloud rendszerhéj](https://shell.azure.com/powershell), vagy futtassa a PowerShell a számítógépről. Az Azure-felhő rendszerhéj a szabad interaktív rendszerhéjat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha PowerShell a számítógépen futtatja, akkor a *AzureRM* PowerShell-modult, 6.0.1 verzió vagy újabb. Futtatás `Get-Module -ListAvailable AzureRM` a számítógépen, a telepített verzió található. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket. Ha a PowerShell helyben fut, is futtatásához szükséges `Login-AzureRmAccount` bejelentkezni az Azure-bA.

Tekintse meg a korlátozások a [Get-AzureRmNetworkUsage](/powershell/module/azurerm.network/powershell/module/azurerm.network/get-azurermnetworkusage). Az alábbi példa lekérdezi az erőforrásokat, ahol telepíti az USA keleti régiója helyen legalább egy erőforrás használata:

```azurepowershell-interactive
Get-AzureRmNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Kimeneti kap formázott ugyanaz, mint a következő egy példa a kimenetre:

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

Azure parancssori felület (CLI) parancsok használata ebben a cikkben a feladatokat, vagy futtassa a parancsokat a [Azure Cloud rendszerhéj](https://shell.azure.com/bash), vagy a CLI-t a számítógépen való futtatásával. Ez a cikk igényel az Azure parancssori felület 2.0.32 verzió vagy újabb. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Ha a helyben fut az Azure parancssori felület, is futtatásához szükséges `az login` bejelentkezni az Azure-bA.

Tekintse meg a korlátozások a [az hálózati lista-módjait](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). A következő példa az erőforrások felhasználását lekérdezi az USA keleti régiója helyen:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Kimeneti kap formázott ugyanaz, mint a következő egy példa a kimenetre:

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

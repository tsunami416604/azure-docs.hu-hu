---
title: Az Azure-erőforrások használatának ellenőrzése a korlátok kal szemben | Microsoft dokumentumok
description: Ismerje meg, hogyan ellenőrizheti az Azure-erőforrások használatát az Azure-előfizetési korlátokkal szemben.
services: networking
documentationcenter: na
author: KumudD
ms.author: kumud
tags: azure-resource-manager
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.openlocfilehash: cffa5677c5531f3887639c049998523d7d07586a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455562"
---
# <a name="check-resource-usage-against-limits"></a>Erőforrás-használat ellenőrzése korlátok kal szemben

Ebből a cikkből megtudhatja, hogyan láthatja az előfizetésben üzembe helyezett egyes hálózati erőforrástípusok számát, és hogy mik az [előfizetési korlátok.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) Az erőforrás-használat korlátok ellenezése hasznos az aktuális használat nyomon követéséhez és a későbbi használatra való tervezéshez. Használhatja az [Azure Portalon](#azure-portal), [PowerShell,](#powershell)vagy az [Azure CLI](#azure-cli) a használat nyomon követéséhez.

## <a name="azure-portal"></a>Azure portál

1. Jelentkezzen be az Azure [Portalra.](https://portal.azure.com)
2. Az Azure Portal felső, bal sarkában válassza a **Minden szolgáltatás**lehetőséget.
3. Írja be az *Előfizetések* **jelölőnégyzetet** a Szűrő mezőbe. Amikor a keresési eredmények között megjelenik az **Előfizetések** elem, válassza ki.
4. Válassza ki annak az előfizetésnek a nevét, amelynek használati adatait meg szeretné tekinteni.
5. A **BEÁLLÍTÁSOK csoportban**válassza **a Használat + kvóta lehetőséget.**
6. Az alábbi lehetőségek közül választhat:
   - **Erőforrástípusok**: Kiválaszthatja az összes erőforrástípust, vagy kiválaszthatja a megtekinteni kívánt erőforrástípusokat.
   - **Szolgáltatók**: Kiválaszthatja az összes erőforrás-szolgáltatót, vagy **kiválaszthatja a Számítási**, **Hálózati**vagy **Tárolási lehetőséget.**
   - **Helyek**: Kiválaszthatja az összes Azure-helyet, vagy kiválaszthatja az adott helyeket.
   - Kiválaszthatja, hogy az összes erőforrást, vagy csak azokat az erőforrásokat, ahol legalább egy telepítve van.

     Az alábbi képen látható példa az USA keleti részén legalább egy erőforrást üzembe helyezett összes hálózati erőforrást megjeleníti:

       ![Használati adatok megtekintése](./media/check-usage-against-limits/view-usage.png)

     Az oszlopokat az oszlopfejléc kiválasztásával rendezheti. A megjelenített korlátok az előfizetés korlátai. Ha meg kell növelnie az alapértelmezett korlátot, válassza a **Kérelem növelése**lehetőséget, majd töltse ki és küldje el a támogatási kérelmet. Minden erőforrás nak van egy maximális limitje az [Azure-korlátokban.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) Ha az aktuális korlát már elérte a maximális számot, a korlát nem növelhető.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Futtathatja az Azure Cloud [Shellben](https://shell.azure.com/powershell)követett parancsokat, vagy a PowerShell t a számítógépről futtathatja. Az Azure Cloud Shell egy ingyenes interaktív rendszerhéj. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ha a PowerShell t a számítógépről futtatja, szüksége van az Azure PowerShell-modul 1.0.0-s vagy újabb verzióra. Futtassa `Get-Module -ListAvailable Az` a számítógépen, hogy megtalálja a telepített verziót. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, `Login-AzAccount` az Azure-ba való bejelentkezéshez is futnia kell.

Tekintse meg a használatot korlátok között a [Get-AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage)segítségével. A következő példa lekéri az olyan erőforrások használatát, ahol legalább egy erőforrás telepítve van az USA keleti részén:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

A következő példakimenettel megegyező formátumú kimenetet kap:

```output
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure CLI

Ha az Azure parancssori felület (CLI) parancsokkal hajthatja végre a cikkben szereplő feladatokat, futtassa a parancsokat az [Azure Cloud Shellben,](https://shell.azure.com/bash)vagy a CLI futtatásával a számítógépről. Ez a cikk az Azure CLI 2.0.32-es vagy újabb verzióját igényli. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. Ha az Azure CLI-t helyileg futtatja, `az login` az Azure-ba való bejelentkezéshez is futnia kell.

Tekintse meg a használat korlátok között [az az hálózati lista-használat](/cli/azure/network?view=azure-cli-latest#az-network-list-usages). A következő példa az USA keleti részén lévő erőforrások használatát kapja meg:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

A következő példakimenettel megegyező formátumú kimenetet kap:

```output
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

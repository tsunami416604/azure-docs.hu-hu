---
title: A Network Watcher-bővítmény frissítése a legújabb verzióra
description: Ismerje meg, hogyan frissítheti az Azure Network Watcher bővítményt a legújabb verzióra.
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: e367c348364d03cec6914c99e7ff112803fc58f6
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132431"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>A Network Watcher-bővítmény frissítése a legújabb verzióra

## <a name="overview"></a>Áttekintés

Az [azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy hálózati teljesítmény-figyelési, diagnosztikai és elemzési szolgáltatás, amely az Azure-hálózatokat figyeli. A Network Watcher ügynök virtuálisgép-bővítménye követelmény a hálózati forgalom igény szerinti rögzítése, valamint az Azure-beli virtuális gépeken futó egyéb speciális funkciók használata. A Network Watcher bővítményt olyan szolgáltatások használják, mint a kapcsolat figyelője, a kapcsolat figyelője (előzetes verzió), a kapcsolati hibák és a csomagok rögzítése.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy a Network Watcher bővítmény telepítve van a virtuális gépen.

## <a name="latest-version"></a>Legújabb verzió

Jelenleg a Network Watcher bővítmény legújabb verziója érhető el `1.4.1654.1` .

## <a name="update-your-extension"></a>Bővítmény frissítése

A bővítmény frissítéséhez ismernie kell a bővítmény verzióját.

### <a name="check-your-extension-version"></a>A bővítmény verziójának keresése

A bővítmény verzióját a Azure Portal, az Azure CLI vagy a PowerShell használatával tekintheti meg.

#### <a name="usetheazureportal"></a>A Azure Portal használata

1. Lépjen a virtuális gép **Extensions (bővítmények** ) paneljére a Azure Portal.
1. A részletek ablaktábla megjelenítéséhez válassza a **AzureNetworkWatcher** bővítményt.
1. Keresse meg a verziószámot a **verzió** mezőben.  

#### <a name="use-the-azure-cli"></a>Az Azure CLI használata

Futtassa az alábbi parancsot egy Azure CLI-parancssorból:

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Keresse meg a AzureNetworkWatcher bővítményt a kimenetben. Azonosítsa a verziószámot a kimenet "TypeHandlerVersion" mezőjében.  

#### <a name="usepowershell"></a>A PowerShell használata

Futtassa a következő parancsokat egy PowerShell-parancssorból:

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Keresse meg a AzureNetworkWatcher bővítményt a kimenetben. Azonosítsa a verziószámot a kimenet "TypeHandlerVersion" mezőjében.

### <a name="update-your-extension"></a>Bővítmény frissítése

Ha a verziója korábbi, mint `1.4.1654.1` a jelenlegi legújabb verziója, frissítse a bővítményt az alábbi lehetőségek bármelyikével.

#### <a name="option-1-use-powershell"></a>1. lehetőség: a PowerShell használata

Futtassa a következő parancsokat:

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```

#### <a name="option-2-use-the-azure-cli"></a>2. lehetőség: az Azure CLI használata

A frissítés kényszerítése.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Ha ez nem működik, távolítsa el, majd telepítse újra a bővítményt, és kövesse az alábbi lépéseket a legújabb verzió automatikus hozzáadásához.

Távolítsa el a bővítményt.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Telepítse újra a bővítményt.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

#### <a name="option-3-reboot-your-vms"></a>3. lehetőség: a virtuális gépek újraindítása

Ha az automatikus frissítés értéke TRUE (igaz) értékre van állítva a Network Watcher-bővítmény esetében, indítsa újra a virtuális gép telepítését a legújabb bővítményre.

## <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, tekintse meg a [Linux](./network-watcher-linux.md) vagy [Windows rendszerhez](./network-watcher-windows.md)készült Network Watcher-bővítmény dokumentációját. Az [MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/)is kapcsolatba léphet az Azure-szakértőkkel. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése**lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).
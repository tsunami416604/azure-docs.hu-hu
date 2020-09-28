---
title: A Network Watcher-bővítmény frissítése a legújabb verzióra
description: Megtudhatja, hogyan frissítheti Network Watcher bővítményt a legújabb verzióra
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
ms.openlocfilehash: c386685d63894472623ffc4392a529541a91391c
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410341"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>A Network Watcher bővítmény frissítése a legújabb verzióra 

## <a name="overview"></a>Áttekintés

Az [azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy hálózati teljesítmény-figyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi az Azure-hálózatok figyelését. A Network Watcher ügynök virtuálisgép-bővítményének követelménye a hálózati forgalom igény szerinti rögzítése, valamint az Azure-beli virtuális gépeken futó egyéb speciális funkciók. A Network Watcher bővítményt olyan funkciók használják, mint a kapcsolat figyelője, a kapcsolat figyelője (előzetes verzió), a kapcsolati hibák és a csomagok rögzítése.   

## <a name="prerequisites"></a>Előfeltételek
Ez a dokumentum azt feltételezi, hogy a Network Watcher bővítmény telepítve van a virtuális gépen, és útmutatást nyújt a legújabb verzióra történő frissítéséhez. 

## <a name="latest-version"></a>Legújabb verzió
Jelenleg a Network Watcher bővítmény legújabb verziója érhető el `1.4.1654.1` .

## <a name="updating-your-extension"></a>Bővítmény frissítése 

### <a name="check-your-extension-version"></a>A bővítmény verziójának keresése  

**A Azure Portal használata**

1. Lépjen a virtuális gép "Extensions" (bővítmények) paneljére a Azure Portal.   
2. A részletek ablaktábla megjelenítéséhez kattintson az "AzureNetworkWatcher" kiterjesztésre.  
3. Keresse meg a verziószámot a Version (verzió) mezőben.  

**Az Azure CLI használata** Futtassa az alábbi parancsot egy Azure CLI-parancssorból.   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Keresse meg a AzureNetworkWatcher bővítményt a kimenetben, és azonosítsa a verziószámot a kimenet "TypeHandlerVersion" mezőjében.  


**A PowerShell használata** Futtassa a következő parancsokat egy PowerShell-parancssorból:   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Keresse meg a AzureNetworkWatcher bővítményt a kimenetben, és azonosítsa a verziószámot a kimenet "TypeHandlerVersion" mezőjében.   


### <a name="update-your-extension"></a>Bővítmény frissítése

Ha a verziószáma alacsonyabb, mint `1.4.1654.1` (az aktuális legújabb verzió), frissítse a bővítményt az alábbi lehetőségek bármelyikével. 

**1. lehetőség: a PowerShell használata**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**2. lehetőség: az Azure CLI használata**  

Frissítés kényszerítése 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Ha ez nem működik. Távolítsa el, majd telepítse újra a bővítményt az alábbi lépések segítségével. Ekkor a rendszer automatikusan hozzáadja a legújabb verziót. 

A bővítmény eltávolítása 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

A bővítmény újbóli telepítése

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**3. lehetőség: a virtuális gépek újraindítása**

Ha az automatikus frissítés beállítása true (igaz) értékre van állítva a NetworkWatcher-bővítménynél. A virtuális gép újraindítása a legújabb bővítményt telepíti.


## <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, tekintse meg a Network Watcher bővítmény dokumentációját ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux), [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)), vagy lépjen kapcsolatba az Azure-szakértőkkel az [MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

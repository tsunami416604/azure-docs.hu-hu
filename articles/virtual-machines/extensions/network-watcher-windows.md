---
title: Azure Network Watcher Agent virtuálisgép-bővítmény a Windowshoz
description: Telepítse a Network Watcher Agent ügynököt a Windows rendszerű virtuális gépen a virtuálisgép-bővítmény használatával.
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 998e160edce25b9d466a1db090abcefeb7870172
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073689"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Network Watcher Agent virtuálisgép-bővítmény a Windowshoz

## <a name="overview"></a>Áttekintés

Az [azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy hálózati teljesítmény-figyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi az Azure-hálózatok figyelését. A Network Watcher ügynök virtuálisgép-bővítményének követelménye a hálózati forgalom igény szerinti rögzítése, valamint az Azure-beli virtuális gépeken futó egyéb speciális funkciók.


Ez a dokumentum részletesen ismerteti a Windows rendszerhez készült Network Watcher ügynök virtuálisgép-bővítményének támogatott platformokat és telepítési lehetőségeit. Az ügynök telepítése nem zavarja vagy nem igényli a virtuális gép újraindítását. A bővítményt a telepített virtuális gépekre is telepítheti. Ha a virtuális gépet egy Azure-szolgáltatás telepíti, a szolgáltatás dokumentációjában ellenőrizze, hogy engedélyezi-e a bővítmények telepítését a virtuális gépen.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Windows rendszerhez készült Network Watcher-ügynök bővítmény futtatható Windows Server 2008 R2, 2012, 2012 R2 és 2016 kiadásokkal. A nano Server jelenleg nem támogatott.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Network Watcher ügynök egyes funkciói megkövetelik, hogy a célként megadott virtuális gép csatlakozni lehessen az internethez. A kimenő kapcsolatok létrehozásának lehetősége nélkül a Network Watcher ügynök nem fogja tudni feltölteni a csomagokat a Storage-fiókjába. További részletekért tekintse meg a [Network Watcher dokumentációját](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Network Watcher ügynök bővítmény sémáját jeleníti meg. A bővítmény nem igényli, és nem támogatja a felhasználó által megadott beállításokat, és az alapértelmezett konfigurációra támaszkodik.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítmények Azure Resource Manager-sablonokkal helyezhetők üzembe. A Azure Resource Manager sablon előző szakaszában részletes JSON-sémát használva futtathatja az Network Watcher Agent bővítményt egy Azure Resource Manager sablon központi telepítése során.

## <a name="powershell-deployment"></a>PowerShell-telepítés

A `Set-AzVMExtension` parancs használatával telepítse a Network Watcher Agent virtuálisgép-bővítményt egy meglévő virtuális gépre:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshooting"></a>Hibaelhárítás

A bővítmények telepítésének állapotáról a Azure Portal és a PowerShellből kérhet le információkat. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot a Azure PowerShell modul használatával:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

A bővítmény-végrehajtás kimenete a következő könyvtárban található fájlokra van naplózva:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, tekintse meg a Network Watcher felhasználói útmutató dokumentációját, vagy lépjen kapcsolatba az Azure-szakértőkkel az [MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

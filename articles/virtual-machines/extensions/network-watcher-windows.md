---
title: Az Azure Network Watcher ügynök Windows rendszerhez készült virtuális gépi bővítménye
description: Telepítse a Network Watcher Agent-et a Windows virtuális gépen egy virtuálisgép-bővítmény használatával.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: mimckitt
ms.openlocfilehash: f226e240a59b33c2913919495410b1a4923b4902
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261669"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>A Network Watcher Agent virtuálisgép-bővítménye Windows rendszerhez

## <a name="overview"></a>Áttekintés

[Az Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy hálózati teljesítményfigyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi az Azure-hálózatok figyelését. A Network Watcher Agent virtuálisgép-bővítmény igény szerinti hálózati forgalom és az Azure virtuális gépek egyéb speciális funkcióinak rögzítésére vonatkozó követelmény.


Ez a dokumentum részletezi a Windows Network Watcher Agent virtuálisgép-bővítmény támogatott platformjait és telepítési lehetőségeit. Az ügynök telepítése nem zavarja meg, és nem igényel újraindítást a virtuális gép. A bővítmény üzembe helyezhető virtuális gépekre. Ha a virtuális gépet egy Azure-szolgáltatás telepíti, ellenőrizze a szolgáltatás dokumentációjában, hogy megállapítsa, engedélyezi-e a bővítmények telepítését a virtuális gépben.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Windows Network Watcher Agent bővítménye windows Server 2008 R2, 2012, 2012 R2 és 2016 kiadásokon is futtatható. A Nano Server jelenleg nem támogatott.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Network Watcher Agent néhány funkciója megköveteli, hogy a célvirtuális gép csatlakozzon az internethez. A kimenő kapcsolatok létrehozása nélkül a Network Watcher Agent nem tudja feltölteni a csomagrögzítéseket a tárfiókba. További részletekért tekintse meg a [Hálózatfigyelő dokumentációját.](../../network-watcher/network-watcher-monitoring-overview.md)

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Network Watcher Agent bővítmény sémáját jeleníti meg. A bővítmény nem igényel, és nem is támogatja a felhasználó által megadott beállításokat, és az alapértelmezett konfigurációra támaszkodik.

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

### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték / Példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| közzétevő | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Resource Manager-sablonokkal üzembe helyezheti az Azure VM-bővítményeket. Az Azure Resource Manager-sablon előző szakaszában részletezett JSON-séma használatával futtathatja a Network Watcher Agent bővítményt az Azure Resource Manager-sablon üzembe helyezése során.

## <a name="powershell-deployment"></a>PowerShell-telepítés

A `Set-AzVMExtension` paranccsal telepítheti a Network Watcher Agent virtuálisgép-bővítményt egy meglévő virtuális gépre:

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

A bővítmény-üzembe helyezések állapotára vonatkozó adatokat az Azure Portalról és a PowerShellből is lekérheti. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure PowerShell-modul használatával:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

A bővítmény-végrehajtási kimenet a következő könyvtárban található fájlokba kerül:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, tekintse meg a Network Watcher felhasználói útmutató dokumentációját, vagy lépjen kapcsolatba az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.

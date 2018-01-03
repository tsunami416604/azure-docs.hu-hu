---
title: "Windows Azure hálózati figyelő ügynök virtuális gép bővítmény |} Microsoft Docs"
description: "A Windows virtuális gépet egy virtuálisgép-bővítmény használatával a hálózati figyelő-ügynök telepítéséhez."
services: virtual-machines-windows
documentationcenter: 
author: dennisg
manager: amku
editor: 
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 68855e0070916dc672914fbc8ca3587a5d3c25f6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Windows hálózati figyelő ügynök virtuálisgép-bővítmény

## <a name="overview"></a>Áttekintés

[Az Azure hálózati figyelőt](../../network-watcher/network-watcher-monitoring-overview.md) hálózati teljesítmény figyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi, hogy az Azure-hálózatok figyelése. A hálózati figyelő ügynök virtuálisgép-bővítmény feltétele az igény szerinti hálózati forgalmat, és egyéb speciális funkció az Azure virtuális gépeken.


Ez a dokumentum részletesen a támogatott platformokról és a Windows hálózati figyelő ügynök virtuálisgép-bővítmény vonatkozó telepítési lehetőségeket.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A hálózati figyelő ügynök kiterjesztése a Windows is futtathatók a Windows Server 2008 R2, 2012, 2012 R2 és 2016 kiadását. A Nano Server nem támogatott.

### <a name="internet-connectivity"></a>Internetkapcsolat

A hálózati figyelő ügynök funkciók némelyike megköveteli, hogy a cél virtuális gép kapcsolódnia kell az internetre. Kimenő kapcsolatok létesítéséhez képessége nélkül a hálózati figyelő ügynök nem lesz tölthet fel csomag rögzíti a tárfiókhoz. További részletekért lásd: a [hálózati figyelőt dokumentáció](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>A séma kiterjesztése

A következő JSON jeleníti meg a hálózati figyelő ügynök bővítmény sémáját. A bővítmény sem van szüksége, és nem támogatja, a megadott felhasználó által megadott beállításokat és az alapértelmezett beállításon támaszkodik.

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

### <a name="property-values"></a>A tulajdonság értékek

| Name (Név) | Érték / – példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Közzétevő | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Sablonalapú telepítés

Azure virtuális gép bővítményei az Azure Resource Manager-sablonok telepítése. A részletes leírást talál az előző szakaszban az Azure Resource Manager-sablon JSON-séma segítségével a hálózati figyelő ügynök bővítmény futtatása az Azure Resource Manager sablon üzembe helyezése során.

## <a name="powershell-deployment"></a>PowerShell telepítése

Használja a `Set-AzureRmVMExtension` parancs használatával a hálózati figyelő ügynök virtuálisgép-bővítmény telepítése egy meglévő virtuális gépre:

```powershell
Set-AzureRmVMExtension `
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

Bővítmény központi telepítések állapotára vonatkozó adatainak lekérése az Azure portál és a PowerShell. Egy adott virtuális gép bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure PowerShell-modullal:

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

A következő könyvtárban található fájlok kerül a bővítmény végrehajtás kimenetének:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, tekintse meg a hálózati figyelő felhasználói útmutató dokumentációját, vagy lépjen kapcsolatba az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/en-us/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/en-us/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/en-us/support/faq/).

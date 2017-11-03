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
ms.openlocfilehash: b8d6a998bc86337b286a3434f44f762cca9b7e68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Windows hálózati figyelő ügynök virtuálisgép-bővítmény

## <a name="overview"></a>Áttekintés

[Az Azure hálózati figyelőt](https://review.docs.microsoft.com/en-us/azure/network-watcher/) hálózati teljesítmény figyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi az Azure-hálózatok figyelését. A hálózati figyelő ügynök virtuálisgép-bővítményt az egyes hálózati figyelőt szolgáltatásokat az Azure virtuális gépeken működik. Ez magában foglalja, igény szerint és egyéb speciális funkciók a hálózati forgalom rögzítése.

Ez a dokumentum részletesen a támogatott platformokról és a Windows hálózati figyelő ügynök virtuálisgép-bővítmény vonatkozó telepítési lehetőségeket.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A hálózati figyelő ügynök kiterjesztése a Windows is futtathatók a Windows Server 2008 R2, 2012, 2012 R2 és 2016 kiadását. Vegye figyelembe, hogy a Nano Server jelenleg nem támogatott.

### <a name="internet-connectivity"></a>Internetkapcsolat

A hálózati figyelő ügynök funkciók némelyike megköveteli, hogy a cél virtuális gép kapcsolódnia kell az internetre. Kimenő kapcsolatokat képessége nélkül egyes hálózati figyelő ügynök szolgáltatásokat lehet, hogy hibás működését, vagy már nem érhető el. További részletekért lásd: a [hálózati figyelőt dokumentáció](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>A séma kiterjesztése

A következő JSON jeleníti meg a hálózati figyelő ügynök bővítmény sémáját. A bővítmény sem szükséges és nem támogatja a megadott felhasználó által megadott beállításokat jelenleg és az alapértelmezett beállításon támaszkodik.

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

| Név | Érték / – példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| Közzétevő | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Az előző szakaszban ismertetett JSON-séma segítségével az Azure Resource Manager-sablonok az Azure Resource Manager sablon telepítése során a hálózati figyelő ügynök bővítmény futtatása.

## <a name="powershell-deployment"></a>PowerShell telepítése

A `Set-AzureRmVMExtension` parancs segítségével a hálózati figyelő ügynök virtuálisgép-bővítmény telepítése egy meglévő virtuális gépre.

```powershell
Set-AzureRmVMExtension -ResourceGroupName "myResourceGroup1" `
                       -Location "WestUS" `
                       -VMName "myVM1" `
                       -Name "networkWatcherAgent" `
                       -Publisher "Microsoft.Azure.NetworkWatcher" `
                       -Type "NetworkWatcherAgentWindows" `
                       -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshooting"></a>Hibaelhárítás

Bővítmény központi telepítések állapotára vonatkozó lehet adatokat beolvasni az Azure-portálon, és az Azure PowerShell modul segítségével. A megadott virtuális gépek bővítmények központi telepítési állapotának megtekintéséhez a következő parancsot az Azure PowerShell modullal.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

A következő könyvtárban található fájlok kerül a bővítmény végrehajtás kimenetének:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, tekintse meg a hálózati figyelő felhasználói útmutató dokumentációját, vagy lépjen kapcsolatba az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/en-us/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/en-us/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/en-us/support/faq/).

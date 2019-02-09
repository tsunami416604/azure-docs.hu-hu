---
title: Windows Azure Network Watcher-ügynök virtuális gépi bővítmény |} A Microsoft Docs
description: Windows virtuális gép, virtuálisgép-bővítmények használatával a Network Watcher-ügynök telepítése.
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 6e02f5a5b42da9c99a08782903cdc05ee32ec9d4
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55976919"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Network Watcher-ügynök virtuálisgép-bővítmény a Windows

## <a name="overview"></a>Áttekintés

[Az Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) egy hálózati teljesítmény figyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi Azure-hálózatok figyelését. A Network Watcher-ügynök virtuálisgép-bővítmény akkor igény szerinti hálózati forgalmat, és más speciális funkciókat az Azure-beli virtuális gépeken rögzítéséhez szükséges.


Ez a dokumentum részletesen, a támogatott platformokról és az üzembe helyezési lehetőségeit a Network Watcher-ügynök virtuálisgép-bővítmény Windows. Az ügynök telepítésének nem zavarja, vagy a virtuális gép újraindítása szükséges. A bővítmény üzembe helyezhető üzembe helyezett virtuális gépek. Ha a virtuális gép telepítve van az Azure-szolgáltatások által, dokumentációjában a szolgáltatás határozza meg, függetlenül attól, lehetővé teszi a bővítmények telepítése a virtuális gép számára.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Network Watcher-ügynök bővítmény esetében a Windows is futtatható a Windows Server 2008 R2, 2012, 2012 R2 és 2016-kiadások. A nano Server jelenleg nem támogatott.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Network Watcher-ügynök funkciók némelyike megköveteli, hogy a céloldali virtuális gép csatlakozni az internethez. Kimenő kapcsolatok létesítéséhez nélkül a Network Watcher-ügynök nem tud csomagrögzítés feltölteni a tárfiókba. További részletekért tekintse meg a [Network Watcher dokumentációja](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Network Watcher-ügynök bővítmény sémáját jeleníti meg. A bővítmény sem igényel, és nem támogatja, minden olyan felhasználó által megadott beállításokat, és az alapértelmezett konfigurációban támaszkodik.

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

Azure-beli Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepítheti. Az előző szakaszban az Azure Resource Manager-sablon részletes JSON-séma használatával futtassa a Network Watcher-ügynök bővítményt egy Azure Resource Manager-sablon telepítése során.

## <a name="powershell-deployment"></a>PowerShell környezetben végzett telepítés

Használja a `Set-AzVMExtension` parancsot a Network Watcher-ügynök virtuálisgép-bővítmény egy meglévő virtuális gépek üzembe helyezéséhez:

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

Az Azure portal, PowerShell bővítmény központi telepítések állapotával kapcsolatos adatokat lehet lekérdezni. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure PowerShell modullal:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő könyvtárban található fájlok:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, tekintse meg a Network Watcher felhasználói útmutató dokumentációját, vagy lépjen kapcsolatba az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

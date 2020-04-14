---
title: Azure Network Watcher Agent virtuálisgép-bővítmény Linuxhoz
description: Telepítse a Network Watcher Agent linuxos virtuális gépen egy virtuális gép bővítmény használatával.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: mimckitt
ms.openlocfilehash: 476241ad3d4077f2da0c513e3c989218701232ba
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255753"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>A Network Watcher ügynök Linux rendszerhez készült virtuális gépi bővítménye

## <a name="overview"></a>Áttekintés

[Az Azure Network Watcher](/azure/network-watcher/) egy hálózati teljesítményfigyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi az Azure-hálózatok figyelését. A Network Watcher Agent virtuális gép (VM) bővítmény az Azure virtuális gépek egyes Hálózati figyelő funkcióinak követelménye, például a hálózati forgalom igény szerinti rögzítése és más speciális funkciók.

Ez a cikk részletezi a támogatott platformok és a linuxos Network Watcher Agent VM-bővítmény telepítési beállításait. Az ügynök telepítése nem zavarja meg, vagy a virtuális gép újraindítását igényli. A bővítmény üzembe helyezhető virtuális gépekre. Ha a virtuális gépet egy Azure-szolgáltatás telepíti, ellenőrizze a szolgáltatás dokumentációjában, hogy megállapítsa, engedélyezi-e a bővítmények telepítését a virtuális gépben.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Network Watcher Agent bővítmény a következő Linux-disztribúciókhoz konfigurálható:

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 és 8 |
| Red Hat | 6 és 7 |
| Oracle Linux | 6.8+ és 7 |
| SUSE Linux Enterprise Server | 11 és 12 |
| OpenSUSE ugrás | 42.3+ |
| CentOS | 6.5+ és 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>Internetkapcsolat

A Network Watcher Agent néhány funkciója megköveteli, hogy a virtuális gép csatlakozik az internethez. A kimenő kapcsolatok létrehozása nélkül a Network Watcher Agent egyes funkciói hibásan működhetnek, vagy elérhetetlenné válhatnak. A Network Watcher ügynökre vonatkozó funkcióiról a[Hálózatfigyelő dokumentációjában](/azure/network-watcher/)talál további információt.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Network Watcher Agent bővítmény sémáját jeleníti meg. A bővítmény nem igényel, vagy támogatja a felhasználó által megadott beállításokat. A bővítmény az alapértelmezett konfigurációra támaszkodik.

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
        "type": "NetworkWatcherAgentLinux",
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
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure-erőforrás-kezelő sablonnal üzembe helyezheti az Azure Virtuálisgép-bővítményeket. A Network Watcher Agent bővítmény központi telepítéséhez használja az előző jsonsémát a sablonban.

## <a name="azure-classic-cli-deployment"></a>Az Azure klasszikus CLI-telepítése

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

A következő példa telepíti a Network Watcher Agent virtuálisgép-bővítményt egy meglévő virtuális gépre, amely a klasszikus üzembe helyezési modellen keresztül van telepítve:

```console
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Az Azure CLI üzembe helyezése

A következő példa telepíti a Network Watcher Agent virtuálisgép-bővítményt egy meglévő virtuális gépre, amelyet az Erőforrás-kezelő n keresztül telepítenek:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshooting"></a>Hibaelhárítás

Az Azure Portal vagy az Azure CLI használatával lekérheti a bővítmény-üzembe helyezések állapotát.

A következő példa az Azure CLI használatával üzembe helyezett virtuális gép NetworkWatcherAgentLinux-bővítményének üzembe helyezési állapotát mutatja be:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, olvassa el a [Network Watcher dokumentációját,](/azure/network-watcher/)vagy lépjen kapcsolatba az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.** Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)-ben talál további információt.

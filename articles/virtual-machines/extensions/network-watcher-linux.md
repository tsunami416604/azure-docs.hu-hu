---
title: Azure Network Watcher Agent virtuálisgép-bővítmény Linux rendszerhez
description: Telepítse a Network Watcher-ügynököt a Linux rendszerű virtuális gépen a virtuálisgép-bővítmény használatával.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 69d2bfe4576a9350e905fc10f3d7617619e6284a
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915486"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Network Watcher Agent virtuálisgép-bővítmény Linux rendszerhez

## <a name="overview"></a>Áttekintés

Az [azure Network Watcher](/azure/network-watcher/) egy hálózati teljesítmény-figyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi az Azure-hálózatok figyelését. Az Network Watcher Agent virtuálisgép-bővítmény az Azure-beli virtuális gépek egyes Network Watcher funkcióinak, például az igény szerinti hálózati forgalom rögzítésének és más speciális funkcióknak a követelménye.

Ez a cikk a Linux rendszerhez készült Network Watcher Agent virtuálisgép-bővítmény támogatott platformait és telepítési lehetőségeit ismerteti. Az ügynök telepítése nem zavarja vagy nem igényli a virtuális gép újraindítását. A bővítményt a telepített virtuális gépekre is telepítheti. Ha a virtuális gépet egy Azure-szolgáltatás telepíti, a szolgáltatás dokumentációjában ellenőrizze, hogy engedélyezi-e a bővítmények telepítését a virtuális gépen.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Network Watcher ügynök bővítmény a következő Linux-disztribúciók esetében konfigurálható:

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 és 8 |
| Red Hat | 6 és 7 |
| Oracle Linux | 6.8+ és 7 |
| SUSE Linux Enterprise Server | 11 és 12 |
| OpenSUSE Leap | 42.3 + |
| CentOS | 6.5+ és 7 |
| CoreOS | 899.17.0 + |


### <a name="internet-connectivity"></a>Internetkapcsolat

A Network Watcher-ügynök néhány funkciója megköveteli, hogy a virtuális gép csatlakoztatva legyen az internethez. A kimenő kapcsolatok létrehozása nélkül előfordulhat, hogy a Network Watcher ügynök egyes funkciói meghibásodnak vagy elérhetetlenné válnak. Az ügynököt igénylő Network Watcher funkcióival kapcsolatos további információkért tekintse meg a[Network Watcher dokumentációját](/azure/network-watcher/).

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Network Watcher ügynök bővítmény sémáját jeleníti meg. A bővítmény nem igényli vagy nem támogatja a felhasználó által megadott beállításokat. A bővítmény az alapértelmezett konfigurációra támaszkodik.

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

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure virtuálisgép-bővítményeket Azure Resource Manager sablonnal is üzembe helyezheti. A Network Watcher Agent bővítmény üzembe helyezéséhez használja a sablon korábbi JSON-sémáját.

## <a name="azure-classic-cli-deployment"></a>Klasszikus Azure CLI üzembe helyezés

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Az alábbi példa telepíti a Network Watcher Agent virtuálisgép-bővítményt a klasszikus üzemi modellel telepített meglévő virtuális gépre:

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az alábbi példa telepíti a Network Watcher Agent virtuálisgép-bővítményt a Resource Managerrel üzembe helyezett meglévő virtuális gépre:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshooting"></a>Hibakeresés

A bővítmények állapotával kapcsolatos információkat a Azure Portal vagy az Azure CLI használatával kérheti le.

Az alábbi példa a Resource Managerrel üzembe helyezett virtuális gépek NetworkWatcherAgentLinux-bővítményének telepítési állapotát mutatja be az Azure CLI használatával:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, tekintse meg az [Network Watcher dokumentációját](/azure/network-watcher/), vagy lépjen kapcsolatba az Azure-szakértőkkel az [MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

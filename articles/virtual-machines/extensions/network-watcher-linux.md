---
title: Az Azure Network Watcher-ügynök virtuálisgép-bővítmény linuxhoz |} A Microsoft Docs
description: A Network Watcher-ügynök virtuálisgép-bővítmények használata Linux rendszerű virtuális gépre telepíteni.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 22b18f77b3d997cdba7b60b53f1968b516701cc1
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055526"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Hálózati figyelő ügynök virtuálisgép-bővítmény linuxhoz

## <a name="overview"></a>Áttekintés

[Az Azure Network Watcher](/azure/network-watcher/) egy hálózati teljesítmény figyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi az Azure-hálózatok figyelését. A Network Watcher-ügynök (VM) virtuálisgép-bővítmény esetében a követelmény a Network Watcher funkcióit az Azure virtuális gépekhez, például igény szerint, és más speciális funkciókat a hálózati forgalom rögzítése.

Ez a cikk részletesen, a támogatott platformokról és az üzembe helyezési lehetőségeit a Network Watcher-ügynök VM-bővítmény linuxhoz. Az ügynök telepítésének nem zavarja, vagy a virtuális gép újraindítása szükséges. A bővítmény üzembe helyezhető üzembe helyezett virtuális gépek. Ha a virtuális gép telepítve van az Azure-szolgáltatások által, dokumentációjában a szolgáltatás határozza meg, függetlenül attól, lehetővé teszi a bővítmények telepítése a virtuális gép számára.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Network Watcher-ügynök bővítményt a következő Linux-disztribúciókra vonatkozó konfigurálhatók:

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS és 12.04 LTS |
| Debian | 7. és 8 |
| Red Hat | 6. és 7 |
| Oracle Linux | 6.8 + és 7 |
| SUSE Linux Enterprise Server | 11 – 12 |
| OpenSUSE azt | 42.3 + |
| CentOS | 6.5-ös + és 7 |
| CoreOS | 899.17.0+ |

CoreOS nem támogatott.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Network Watcher-ügynök funkciók némelyike megköveteli, hogy egy virtuális gép csatlakozik az internethez. Anélkül, hogy lehetővé teszi a kimenő kapcsolatokat a Network Watcher-ügynök funkcióit előfordulhat, hogy megfelelően működni, vagy már nem érhető el. A Network Watcher-funkció, amely megköveteli az ügynök kapcsolatos további információkért lásd: a[Network Watcher dokumentációja](/azure/network-watcher/).

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Network Watcher-ügynök bővítmény sémáját jeleníti meg. A bővítmény nem igényelnek, vagy támogatja, a felhasználó által megadott beállításokat. A bővítmény az alapértelmezett konfigurációban támaszkodik.

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

Telepíthet egy Azure Resource Manager-sablon Azure-beli Virtuálisgép-bővítmények. A Network Watcher-ügynök bővítmény üzembe helyezéséhez használja a sablonban az előző json-sémájában.

## <a name="azure-cli-10-deployment"></a>Az Azure CLI 1.0-s üzembe helyezése

A következő példa telepíti a Network Watcher-ügynök VM-bővítmény egy meglévő virtuális Gépet a klasszikus üzemi modellel üzembe helyezett:

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-20-deployment"></a>Az Azure CLI 2.0-s üzembe helyezése

A következő példa telepíti a Network Watcher-ügynök VM-bővítmény Resource Manager használatával telepített meglévő virtuális géphez:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshooting"></a>Hibaelhárítás

Az Azure Portalon vagy Azure CLI használatával bővítmény központi telepítések állapotának adatait kérheti le.

Az alábbi példa bemutatja a bővítmények központi telepítés állapotát az Azure CLI 1.0 használatával a klasszikus üzemi modellel üzembe helyezett virtuális gépek:

```azurecli
azure config mode asm
azure vm extension get myVM1
```
Bővítmény végrehajtás kimenetének a rendszer naplózza a következő könyvtárban található fájlok:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

Az alábbi példa bemutatja a NetworkWatcherAgentLinux bővítmény telepítési állapotát a virtuális gép Resource Managerrel üzembe helyezett, az Azure CLI 2.0 használatával:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, olvassa el a [Network Watcher dokumentációja](/azure/network-watcher/), vagy lépjen kapcsolatba az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatás**. Azure-támogatási használatával kapcsolatos információkért lásd: a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

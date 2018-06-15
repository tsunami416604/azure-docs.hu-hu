---
title: Az Azure hálózati figyelő ügynök virtuálisgép-bővítmény Linux |} Microsoft Docs
description: A hálózati figyelő ügynök a Linux virtuális gépet egy virtuálisgép-bővítmény telepítése.
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
ms.openlocfilehash: db508e2311602a66a2c252ffaa842f8bfb4f670b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076071"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Hálózati figyelő ügynök virtuálisgép-bővítmény Linux

## <a name="overview"></a>Áttekintés

[Az Azure hálózati figyelőt](/azure/network-watcher/) hálózati teljesítmény figyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi az Azure-hálózatok figyelését. A hálózati figyelő ügynök virtuális gép (VM) kiterjesztés előfeltétele annak, hogy egyes hálózati figyelőt szolgáltatásokat Azure virtuális gépeken, például az igény szerinti és egyéb speciális funkciókat a hálózati forgalom rögzítése.

Ez a cikk a támogatott platformok és a központi telepítési beállításokat a hálózati figyelő ügynök Virtuálisgép-bővítmény Linux részletezi. Az ügynök nem zavarja, vagy újra kell indítani, a virtuális gép.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A hálózati figyelő ügynök kiterjesztés a következő Linux terjesztésekről konfigurálhatók:

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS és 12.04 LTS |
| Debian | 7. és 8 |
| RedHat | 6 és 7 |
| Oracle Linux | 6.8 + és 7 |
| SUSE Linux Enterprise Server | 11 és 12 |
| OpenSUSE termékek | 42.3 + |
| CentOS | 6.5 + és 7 |
| CoreOS | 899.17.0+ |

CoreOS nem támogatott.

### <a name="internet-connectivity"></a>Internetkapcsolat

A hálózati figyelő ügynök funkciók némelyike megköveteli, hogy a virtuális gép csatlakozik az internethez. Kimenő kapcsolatokat képessége nélkül egyes hálózati figyelő ügynök szolgáltatásokat lehet, hogy hibás működését, vagy már nem érhető el. Amely megköveteli az ügynök hálózati figyelőt funkcióival kapcsolatos további információkért lásd: a[hálózati figyelőt dokumentáció](/azure/network-watcher/).

## <a name="extension-schema"></a>Bővítményséma

A következő JSON jeleníti meg a hálózati figyelő ügynök bővítmény sémáját. A bővítmény nem szükséges, vagy támogatja, a felhasználó által megadott beállításokat. A bővítmény támaszkodik az alapértelmezett konfigurációval.

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

### <a name="property-values"></a>A tulajdonság értékek

| Name (Név) | Érték / – példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Azure virtuális gép bővítményei az Azure Resource Manager-sablonok telepítése. A hálózati figyelő ügynök bővítmény telepítéséhez használja az előző json-séma a sablonban.

## <a name="azure-cli-10-deployment"></a>Az Azure CLI 1.0-telepítés

A következő példa telepíti a hálózati figyelő ügynök Virtuálisgép-bővítmény a klasszikus üzembe helyezési modellben telepített meglévő virtuális:

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-20-deployment"></a>Az Azure CLI 2.0-telepítés

A következő példa telepíti a hálózati figyelő ügynök Virtuálisgép-bővítmény a Resource Manager használatával telepített meglévő virtuális:

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshooting"></a>Hibaelhárítás

Bővítmény üzembe helyezése az Azure portálon vagy az Azure CLI állapotára vonatkozó adatok kérheti le.

A következő példa bemutatja a bővítmény telepítési állapotát az Azure CLI 1.0 használata a klasszikus üzembe helyezési modellben telepített virtuális:

```azurecli
azure config mode asm
azure vm extension get myVM1
```
A következő könyvtárban található fájlok kerül a bővítmény végrehajtás kimenetének:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

A következő példa bemutatja a NetworkWatcherAgentLinux bővítmény telepítési állapotát a Resource Manageren keresztül, központilag telepített virtuális gépek az Azure CLI 2.0 használatával:

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, olvassa el a [hálózati figyelőt dokumentáció](/azure/network-watcher/), vagy lépjen kapcsolatba az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **segítségre van szüksége**. Támogatja az Azure használatával kapcsolatos információkért lásd: a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

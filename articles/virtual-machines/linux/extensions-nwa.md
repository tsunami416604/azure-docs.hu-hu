---
title: "Az Azure hálózati figyelő ügynök virtuálisgép-bővítmény Linux |} Microsoft Docs"
description: "A hálózati figyelő ügynök a Linux virtuális gépet egy virtuálisgép-bővítmény telepítése."
services: virtual-machines-linux
documentationcenter: 
author: dennisg
manager: amku
editor: 
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: fd160fc8cfc2e79ffa9492bb0ef4556bec5820d0
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Hálózati figyelő ügynök virtuálisgép-bővítmény Linux

## <a name="overview"></a>Áttekintés

[Az Azure hálózati figyelőt](https://review.docs.microsoft.com/azure/network-watcher/) hálózati teljesítmény figyelési, diagnosztikai és elemzési szolgáltatás, amely lehetővé teszi az Azure-hálózatok figyelését. A hálózati figyelő ügynök virtuálisgép-bővítményt az egyes hálózati figyelőt szolgáltatásokat az Azure virtuális gépeken működik. Ez magában foglalja, igény szerint és egyéb speciális funkciók a hálózati forgalom rögzítése.

Ez a dokumentum részletesen a támogatott platformokról és a Linux hálózati figyelő ügynök virtuálisgép-bővítmény vonatkozó telepítési lehetőségeket.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A hálózati figyelő ügynök bővítmény is futtathatók a a Linux terjesztéseket:

| Disztribúció | Verzió |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS és 12.04 LTS |
| Debian | 7. és 8 |
| RedHat | 6.x, 7.x és |
| Oracle Linux | 7 x |
| SUSE | 11 és 12 |
| OpenSuse | 7.0 |
| CentOS | 7.0 |

Vegye figyelembe, hogy CoreOS jelenleg nem támogatott.

### <a name="internet-connectivity"></a>Internetkapcsolat

A hálózati figyelő ügynök funkciók némelyike megköveteli, hogy a cél virtuális gép kapcsolódnia kell az internetre. Kimenő kapcsolatokat képessége nélkül egyes hálózati figyelő ügynök szolgáltatásokat lehet, hogy hibás működését, vagy már nem érhető el. További részletekért lásd: a [hálózati figyelőt dokumentáció](https://review.docs.microsoft.com/azure/network-watcher/).

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
        "type": "NetworkWatcherAgentLinux",
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
| type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Az előző szakaszban ismertetett JSON-séma segítségével az Azure Resource Manager-sablonok az Azure Resource Manager sablon telepítése során a hálózati figyelő ügynök bővítmény futtatása.

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az Azure CLI segítségével a hálózati figyelő ügynök Virtuálisgép-bővítmény telepítése egy meglévő virtuális gépre.

```azurecli
azure vm extension set myResourceGroup1 myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="troubleshooting-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshooting"></a>Hibaelhárítás

Bővítmény központi telepítések állapotára vonatkozó lehet adatokat beolvasni az Azure-portálon, és az Azure parancssori felület használatával. A megadott virtuális gépek bővítmények központi telepítési állapotának megtekintéséhez a következő parancsot az Azure parancssori felület használatával.

```azurecli
azure vm extension get myResourceGroup1 myVM1
```

A következő könyvtárban található fájlok kerül a bővítmény végrehajtás kimenetének:

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, tekintse meg a hálózati figyelőt dokumentációját, vagy lépjen kapcsolatba az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/en-us/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/en-us/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/en-us/support/faq/).

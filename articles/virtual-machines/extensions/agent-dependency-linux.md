---
title: Figyelőfüggőség linuxos Azure virtuális gépi bővítmény |} A Microsoft Docs
description: Linux rendszerű virtuális gépre az Azure Monitor függőségi ügynök telepítése a virtuális gépi bővítmény használatával.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 5faeebe799bd8cc0ba9a148508ac5b3a6d4b803a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120214"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Az Azure Figyelőfüggőség virtuálisgép-bővítmény linuxhoz

Virtuális gépek térkép funkció az Azure Monitor az adatok lekérése a Microsoft Dependency agent. Az Azure virtuális gép függőségi ügynök virtuálisgép-bővítmény linuxhoz közzétett és a Microsoft támogatja. A bővítmény a függőségi ügynök telepítése Azure-beli virtuális gépeken. Ez a dokumentum részletesen, a támogatott platformok, konfigurációk és Azure virtuális gép függőségi ügynök virtuálisgép-bővítmény linuxhoz üzembe helyezési lehetőségeit.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Az Azure virtuális gép függőségi ügynök-bővítmény linuxhoz is futtatható a felsorolt támogatott operációs rendszerek a [támogatott operációs rendszerek](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) virtuális gépek üzembe helyezés a cikkhez az Azure Monitor szakaszában.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON-beli Linux rendszerű virtuális gépen az Azure virtuális gép függőségi ügynök bővítmény sémáját mutatja. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Azure-beli Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepítheti. Az előző szakaszban az Azure Resource Manager-sablon részletes JSON-sémájában segítségével az Azure virtuális gép függőségi ügynök bővítmény futtathat egy Azure Resource Manager-sablon telepítése során.

A JSON-t egy virtuálisgép-bővítményt a virtuális gép típusú erőforrást is ágyazható. Másik lehetőségként elhelyezhet a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON-fájllal való elhelyezését hatással van az erőforrás nevét, és írja be az értékét. További információkért lásd: [állítsa be a nevét és típusát gyermekerőforrásait](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources).

Az alábbi példa azt feltételezi, hogy a függőségi ügynök bővítményt a virtuális gép típusú erőforrást van beágyazva. Ha a bővítmény erőforrás egymásba ágyazni a JSON kerül a `"resources": []` objektum a virtuális gép.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Ha a JSON-bővítmény a sablonban gyökérmappájában helyez, a az erőforrás neve tartalmaz egy hivatkozást a szülő virtuális gép. A típus a beágyazott konfigurációját tükrözi. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az Azure CLI segítségével a függőségi ügynök Virtuálisgép-bővítmény egy meglévő virtuális gépek üzembe helyezéséhez.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure parancssori felület használatával. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI-vel:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő fájlt:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, lépjen kapcsolatba az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Vagy egy Azure-támogatási esemény fájl is. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatás**. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

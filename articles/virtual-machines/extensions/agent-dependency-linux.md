---
title: Azure Monitor függőségi virtuálisgép-bővítmény linuxos
description: Az Azure Monitor függőségi ügynök linuxos virtuális gépen egy virtuális gép bővítmény használatával üzembe helyezése.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 82f9c5a67cb056752cf8310be3b7c9f0bd2501e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254039"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Azure Monitor függőségi virtuálisgép-bővítmény linuxos

Az Azure Monitor szolgáltatás virtuális gépek leképezése szolgáltatás lekéri az adatokat a Microsoft függőségi ügynök. Az Azure VM-függőségi ügynök virtuálisgép-bővítmény linuxos közzétett és támogatott a Microsoft. A bővítmény telepíti a függőségi ügynök az Azure virtuális gépeken. Ez a dokumentum részletezi a támogatott platformok, konfigurációk és üzembe helyezési lehetőségek az Azure VM-függőségi ügynök virtuálisgép-bővítmény Linuxhoz.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Az Azure VM-függőségi ügynök bővítmény Linux futtatható a támogatott operációs rendszerek az Azure Monitor szolgáltatás virtuális gépek üzembe helyezési cikk támogatott [operációs rendszerek](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) szakaszában felsorolt támogatott operációs rendszerek.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON az Azure VM-függőségi ügynök bővítmény énekét mutatja egy Azure Linux virtuális gépen. 

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

### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték/Példa |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| közzétevő | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Resource Manager-sablonokkal üzembe helyezheti az Azure VM-bővítményeket. Az Azure Resource Manager-sablon előző szakaszában részletezett JSON-séma használatával futtathatja az Azure VM-függőségi ügynök bővítményt az Azure Resource Manager-sablon üzembe helyezése során.

A virtuálisgép-bővítmény JSON-ja beágyazható a virtuálisgép-erőforrásba. Vagy elhelyezheti az Erőforrás-kezelő JSON-sablon gyökér- vagy legfelső szintjén. A JSON elhelyezése hatással van az erőforrás nevének és típusának értékére. További információt a [Név és a gyermekerőforrások típusának beállítása](../../azure-resource-manager/templates/child-resource-name-type.md)című témakörben talál.

A következő példa feltételezi, hogy a függőségi ügynök bővítmény a virtuális gép erőforrásba van ágyazva. A bővítményerőforrás beágyazásakor a JSON a `"resources": []` virtuális gép objektumába kerül.


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

Amikor a JSON kiterjesztést a sablon gyökerére helyezi, az erőforrás neve tartalmaz egy hivatkozást a szülő virtuális gépre. A típus a beágyazott konfigurációt tükrözi. 

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

## <a name="azure-cli-deployment"></a>Az Azure CLI üzembe helyezése

Az Azure CLI segítségével üzembe helyezheti a függőségi ügynök virtuális gép bővítményét egy meglévő virtuális gépre.  

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

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról és az Azure CLI használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával:

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A bővítmény-végrehajtási kimenet a következő fájlba kerül:

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, forduljon az Azure szakértőihez az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Vagy benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.** Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.

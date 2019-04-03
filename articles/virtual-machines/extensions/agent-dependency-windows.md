---
title: Figyelőfüggőség a Windows Azure virtuális gépi bővítmény |} A Microsoft Docs
description: Telepítse az Azure Monitor függőségi ügynököt Windows virtuális gép, virtuálisgép-bővítmények használatával.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: cd10c503c6e65f68d063deb5f8a537fc9f3c9f0f
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58846326"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Figyelőfüggőség a Windows Azure virtuális gépi bővítmény

Virtuális gépek térkép funkció az Azure Monitor az adatok lekérése a Microsoft Dependency agent. A Windows Azure virtuális gép függőségi ügynök virtuális gépi bővítmény közzétett és a Microsoft támogatja. A bővítmény a függőségi ügynök telepítése Azure-beli virtuális gépeken. Ez a dokumentum részletesen, a támogatott platformok, a konfigurációk és a Windows Azure virtuális gép függőségi ügynök virtuálisgép-bővítmény az üzembe helyezési lehetőségeit.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="operating-system"></a>Operációs rendszer

A Windows Azure virtuális gép függőségi ügynök bővítményt is futtatható a felsorolt támogatott operációs rendszerek a [támogatott operációs rendszerek](../../azure-monitor/insights/vminsights-onboard.md#supported-operating-systems) virtuális gépek üzembe helyezés a cikkhez az Azure Monitor szakaszában.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON Azure Windows virtuális gép az Azure virtuális gép függőségi ügynök bővítmény sémáját jeleníti meg. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
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
        "type": "DependencyAgentWindows",
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
| type | DependencyAgentWindows |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Az előző szakaszban részletes JSON-sémájában az Azure Resource Manager-sablon használható az Azure virtuális gép függőségi ügynök bővítmény futtatásához az Azure Resource Manager-sablon telepítése során. 

A JSON-t egy virtuálisgép-bővítményt a virtuális gép típusú erőforrást belülre, vagy elhelyezve, a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON-fájllal való elhelyezését hatással van az erőforrás nevét, és írja be az értékét. További információkért lásd: [állítsa be a nevét és típusát gyermekerőforrásait](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

Az alábbi példa azt feltételezi, hogy a függőségi ügynök bővítményt a virtuális gép típusú erőforrást van beágyazva. A bővítmény erőforrás beágyazása, ha a JSON az kerül a `"resources": []` objektum a virtuális gép.


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
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

Helyezi el a JSON-bővítmény a sablonban gyökérmappájában, amikor az erőforrás neve a szülő virtuális gép egy hivatkozást tartalmaz, és a típus a beágyazott konfigurációját tükrözi. 

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
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell környezetben végzett telepítés

A `Set-AzVMExtension` parancs a függőségi ügynök virtuálisgép-bővítmény egy meglévő virtuális gépek üzembe helyezéséhez használható. A parancs futtatása előtt a nyilvános és privát konfigurációk kell egy PowerShell kivonattábla kell tárolni. 

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure PowerShell-modul segítségével. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure PowerShell modullal.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő könyvtárban található fájlok:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

---
title: Figyelőfüggőség a Windows Azure virtuális gépi bővítmény |} A Microsoft Docs
description: Windows virtuális gépen az Azure Monitor függőségi ügynök telepítése a virtuális gépi bővítmény használatával.
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
ms.openlocfilehash: 34dd872db199a4c10e9f321457188b7f7642944d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120225"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Figyelőfüggőség a Windows Azure virtuális gépi bővítmény

Virtuális gépek térkép funkció az Azure Monitor az adatok lekérése a Microsoft Dependency agent. A Windows Azure virtuális gép függőségi ügynök virtuális gépi bővítmény közzétett és a Microsoft támogatja. A bővítmény a függőségi ügynök telepítése Azure-beli virtuális gépeken. Ez a dokumentum részletesen, a támogatott platformok, a konfigurációk és a Windows Azure virtuális gép függőségi ügynök virtuálisgép-bővítmény az üzembe helyezési lehetőségeit.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="operating-system"></a>Operációs rendszer

A Windows Azure virtuális gép függőségi ügynök bővítményt is futtatható a felsorolt támogatott operációs rendszerek a [támogatott operációs rendszerek](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) virtuális gépek üzembe helyezés a cikkhez az Azure Monitor szakaszában.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON-beli Windows virtuális gépen az Azure virtuális gép függőségi ügynök bővítmény sémáját mutatja.

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

Az Azure Resource Manager-sablonok az Azure Virtuálisgép-bővítmények telepítheti. Az előző szakaszban az Azure Resource Manager-sablon részletes JSON-sémájában segítségével az Azure virtuális gép függőségi ügynök bővítmény futtathat egy Azure Resource Manager-sablon telepítése során.

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
        "type": "DependencyAgentWindows",
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
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell környezetben végzett telepítés

Használhatja a `Set-AzVMExtension` parancsot a függőségi ügynök virtuálisgép-bővítmény egy meglévő virtuális gépek üzembe helyezéséhez. Futtassa a parancsot, a nyilvános és privát konfigurációk kell egy PowerShell kivonattábla kell tárolni.

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

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure PowerShell-modul segítségével. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure PowerShell modullal:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő könyvtárban található fájlok:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Vagy egy Azure-támogatási esemény fájl is. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatás**. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

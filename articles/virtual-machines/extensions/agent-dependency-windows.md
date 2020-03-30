---
title: Azure Monitor függőségi virtuálisgép-bővítmény e Windows rendszerhez
description: Telepítse az Azure Monitor függőségi ügynök a Windows virtuális gépen egy virtuális gép bővítmény használatával.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 27d43af2d5860d287d8b5914379747ae528db34b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250672"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Azure Monitor függőségi virtuálisgép-bővítmény e Windows rendszerhez

Az Azure Monitor szolgáltatás virtuális gépek leképezése szolgáltatás lekéri az adatokat a Microsoft függőségi ügynök. Az Azure VM-függőségi ügynök virtuálisgép-bővítmény a Windows a Microsoft által közzétett és támogatott. A bővítmény telepíti a függőségi ügynök az Azure virtuális gépeken. Ez a dokumentum részletezi a támogatott platformok, konfigurációk és üzembe helyezési lehetőségek az Azure VM függőségi ügynök virtuálisgép-bővítmény a Windows.

## <a name="operating-system"></a>Operációs rendszer

Az Azure VM-függőségi ügynök bővítmény a Windows futtatható a támogatott operációs rendszerek az Azure Monitor szolgáltatás virtuális gépek telepítési cikk támogatott [operációs rendszerek](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) szakaszában felsorolt támogatott operációs rendszerek.

## <a name="extension-schema"></a>Bővítményséma

A következő JSON az Azure VM-függőségi ügynök bővítmény sémáját mutatja be egy Azure Windows virtuális gépen.

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

### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték/Példa |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| közzétevő | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentWindows |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Resource Manager-sablonokkal telepítheti az Azure VM-bővítményeket. Az Azure Resource Manager-sablon előző szakaszában részletezett JSON-séma használatával futtathatja az Azure VM-függőségi ügynök bővítményt az Azure Resource Manager-sablon üzembe helyezése során.

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
        "type": "DependencyAgentWindows",
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
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell-telepítés

A `Set-AzVMExtension` parancs segítségével telepítheti a függőségi ügynök virtuálisgép-bővítményt egy meglévő virtuális gépre. A parancs futtatása előtt a nyilvános és privát konfigurációkat egy PowerShell-kivonattáblában kell tárolni.

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

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról és az Azure PowerShell-modul használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure PowerShell-modul használatával:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A bővítmény-végrehajtási kimenet a következő könyvtárban található fájlokba kerül:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Vagy benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.** Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.

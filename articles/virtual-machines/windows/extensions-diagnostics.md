---
title: Azure Diagnosztikai bővítmény windowsos használatra
description: Az Azure Windows virtuális gépek figyelése az Azure Diagnosztikai bővítmény használatával
services: virtual-machines-windows
documentationcenter: ''
author: johnkemnetz
manager: ashwink
editor: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.openlocfilehash: d4ba3e8bbdad72f10c630a056f988ec83e9b1a68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471618"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Azure diagnosztikai bővítmény Windows virtuális gépekhez

## <a name="overview"></a>Áttekintés

Az Azure Diagnostics virtuálisgép-bővítmény lehetővé teszi, hogy figyelési adatokat, például teljesítményszámlálókat és eseménynaplókat gyűjtsön a Windows virtuális gépről. Részletesen megadhatja, hogy milyen adatokat szeretne gyűjteni, és hová szeretné látni az adatokat, például egy Azure Storage-fiók vagy egy Azure Event Hub. Ezeket az adatokat is használhatja diagramok létrehozásához az Azure Portalon, vagy metrikariasztások at hozhat létre.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Az Azure Diagnosztikai bővítmény futtatható windows 10-es, Windows Server 2008 R2, 2012, 2012 R2 és 2016 rendszeren.

### <a name="internet-connectivity"></a>Internetkapcsolat

Az Azure Diagnostics Extension megköveteli, hogy a cél virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

[Az Azure Diagnostics Extension séma és a tulajdonság értékek ismertetik ebben a dokumentumban.](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. Az előző szakaszban részletezett JSON-séma egy Azure Resource Manager-sablonban használható az Azure Diagnostics bővítmény futtatásához az Azure Resource Manager-sablon üzembe helyezése során. Lásd: [Figyelés és diagnosztika használata Windows vm és Azure Resource Manager-sablonokkal.](extensions-diagnostics-template.md)

## <a name="azure-cli-deployment"></a>Az Azure CLI üzembe helyezése

Az Azure CLI használható az Azure Diagnostics bővítmény üzembe helyezéséhez egy meglévő virtuális gépen. Cserélje le a védett beállításokat és beállítások tulajdonságait érvényes JSON-ra a fenti bővítménysémából. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>PowerShell-telepítés

A `Set-AzVMDiagnosticsExtension` parancs segítségével hozzáadhatja az Azure Diagnostics bővítményt egy meglévő virtuális géphez. Lásd: [A PowerShell használata az Azure Diagnosztika engedélyezéséhez Windows rendszert futtató virtuális gépeken.](ps-extensions-diagnostics.md)

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról és az Azure CLI használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Tekintse meg ezt](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) a cikket egy átfogóbb hibaelhárítási útmutató az Azure Diagnostics bővítmény.

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.

## <a name="next-steps"></a>Következő lépések
* [További információ az Azure diagnosztikai bővítményről](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [A bővítményséma és -verziók áttekintése](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)

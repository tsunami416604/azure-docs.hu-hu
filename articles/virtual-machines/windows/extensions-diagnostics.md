---
title: A Windows Azure diagnosztikai bővítmény |} A Microsoft Docs
description: Azure Windows virtuális gépek kezelése az Azure Diagnostics bővítmény
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
ms.openlocfilehash: fe24307f7ed01c64217bcb1f9f7645a30bce7f44
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322823"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Az Azure Diagnostics bővítmény Windows virtuális gépek számára

## <a name="overview"></a>Áttekintés

Az Azure Diagnostics Virtuálisgép-bővítmény lehetővé teszi a figyelési adatok, például teljesítményszámlálók és az eseménynaplókat, gyűjtését a Windows virtuális gép. Kínálja megadhatja, milyen adatokat szeretne gyűjteni, és hol kívánja-e az adatokat szeretne lépni, például az Azure Storage-fiók vagy egy Azure-Eseményközpontba. Diagramok az Azure Portalon hozhat létre, vagy hozzon létre metrikariasztásokat is használhatja ezeket az adatokat.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

Az Azure Diagnostics bővítményével Windows 10-ügyfél a Windows Server 2008 R2, 2012, 2012 R2 és 2016 is futtatni.

### <a name="internet-connectivity"></a>Internetkapcsolat

Az Azure Diagnostics bővítmény szükséges, hogy a céloldali virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

[Ez a dokumentum ismerteti az Azure Diagnostics bővítmény séma- és értékeket.](../../azure-monitor/platform/diagnostics-extension-schema-1dot3.md)

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Az előző szakaszban részletes JSON-sémájában az Azure Resource Manager-sablon használható az Azure Diagnostics bővítmény futtatásához az Azure Resource Manager-sablon telepítése során. Lásd: [használható monitorozási és diagnosztikai egy Windows virtuális gép és az Azure Resource Manager-sablonokkal](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az Azure CLI segítségével az Azure Diagnostics bővítmény egy meglévő virtuális gépek üzembe helyezéséhez. A védett beállításai és tulajdonságai cserélje le a fenti bővítményséma érvényes JSON. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>PowerShell környezetben végzett telepítés

A `Set-AzureRmVMDiagnosticsExtension` parancs használható az Azure Diagnostics bővítmény hozzáadása egy meglévő virtuális gépet. Lásd még: [Windows rendszerű virtuális gép az Azure Diagnostics engedélyezéséhez használja a Powershellt](ps-extensions-diagnostics.md).
```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzureRmVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure parancssori felület használatával. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure CLI használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Ebben a cikkben](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md) átfogóbb hibaelhárítási útmutató az Azure Diagnostics bővítmény.

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések
* [További információ az Azure Diagnostics bővítmény](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [Tekintse át a séma kiterjesztése és -verziók](../../azure-monitor/platform/diagnostics-extension-schema.md)

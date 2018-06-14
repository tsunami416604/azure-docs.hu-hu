---
title: A Windows Azure diagnosztikai bővítmény |} Microsoft Docs
description: A Windows Azure diagnosztikai kiterjesztéssel Azure Windows virtuális gépek figyelése
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
ms.openlocfilehash: a9621f6f93d8e14e53cfe05ca4a714e88b9d8572
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30928711"
---
# <a name="windows-azure-diagnostics-extension"></a>Windows Azure diagnosztikai bővítmény

## <a name="overview"></a>Áttekintés

A Windows Azure Diagnostics Virtuálisgép-bővítmény lehetővé teszi a figyelési adatok, például a teljesítményszámlálók és az eseménynaplók, gyűjtése a Windows virtuális Gépre. Milyen adatokat szeretne gyűjteni, és ha azt szeretné, hogy az adatokat, például egy Azure Storage-fiókot vagy egy Azure Eseményközponthoz granularly adhatja meg. Ezen adatok segítségével is létre diagramokat az Azure portálon, vagy hozzon létre metrika riasztásokat.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Windows Azure diagnosztikai bővítmény is futtathatók a Windows 10-ügyfeleknek, Windows Server 2008 R2, 2012, a 2012 R2 és a 2016.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Windows Azure diagnosztikai bővítmény szükséges, hogy a cél virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>A séma kiterjesztése

[Ez a dokumentum ismerteti a Windows Azure diagnosztikai bővítmény séma és a tulajdonság értékét.](../../monitoring-and-diagnostics/azure-diagnostics-schema-1dot3-and-later.md)

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonok is telepíthető. Az előző szakaszban ismertetett JSON-séma segítségével az Azure Resource Manager-sablon a Windows Azure diagnosztikai bővítmény futtatása az Azure Resource Manager sablon üzembe helyezése során. Lásd: [használata figyelési és diagnosztika a Windows virtuális gép és az Azure Resource Manager-sablonok](extensions-diagnostics-template.md).

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az Azure CLI segítségével telepítse a Windows Azure diagnosztikai bővítmény egy meglévő virtuális gépet. A fenti bővítmény séma érvényes JSON cserélje le a védett beállítások és a helybeállítások tulajdonságai. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>PowerShell telepítése

A `Set-AzureRmVMDiagnosticsExtension` parancs használható a Windows Azure diagnosztikai bővítmény hozzáadása egy meglévő virtuális gépet. Lásd még: [Windows rendszerű virtuális gépként Azure Diagnostics engedélyezéséhez használja a Powershellt](ps-extensions-diagnostics.md).
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

Bővítmény központi telepítések állapotára vonatkozó lehet adatokat beolvasni az Azure-portálon, és az Azure parancssori felület használatával. A megadott virtuális gépek bővítmények központi telepítési állapotának megtekintéséhez a következő parancsot az Azure parancssori felület használatával.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

[Ebben a cikkben találhat](../../monitoring-and-diagnostics/azure-diagnostics-troubleshooting.md) egy átfogóbb hibaelhárítási útmutató a Windows Azure diagnosztikai bővítmény.

### <a name="support"></a>Támogatás

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a a [MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl az Azure támogatási incidens. Lépjen a [az Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza ki a Get-támogatási szolgálathoz. Támogatja az Azure használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések
* [További információ a Windows Azure diagnosztikai bővítmény](../../monitoring-and-diagnostics/azure-diagnostics.md)
* [Tekintse át a séma kiterjesztése és -verziók](../../monitoring-and-diagnostics/azure-diagnostics-schema.md)

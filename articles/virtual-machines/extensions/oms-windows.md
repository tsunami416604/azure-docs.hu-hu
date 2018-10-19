---
title: Windows Azure Log Analytics virtuális gépi bővítmény |} A Microsoft Docs
description: Windows virtuális gép, virtuálisgép-bővítmények használatával a Log Analytics-ügynök telepítése.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: roiyz
ms.openlocfilehash: 12f7c52f916f385ddf95cf16aa89c4848ab7c118
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406602"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Log Analytics virtuálisgép-bővítmény a Windows

A log Analytics monitorozási képességeket biztosít a felhőben és helyszíni eszközökön. A Log Analytics ügynök virtuálisgép-bővítmény a Windows közzétett és a Microsoft támogatja. A bővítmény a Log Analytics-ügynököket telepíti az Azure-beli virtuális gépeken, és regisztrálja a virtuális gépek egy meglévő Log Analytics-munkaterületet. Ez a dokumentum részletesen, a támogatott platformok, konfigurációk és üzembe helyezési lehetőségeit a Log Analytics virtuálisgép-bővítmény Windows.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Log Analytics-ügynök bővítmény esetében a Windows is futtatható a Windows Server 2008 R2, 2012, 2012 R2 és 2016-kiadások.

### <a name="azure-security-center"></a>Azure Security Center

Az Azure Security Center automatikusan építi ki a Log Analytics-ügynököket, és csatlakoztatja a az alapértelmezett log analytics-munkaterületet az Azure-előfizetés. Ha az Azure Security Center használ, ne futtassa a jelen dokumentumban leírt lépések segítségével. Ez felülírja a konfigurált munkaterületével és a szünet a kapcsolatot az Azure Security Centerrel.

### <a name="internet-connectivity"></a>Internetkapcsolat
A Log Analytics Windows agent bővítmény szükséges, hogy a céloldali virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Log Analytics-ügynök bővítmény sémáját jeleníti meg. A bővítmény telepítéséhez, a munkaterület azonosítóját és a cél Log Analytics-munkaterületet a munkaterület kulcsát. Ezek a beállítások a munkaterület az Azure Portalon található. A munkaterület kulcsát kényes adatként kell kezelni, mert azt egy védett beállítás konfigurációjának kell tárolni. Az Azure VM-bővítmény védett beállítás adatok titkosítva, és csak az átjárót tartalmazó a cél virtuális gépen. Vegye figyelembe, hogy **munkaterület azonosítója** és **workspaceKey** kis-és nagybetűket.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| munkaterület azonosítója (e.g)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (például:) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* A munkaterület azonosítója a consumerId nevezzük a Log Analytics API-ban.

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Az előző szakaszban részletes JSON-sémája a Log Analytics-ügynök bővítmény futtatása során egy Azure Resource Manager-sablon üzembe helyezése Azure Resource Manager-sablon is használható. A Log Analytics ügynök Virtuálisgép-bővítményt tartalmazó mintát sablon megtalálható a [Azure gyors üzembe helyezési katalógus](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

A JSON-t egy virtuálisgép-bővítményt a virtuális gép típusú erőforrást belülre, vagy elhelyezve, a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON-fájllal való elhelyezését hatással van az erőforrás nevét, és írja be az értékét. További információkért lásd: [állítsa be a nevét és típusát gyermekerőforrásait](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

Az alábbi példa azt feltételezi, hogy a Log Analytics-bővítményt a virtuális gép típusú erőforrást van beágyazva. A bővítmény erőforrás beágyazása, ha a JSON az kerül a `"resources": []` objektum a virtuális gép.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Helyezi el a JSON-bővítmény a sablonban gyökérmappájában, amikor az erőforrás neve a szülő virtuális gép egy hivatkozást tartalmaz, és a típus a beágyazott konfigurációját tükrözi. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell környezetben végzett telepítés

A `Set-AzureRmVMExtension` parancs is használható a Log Analytics ügynök virtuálisgép-bővítmény egy meglévő virtuális gépek üzembe helyezéséhez. A parancs futtatása előtt a nyilvános és privát konfigurációk kell egy PowerShell kivonattábla kell tárolni. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure PowerShell-modul segítségével. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure PowerShell modullal.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő könyvtárban található fájlok:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

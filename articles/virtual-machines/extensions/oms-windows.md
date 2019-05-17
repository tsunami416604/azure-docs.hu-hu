---
title: A figyelő a Windows Azure virtuális gépi bővítmény |} A Microsoft Docs
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
ms.date: 04/29/2019
ms.author: roiyz
ms.openlocfilehash: 270b3ae49a815c9e12fce9377c8298192237f28a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790369"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>A figyelő a Windows Azure virtuális gépi bővítmény

Az Azure Monitor naplóira monitorozási képességeket biztosít a felhőbeli és helyszíni eszközök között. A Log Analytics ügynök virtuálisgép-bővítmény a Windows közzétett és a Microsoft támogatja. A bővítmény a Log Analytics-ügynököket telepíti az Azure-beli virtuális gépeken, és regisztrálja a virtuális gépek egy meglévő Log Analytics-munkaterületet. Ez a dokumentum részletesen, a támogatott platformok, a konfigurációk és a Windows Azure Monitor virtuálisgép-bővítmény az üzembe helyezési lehetőségeit.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A Log Analytics agent bővítmény Windows támogatja a Windows operációs rendszer verzió a következő:

- A Windows Server 2019
- A Windows Server 2008 R2, 2012, 2012 R2, 2016 1709-es és 1803-as verzióban

### <a name="agent-and-vm-extension-version"></a>Az ügynök és a Virtuálisgép-bővítmény verziója
Az alábbi táblázat tartalmazza az Azure Monitor Virtuálisgép-bővítmény és a Log Analytics ügynök csomag minden kiadott verziójáról. 

| Az Azure Monitor Linux rendszerű virtuális gép bővítmény verziója | Log Analytics-ügynököket csomag verziója | Kiadás dátuma | Kibocsátási megjegyzések |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 8.0.11049.0 | 1.0.11049.1 | 2017. február | |
| 8.0.11072.0 | 1.0.11072.1 | Szeptembertől 2017. | |
| 8.0.11081.0 | 1.0.11081.5 | 2017. november | | 
| 8.0.11103.0 | n/a |  2018. április | |
| 8.0.11136.0 | n/a | Szeptembertől 2018. |  <ul><li> Támogatás hozzáadva az erőforrás-azonosító módosítását a virtuális gép áthelyezése észlelése </li><li> Támogatás hozzáadva a jelentési erőforrást azonosító használata nem bővítmény telepítése </li></ul>| 
| 10.19.10006.0 | n/a | A 2018. december | <ul><li> Kisebb stabilizálása javításai </li></ul> | 
| 10.19.13515.0 | 1.0.13515.1 | 2019. március | <ul><li>Kisebb stabilizálása javításai </li></ul> |

### <a name="azure-security-center"></a>Azure Security Center

Az Azure Security Center automatikusan építi ki a Log Analytics-ügynököket, és csatlakoztatja az Azure-előfizetés alapértelmezett Log Analytics-munkaterületen. Ha az Azure Security Center használ, ne futtassa a jelen dokumentumban leírt lépések segítségével. Ez felülírja a konfigurált munkaterületével és a szünet a kapcsolatot az Azure Security Centerrel.

### <a name="internet-connectivity"></a>Internetkapcsolat
A Log Analytics Windows agent bővítmény szükséges, hogy a céloldali virtuális gép csatlakozik az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Log Analytics-ügynök bővítmény sémáját jeleníti meg. A bővítmény a munkaterület-Azonosítót és a munkaterület kulcsát a cél Log Analytics-munkaterület szükséges. Ezek a beállítások a munkaterület az Azure Portalon található. A munkaterület kulcsát kényes adatként kell kezelni, mert azt egy védett beállítás konfigurációjának kell tárolni. Az Azure VM-bővítmény védett beállítás adatok titkosítva, és csak az átjárót tartalmazó a cél virtuális gépen. Vegye figyelembe, hogy **munkaterület azonosítója** és **workspaceKey** kis-és nagybetűket.

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

## <a name="template-deployment"></a>Sablon telepítése

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Az előző szakaszban részletes JSON-sémája a Log Analytics-ügynök bővítmény futtatása során egy Azure Resource Manager-sablon üzembe helyezése Azure Resource Manager-sablon is használható. A Log Analytics ügynök Virtuálisgép-bővítményt tartalmazó mintát sablon megtalálható a [Azure gyors üzembe helyezési katalógus](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

>[!NOTE]
>A sablon nem támogatja egynél több munkaterület-Azonosítót és a munkaterületkulcsot megadását, ha meg szeretné konfigurálni az ügynököt, hogy több munkaterületnek küldjenek jelentést. Az ügynököt, hogy több munkaterületnek küldjenek jelentést beállítása: [hozzáadása és eltávolítása a munkaterület](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace).  

A JSON-t egy virtuálisgép-bővítményt a virtuális gép típusú erőforrást belülre, vagy elhelyezve, a legfelső szintű vagy a legfelső szintű Resource Managerből származó JSON-sablon. A JSON-fájllal való elhelyezését hatással van az erőforrás nevét, és írja be az értékét. További információkért lásd: [állítsa be a nevét és típusát gyermekerőforrásait](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources). 

Az alábbi példa azt feltételezi, hogy az Azure Monitor-bővítményt a virtuális gép típusú erőforrást van beágyazva. A bővítmény erőforrás beágyazása, ha a JSON az kerül a `"resources": []` objektum a virtuális gép.


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

A `Set-AzVMExtension` parancs is használható a Log Analytics ügynök virtuálisgép-bővítmény egy meglévő virtuális gépek üzembe helyezéséhez. A parancs futtatása előtt a nyilvános és privát konfigurációk kell egy PowerShell kivonattábla kell tárolni. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
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

### <a name="troubleshoot"></a>Az eszköz nem tudta a várt módon befejezni a szinkronizálást. A probléma megoldásának módjáról erre az üzenetre kattintva tájékozódhat.

Bővítmény központi telepítések állapotát lehet adatokat beolvasni az Azure Portalról, és az Azure PowerShell-modul segítségével. Adott Virtuálisgép-bővítmények központi telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure PowerShell modullal.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Bővítmény végrehajtás kimenetének a rendszer naplózza a következő könyvtárban található fájlok:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

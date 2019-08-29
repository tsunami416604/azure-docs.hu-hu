---
title: Azure Monitor virtuálisgép-bővítmény a Windowshoz | Microsoft Docs
description: Telepítse a Log Analytics Agent ügynököt a Windows rendszerű virtuális gépen a virtuálisgép-bővítmény használatával.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2019
ms.author: roiyz
ms.openlocfilehash: 393b01d7815d94e4ad623c552f4110421c9d98bd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092254"
---
# <a name="azure-monitor-virtual-machine-extension-for-windows"></a>A Windows rendszerhez készült virtuálisgép-bővítmény Azure Monitor

Azure Monitor naplók a Felhőbeli és a helyszíni eszközök figyelési lehetőségeit biztosítják. A Windows rendszerhez készült Log Analytics Agent virtuálisgép-bővítményt a Microsoft közzétette és támogatja. A bővítmény a Log Analytics-ügynököket telepíti az Azure-beli virtuális gépeken, és regisztrálja a virtuális gépek egy meglévő Log Analytics-munkaterületet. Ez a dokumentum a Windows rendszerhez készült Azure Monitor virtuálisgép-bővítmény támogatott platformait, konfigurációit és központi telepítési lehetőségeit részletezi.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A támogatott Windows operációs rendszerekkel kapcsolatos részletekért tekintse meg a [log Analytics ügynök áttekintését](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) ismertető cikket.

### <a name="agent-and-vm-extension-version"></a>Az ügynök és a Virtuálisgép-bővítmény verziója
Az alábbi táblázat a Windows Azure Monitor virtuálisgép-bővítmény verziójának és Log Analytics ügynök csomagjának leképezését tartalmazza minden egyes kiadáshoz. 

| Log Analytics Windows-ügynök csomagjának verziója | Azure Monitor Windowsos virtuálisgép-bővítmény verziója | Kiadás dátuma | Kibocsátási megjegyzések |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18011 | 1.0.18011 | Július 2019 | <ul><li> Kisebb hibajavítások és stabilizáció-javítások </li><li> Megnövekedett MaxExpressionDepth – 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | Június 2019 | <ul><li> Kisebb hibajavítások és stabilizáció-javítások </li><li> Új lehetőség az alapértelmezett hitelesítő adatok letiltására a proxy-kapcsolatok létrehozásakor (WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH-támogatás) </li></ul>|
| 10.19.13515 | 1.0.13515 | Március 2019 | <ul><li>Kisebb stabilizációs javítások </li></ul> |
| 10.19.10006 | n/a | Dec 2018 | <ul><li> Kisebb stabilizációs javítások </li></ul> | 
| 8.0.11136 | n/a | Szeptember 2018 |  <ul><li> Az erőforrás-azonosító változásának észlelése a virtuális gépek áthelyezésének támogatásával </li><li> Jelentéskészítési erőforrás-azonosító támogatása a nem bővítmények telepítésének használatakor </li></ul>| 
| 8.0.11103 | n/a |  2018. április | |
| 8.0.11081 | 1.0.11081 | November 2017 | | 
| 8.0.11072 | 1.0.11072 | Szeptember 2017 | |
| 8.0.11049 | 1.0.11049 | Feb 2017 | |

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center automatikusan kiépíti a Log Analytics ügynököt, és az Azure-előfizetés alapértelmezett Log Analytics munkaterületével csatlakoztatja. Ha az Azure Security Center használ, ne futtassa a jelen dokumentumban leírt lépések segítségével. Ezzel felülírja a konfigurált munkaterületet, és megszakítja a kapcsolódást a Azure Security Center.

### <a name="internet-connectivity"></a>Internetkapcsolat
A Windowshoz készült Log Analytics-ügynök bővítmény megköveteli, hogy a célként megadott virtuális gép csatlakoztatva legyen az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Log Analytics ügynök bővítmény sémáját jeleníti meg. A kiterjesztéshez a munkaterület-azonosító és a munkaterület kulcsa szükséges a cél Log Analytics munkaterületről. Ezek a Azure Portal munkaterületének beállításaiban találhatók. A munkaterület kulcsát kényes adatként kell kezelni, mert azt egy védett beállítás konfigurációjának kell tárolni. Az Azure VM-bővítmény védett beállítás adatok titkosítva, és csak az átjárót tartalmazó a cél virtuális gépen. Vegye figyelembe, hogy **munkaterület azonosítója** és **workspaceKey** kis-és nagybetűket.

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
| workspaceId (például:)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (például:) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\*A munkaterület azonosítója neve consumerId a Log Analytics API-ban.

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények is üzembe helyezhetők az Azure Resource Manager-sablonok. Az előző szakaszban részletezett JSON-séma használható Azure Resource Manager sablonban az Log Analytics Agent bővítmény futtatásához Azure Resource Manager sablon központi telepítésekor. Az Log Analytics Agent virtuálisgép-bővítményt tartalmazó minta sablon az [Azure Gyorskonfigurálás Galleryben](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)található. 

>[!NOTE]
>A sablon nem támogatja több munkaterület-azonosító és munkaterület-kulcs megadását, ha úgy szeretné konfigurálni az ügynököt, hogy több munkaterületre is jelentsen. Ha úgy szeretné beállítani az ügynököt, hogy több munkaterületnek jelentsen, tekintse meg a [munkaterület hozzáadása vagy eltávolítása](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace)című témakört.  

A virtuálisgép-bővítmények JSON-je beágyazható a virtuális gép erőforrásaiba, vagy egy Resource Manager JSON-sablon legfelső szintű vagy legfelső szintjén helyezhető el. A JSON elhelyezése hatással van az erőforrás nevének és típusának értékére. További információkért lásd: [állítsa be a nevét és típusát gyermekerőforrásait](../../azure-resource-manager/child-resource-name-type.md). 

Az alábbi példa azt feltételezi, hogy a Azure Monitor bővítmény a virtuális gép erőforrásán belül van beágyazva. A bővítmény erőforrás beágyazása, ha a JSON az kerül a `"resources": []` objektum a virtuális gép.


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

## <a name="powershell-deployment"></a>PowerShell-telepítés

A `Set-AzVMExtension` parancs használatával telepítheti a log Analytics Agent virtuálisgép-bővítményt egy meglévő virtuális gépre. A parancs futtatása előtt a nyilvános és a privát konfigurációkat egy PowerShell-kivonatoló táblában kell tárolni. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
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

A bővítmények állapotával kapcsolatos adatok beolvashatók a Azure Portalból, és az Azure PowerShell modul használatával. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa az alábbi parancsot a Azure PowerShell modul használatával.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A bővítmény-végrehajtás kimenete a következő könyvtárban található fájlokra van naplózva:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

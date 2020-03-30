---
title: Log Analytics virtuális gépi bővítmény Windowshoz
description: Telepítse a Log Analytics-ügynököt a Windows virtuális gépen egy virtuálisgép-bővítmény használatával.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/30/2020
ms.author: akjosh
ms.openlocfilehash: 85977819d30ddc8745eb9231242eb1990222676c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530988"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>Log Analytics virtuális gépi bővítmény Windowshoz

Az Azure Monitor Logs figyelési képességeket biztosít a felhőbeli és helyszíni eszközökön. A Windows Log Analytics ügynökügynök-bővítményét a Microsoft közzéteszi és támogatja. A bővítmény telepíti a Log Analytics-ügynököt az Azure virtuális gépeken, és virtuális gépeket foglal be egy meglévő Log Analytics-munkaterületre. Ez a dokumentum részletezi a támogatott platformok, konfigurációk és üzembe helyezési lehetőségek a Log Analytics virtuálisgép-bővítmény a Windows.

## <a name="prerequisites"></a>Előfeltételek

### <a name="operating-system"></a>Operációs rendszer

A támogatott Windows operációs rendszerekkel kapcsolatos részleteket a [Log Analytics-ügynök áttekintéséről](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) szóló cikk tartalmazza.

### <a name="agent-and-vm-extension-version"></a>Ügynök és vm-bővítmény verziója
Az alábbi táblázat a Windows Log Analytics virtuálisgép-bővítmény és a Log Analytics-ügynökcsomag verziójának leképezését tartalmazza az egyes kiadásokhoz. 

| Log Analytics Windows ügynökcsomag verziója | Log Analytics Windows VM bővítmény verziója | Megjelenési dátum | Kibocsátási megjegyzések |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18029 | 1.0.18029 | 2020. március   | <ul><li>SHA-2 kódaláírási támogatás hozzáadása</li><li>Javítja a virtuális gép bővítményének telepítését és kezelését</li><li>Hiba elhárítása az Azure Arc kiszolgálókhoz integrációjában</li><li>Beépített hibaelhárító eszköz hozzáadása az ügyfélszolgálathoz</li><li>További Azure Government-régiók támogatása</li> |
| 10.20.18018 | 1.0.18018 | 2019. október | <ul><li> Kisebb hibajavítások és stabilizációs fejlesztések </li></ul> |
| 10.20.18011 | 1.0.18011 | 2019. július | <ul><li> Kisebb hibajavítások és stabilizációs fejlesztések </li><li> A MaxExpressionDepth növelése 10000-re </li></ul> |
| 10.20.18001 | 1.0.18001 | 2019. június | <ul><li> Kisebb hibajavítások és stabilizációs fejlesztések </li><li> Proxykapcsolat létrehozásakor az alapértelmezett hitelesítő adatok letiltásának lehetősége (WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH támogatása) </li></ul>|
| 10.19.13515 | 1.0.13515 | 2019. március | <ul><li>Kisebb stabilizációs javítások </li></ul> |
| 10.19.10006 | n/a | 2018. december | <ul><li> Kisebb stabilizációs javítások </li></ul> | 
| 8.0.11136 | n/a | 2018. szeptember |  <ul><li> Hozzáadott támogatás az erőforrás-azonosító változásának észleléséhez a virtuális gépek áthelyezésekén </li><li> Hozzáadott támogatás jelentési erőforrás-azonosító használata esetén nem kiterjesztés telepítés </li></ul>| 
| 8.0.11103 | n/a |  2018. április | |
| 8.0.11081 | 1.0.11081 | 2017. november | | 
| 8.0.11072 | 1.0.11072 | 2017. szeptember | |
| 8.0.11049 | 1.0.11049 | 2017. február | |


### <a name="azure-security-center"></a>Azure Security Center

Az Azure Security Center automatikusan leköti a Log Analytics-ügynököt, és csatlakoztatja azt az Azure-előfizetés alapértelmezett Log Analytics-munkaterületéhez. Ha az Azure Security Centert használja, ne futtassa a jelen dokumentum lépéseit. Ezzel felülírja a konfigurált munkaterületet, és megszakítja a kapcsolatot az Azure Security Centerrel.

### <a name="internet-connectivity"></a>Internetkapcsolat
A Windows Log Analytics-ügynökbővítménymegköveteli, hogy a cél virtuális gép csatlakozzon az internethez. 

## <a name="extension-schema"></a>Bővítményséma

A következő JSON a Log Analytics-ügynökbővítmény sémáját jeleníti meg. A bővítmény megköveteli a munkaterület-azonosító és a munkaterület kulcsa a cél Log Analytics munkaterületről. Ezek az Azure Portal munkaterületének beállításaiban találhatók. Mivel a munkaterületi kulcsot bizalmas adatként kell kezelni, védett beállítási konfigurációban kell tárolni. Az Azure VM-bővítmény védett beállítási adatai titkosítva vannak, és csak a cél virtuális gépen fejtik vissza. Vegye figyelembe, hogy **a workspaceId** és **a workspaceKey** a kis- és nagybetűket is figyelembe veszi.

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
### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték / Példa |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| közzétevő | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| munkaterület-azonosító (pl.)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (pl. | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\*A workspaceId neve a log analytics API-ban consumerId.

> [!NOTE]
> További tulajdonságokért lásd: Azure [Connect Windows Computers to Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

## <a name="template-deployment"></a>Sablonalapú telepítés

Az Azure Virtuálisgép-bővítmények az Azure Resource Manager-sablonokkal telepíthetők. Az előző szakaszban részletezett JSON-séma egy Azure Resource Manager-sablonban használható a Log Analytics-ügynök bővítmény futtatásához az Azure Resource Manager-sablon üzembe helyezése során. A Log Analytics-ügynök virtuálisgép-bővítményét tartalmazó mintasablon az [Azure Quickstart Gallery webhelyen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)található. 

>[!NOTE]
>A sablon nem támogatja egynél több munkaterület-azonosító és munkaterületkulcs megadását, ha úgy szeretné konfigurálni az ügynököt, hogy több munkaterületnek jelentsen. Ha úgy szeretné beállítani az ügynököt, hogy több munkaterületnek jelentsen, olvassa el a Munkaterület hozzáadása vagy eltávolítása című [témakört.](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace)  

A virtuálisgép-bővítmény JSON-ja beágyazható a virtuálisgép-erőforrásba, vagy elhelyezhető egy Erőforrás-kezelő JSON-sablon gyökér- vagy legfelső szintjén. A JSON elhelyezése hatással van az erőforrás nevének és típusának értékére. További információt a [Név és a gyermekerőforrások típusának beállítása](../../azure-resource-manager/templates/child-resource-name-type.md)című témakörben talál. 

A következő példa feltételezi, hogy a Log Analytics-bővítmény a virtuálisgép-erőforrásba van ágyazva. A bővítmény erőforrás beágyazásakor a `"resources": []` JSON a virtuális gép objektumába kerül.


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

Amikor a JSON bővítményt helyezi a sablon gyökeréhez, az erőforrás neve tartalmaz egy hivatkozást a szülő virtuális gépre, és a típus a beágyazott konfigurációt tükrözi. 

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

A `Set-AzVMExtension` parancs segítségével telepítheti a Log Analytics ügynök virtuálisgép-bővítményt egy meglévő virtuális gépre. A parancs futtatása előtt a nyilvános és privát konfigurációkat egy PowerShell-kivonattáblában kell tárolni. 

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

A bővítmény-üzembe helyezések állapotára vonatkozó adatok az Azure Portalról és az Azure PowerShell-modul használatával is lekérdezhetők. Egy adott virtuális gép bővítményeinek telepítési állapotának megtekintéséhez futtassa a következő parancsot az Azure PowerShell-modul használatával.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

A bővítmény-végrehajtási kimenet a következő könyvtárban található fájlokba kerül:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/) Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni lehetőséget. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.

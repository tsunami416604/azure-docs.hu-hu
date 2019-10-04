---
title: Hibrid környezetek Azure Monitor (előzetes verzió) engedélyezése | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Monitor for VMs egy vagy több virtuális gépet tartalmazó hibrid felhőalapú környezetben.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: b06fe477f551977b1357f3b1b185cb340a948052
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905500"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Azure Monitor for VMs (előzetes verzió) engedélyezése hibrid környezetekhez

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk azt ismerteti, hogyan engedélyezhető Azure Monitor for VMs (előzetes verzió) az adatközpontban vagy más felhőalapú környezetben üzemeltetett virtuális gépekhez vagy fizikai számítógépekhez. A folyamat végén sikeresen megkezdte a virtuális gépek monitorozását a környezetben, és megtudhatja, hogy a teljesítmény-és rendelkezésre állási problémákba ütközik-e. 

Mielőtt elkezdené, tekintse át az előfeltételeket, és győződjön meg arról, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek. [](vminsights-enable-overview.md) Tekintse át a követelményeket és a központi telepítési módszerek a [Log Analytics Linux és Windows-ügynök](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Az Azure Monitor, virtuális gépek térkép függőségi ügynök maga adatokat nem továbbít, és nincs szükség tűzfalak és a portok módosítása. A leképezési adatokat mindig a Log Analytics ügynök továbbítja a Azure Monitor szolgáltatáshoz közvetlenül vagy az [Operations Management Suite](../../azure-monitor/platform/gateway.md) -átjárón keresztül, ha az informatikai biztonsági szabályzatok nem engedélyezik a hálózaton lévő számítógépek számára az internethez való kapcsolódást.

A feladat elvégzéséhez szükséges lépéseket a következőképpen összegzi:

1. Telepítse a Windows vagy Linux rendszerhez készült Log Analytics-ügynököt. Az ügynök telepítése előtt tekintse át a [log Analytics ügynök áttekintését](../platform/log-analytics-agent.md) ismertető cikket a rendszerkövetelmények és a telepítési módszerek megismeréséhez.

2. Töltse le és telepítse az Azure Monitor, a virtuális gépek térkép függőségi ügynök [Windows](https://aka.ms/dependencyagentwindows) vagy [Linux](https://aka.ms/dependencyagentlinux).

3. A teljesítményszámlálók gyűjtésének engedélyezéséhez.

4. Az Azure Monitor-beli virtuális gépek üzembe helyezése.

>[!NOTE]
>A függőségi ügynök telepítéséhez a cikkben ismertetett információk a [Service Map megoldásra](service-map.md)is érvényesek.  

## <a name="install-the-dependency-agent-on-windows"></a>Telepítse a függőségi ügynököt Windows

Telepítheti a függőségi ügynök manuálisan a Windows-számítógépeken futó `InstallDependencyAgent-Windows.exe`. Ha a végrehajtható fájl kapcsolók nélkül futtatja, a telepítővarázsló, amelyekkel az ügynök telepítéséhez interaktív módon elindítja.

>[!NOTE]
>*Rendszergazdai* jogosultság szükséges telepíteni, vagy távolítsa el az ügynököt.

Az alábbi táblázat a paramétereket, az ügynök a parancssorból a telepítő által támogatott emeli ki.

| Paraméter | Leírás |
|:--|:--|
| /? | A parancssori kapcsolók listáját adja vissza. |
| /S | Felhasználói beavatkozás nélkül csendes telepítést hajt végre. |

A telepítőprogramnak a `/?` paraméterrel való futtatásához például írja be a következőt: **InstallDependencyAgent-Windows. exe/?** .

A Windows a függőségi ügynök fájlok vannak telepítve a *C:\Program Files\Microsoft függőségi ügynök* alapértelmezés szerint. Ha a telepítés befejezése után a függőségi ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. A naplózási könyvtár *%Programfiles%\Microsoft függőségi Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>A függőségi ügynök telepítése Linux rendszeren

A függőségi ügynök telepítve van a Linux-kiszolgálókon *InstallDependencyAgent-Linux64.bin*, azt a héjparancsfájlt, egy önkicsomagoló bináris. A fájl használatával futtathatja `sh` vagy adjon hozzá végrehajtási engedélyeket magát a fájlt.

>[!NOTE]
> Az ügynök telepítéséhez vagy eltávolításához gyökérszintű hozzáférés szükséges.
>

| Paraméter | Leírás |
|:--|:--|
| – Súgó | A parancssori kapcsolók listájának lekérése. |
| -s | Beavatkozás nélküli telepítés a felhasználónak szóló üzenetek nélkül. |
| --ellenőrzése | Ellenőrizze a jogosultságokat és az operációs rendszer, de ne telepítse az ügynököt. |

A telepítőprogramnak a `-help` paraméterrel való futtatásához például írja be a **InstallDependencyAgent-Linux64. bin-help**parancsot.

Telepítse a Linux függőségi ügynököt root-ként a parancs `sh InstallDependencyAgent-Linux64.bin`futtatásával.

Ha a függőségi ügynök nem indul el, ellenőrizze a hibával kapcsolatos részletes információk a naplókat. A Linux-ügynökök, a naplózási könyvtár van */var/opt/microsoft/dependency-agent/log*.

A függőségi ügynök fájlok kerülnek a következő könyvtárak:

| Fájlok | Hely |
|:--|:--|
| Alapvető fájlok | /opt/microsoft/dependency-agent |
| Naplófájlok | /var/opt/microsoft/dependency-agent/log |
| Konfigurációs fájlok | /etc/opt/microsoft/dependency-agent/config |
| Szolgáltatás végrehajtható fájljai | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Bináris tárolófájlok | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Telepítési példaszkriptek

Könnyedén üzembe helyezéséhez egyszerre több kiszolgálón a függőségi ügynököt, a következő parancsfájl például töltse le és telepítse a függőségi ügynököt Windows vagy Linux rendszeren.

### <a name="powershell-script-for-windows"></a>PowerShell-szkript Windowshoz

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Héjszkript Linuxhoz

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Célállapot-konfiguráló

A Desired State Configuration (DSC) használatával a függőségi ügynököt telepíti, a következő példakód a xPSDesiredStateConfiguration modul használhatja:

```powershell
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>Teljesítményszámlálók engedélyezése

Ha a Log Analytics-munkaterületet a megoldás által hivatkozott már nincs konfigurálva a megoldás által igényelt a teljesítményszámlálók adatainak összegyűjtése, meg kell engedélyezheti őket. Ezt kétféleképpen teheti meg:
* Leírtak szerint manuálisan [a Log Analytics Windows és Linux rendszerű teljesítmény adatforrások](../../azure-monitor/platform/data-sources-performance-counters.md)
* A [Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1) -katalógusból elérhető PowerShell-szkript letöltésével és futtatásával

## <a name="deploy-azure-monitor-for-vms"></a>Az Azure Monitor-beli virtuális gépek üzembe helyezése

Ez a módszer egy JSON-sablon, amely a megoldás-összetevőket a Log Analytics-munkaterület engedélyezése konfigurációját tartalmazza.

Ha nem tudja, hogyan helyezhet üzembe erőforrásokat sablon használatával, tekintse meg a következőt:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Az Azure CLI használatához először telepítenie és használnia kell a CLI-t helyileg. Kell futnia az Azure CLI 2.0.27-es vagy újabb. A verzió azonosításához futtassa `az --version`. Az Azure CLI telepítéséhez vagy frissítéséhez tekintse meg [Az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört.

### <a name="create-and-execute-a-template"></a>Hozzon létre, és hajtsa végre a sablon

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Mentse a fájlt *installsolutionsforvminsights.json* egy helyi mappába.

1. Rögzítse a *WorkspaceName*, a *ResourceGroupName*és a *WorkspaceLocation*értékeit. A *WorkspaceName* értéke a log Analytics munkaterület neve. Az érték *WorkspaceLocation* a régió, a munkaterület van definiálva.

1. Készen áll a sablon üzembe helyezése a következő PowerShell-parancs használatával:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A konfiguráció módosítása több percet is igénybe vehet. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

    ```powershell
    provisioningState       : Succeeded
    ```
   Miután engedélyezte a figyelés, az állapot és a hibrid számítógép metrikák megtekintéséhez nagyjából 10 percet vehet igénybe.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="vm-doesnt-appear-on-the-map"></a>A virtuális gép nem jelenik meg a térképen

Ha a függőségi ügynök telepítése sikeres volt, de a számítógép nem jelenik meg a térképen, a következő lépésekkel diagnosztizálhatja a problémát.

1. A függőségi ügynök sikeres telepítését? Ellenőrzi, hogy ha a szolgáltatás telepítve van és fut ellenőrizheti.

    **Windows**: Keresse meg a "Microsoft függőségi ügynök" nevű szolgáltatást. 

    **Linux**: Keresse meg a "Microsoft-függőség-ügynök" futó folyamatot.

2. Ön a [log Analytics ingyenes díjszabási szintjére](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)vonatkozik? Az ingyenes csomag legfeljebb öt egyedi számítógépet tesz lehetővé. A további számítógépek nem jelennek meg a térképen, még akkor is, ha az előző öt már nem küld adatokat.

3. A számítógép napló-és teljesítményszámláló-adatokat küld Azure Monitor naplókba? Hajtsa végre a következő lekérdezést a számítógépen: 

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Egy vagy több eredményt adott vissza? Az adatok legutóbbi? Ha igen, a Log Analytics-ügynök megfelelően működik, és kommunikál a szolgáltatással. Ha nem, ellenőrizze az ügynököt a kiszolgálón: [Log Analytics ügynök a Windows hibaelhárításához](../platform/agent-windows-troubleshoot.md) vagy [log Analytics ügynök a Linux rendszerhez – hibaelhárítás](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>A számítógép megjelenik a térképen, de nem rendelkezik folyamatokkal

Ha látja a kiszolgálót a térképen, de nem rendelkezik folyamat-vagy adatszolgáltatással, akkor ez azt jelzi, hogy a függőségi ügynök telepítve van és fut, de a kernel-illesztőprogram nem töltődött be. 

Ellenőrizze a C:\Program Files\Microsoft függőségi Agent\logs\wrapper.log fájlt (Windows) vagy /var/opt/microsoft/dependency-agent/log/service.log fájlt (Linux). Az utolsó sort jeleznie kell, miért nem töltődtek be a kernelbe. Például a kernel előfordulhat, hogy nem támogatja a linuxon futó Ha frissítette a kernel.


## <a name="next-steps"></a>További lépések

Most, hogy a figyelés engedélyezve van a virtuális gépek számára, ezek az információk a Azure Monitor for VMssal való elemzéshez érhetők el.
 
- Az állapotfigyelő funkció használatának megismeréséhez tekintse meg a [Azure monitor for VMS állapotának megtekintése](vminsights-health.md)című témakört.
- Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).
- Az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md)a szűk keresztmetszetek és a virtuális gépek teljesítményének teljes kihasználtsága alapján:.
- Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).
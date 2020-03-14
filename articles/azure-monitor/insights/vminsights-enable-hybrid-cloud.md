---
title: Hibrid környezetek Azure Monitor (előzetes verzió) engedélyezése | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Monitor for VMs egy vagy több virtuális gépet tartalmazó hibrid felhőalapú környezetben.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: bd44eebf8aceaf7fe32cf8cf1b1152db32acb344
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275138"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Azure Monitor for VMs (előzetes verzió) engedélyezése hibrid környezetekhez

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk azt ismerteti, hogyan engedélyezhető Azure Monitor for VMs (előzetes verzió) az adatközpontban vagy más felhőalapú környezetben üzemeltetett virtuális gépekhez vagy fizikai számítógépekhez. A folyamat végén sikeresen megkezdte a virtuális gépek monitorozását a környezetben, és megtudhatja, hogy a teljesítmény-és rendelkezésre állási problémákba ütközik-e.

Mielőtt elkezdené, tekintse át az [előfeltételeket](vminsights-enable-overview.md) , és győződjön meg arról, hogy az előfizetés és az erőforrások megfelelnek a követelményeknek. Tekintse át a [log Analytics Linux-és Windows-ügynök](../../log-analytics/log-analytics-agent-overview.md)követelményeit és üzembe helyezési módszereit.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Az Azure Monitor, virtuális gépek térkép függőségi ügynök maga adatokat nem továbbít, és nincs szükség tűzfalak és a portok módosítása. A leképezési adatokat mindig a Log Analytics ügynök továbbítja a Azure Monitor szolgáltatáshoz közvetlenül vagy az [Operations Management Suite-átjárón](../../azure-monitor/platform/gateway.md) keresztül, ha az informatikai biztonsági szabályzatok nem engedélyezik a hálózaton lévő számítógépek számára az internethez való kapcsolódást.

A feladat elvégzéséhez szükséges lépéseket a következőképpen összegzi:

1. Telepítse a Windows vagy Linux rendszerhez készült Log Analytics-ügynököt. Az ügynök telepítése előtt tekintse át a [log Analytics ügynök áttekintését](../platform/log-analytics-agent.md) ismertető cikket a rendszerkövetelmények és a telepítési módszerek megismeréséhez.

2. Töltse le és telepítse a Azure Monitor for VMs Map-függőségi ügynököt Windows vagy [Linux](https://aka.ms/dependencyagentlinux) [rendszerre](https://aka.ms/dependencyagentwindows) .

3. A teljesítményszámlálók gyűjtésének engedélyezéséhez.

4. Az Azure Monitor-beli virtuális gépek üzembe helyezése.

>[!NOTE]
>A függőségi ügynök telepítéséhez a cikkben ismertetett információk a [Service Map megoldásra](service-map.md)is érvényesek.  

## <a name="install-the-dependency-agent-on-windows"></a>Telepítse a függőségi ügynököt Windows

A függőségi ügynököt manuálisan is telepítheti a Windows rendszerű számítógépeken `InstallDependencyAgent-Windows.exe`futtatásával. Ha a végrehajtható fájl kapcsolók nélkül futtatja, a telepítővarázsló, amelyekkel az ügynök telepítéséhez interaktív módon elindítja.

>[!NOTE]
>Az ügynök telepítéséhez vagy eltávolításához *rendszergazdai* jogosultságok szükségesek.

Az alábbi táblázat a paramétereket, az ügynök a parancssorból a telepítő által támogatott emeli ki.

| Paraméter | Leírás |
|:--|:--|
| /? | A parancssori kapcsolók listáját adja vissza. |
| /S | Felhasználói beavatkozás nélkül csendes telepítést hajt végre. |

Ha például a `/?` paraméterrel szeretné futtatni a telepítőprogramot, írja be a következőt: **InstallDependencyAgent-Windows. exe/?** .

A Windows-függőségi ügynök fájljai alapértelmezés szerint telepítve vannak a *C:\Program Files\Microsoft Dependency Agent* . Ha a telepítés befejezése után a függőségi ügynök nem indul el, ellenőrizze a naplókat a részletes hibaüzenetek megtekintéséhez. A naplózási könyvtár a *%ProgramFiles%\Microsoft függőségi Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>A függőségi ügynök telepítése Linux rendszeren

A függőségi ügynök Linux-kiszolgálókra van telepítve a *InstallDependencyAgent-Linux64. bin*fájlból, amely egy önkicsomagoló bináris elemet tartalmazó rendszerhéj-parancsfájl. A fájlt `sh` használatával vagy a fájlhoz való végrehajtási engedélyek hozzáadásával futtathatja.

>[!NOTE]
> Az ügynök telepítéséhez vagy eltávolításához gyökérszintű hozzáférés szükséges.
>

| Paraméter | Leírás |
|:--|:--|
| – Súgó | A parancssori kapcsolók listájának lekérése. |
| -s | Beavatkozás nélküli telepítés a felhasználónak szóló üzenetek nélkül. |
| --ellenőrzése | Ellenőrizze a jogosultságokat és az operációs rendszer, de ne telepítse az ügynököt. |

A telepítőprogramnak a `-help` paraméterrel való futtatásához például írja be a **InstallDependencyAgent-Linux64. bin-help**parancsot.

Telepítse a Linux függőségi ügynököt root-ként a parancs futtatásával `sh InstallDependencyAgent-Linux64.bin`.

Ha a függőségi ügynök nem indul el, ellenőrizze a hibával kapcsolatos részletes információk a naplókat. Linux-ügynökök esetén a */var/opt/Microsoft/Dependency-Agent/log*a naplózási könyvtár.

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
* Manuálisan, a [Windows és a Linux teljesítmény adatforrásai](../../azure-monitor/platform/data-sources-performance-counters.md) című témakörben leírtak szerint log Analytics
* A [Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1) -katalógusból elérhető PowerShell-szkript letöltésével és futtatásával

## <a name="deploy-azure-monitor-for-vms"></a>Az Azure Monitor-beli virtuális gépek üzembe helyezése

Ez a módszer egy JSON-sablon, amely a megoldás-összetevőket a Log Analytics-munkaterület engedélyezése konfigurációját tartalmazza.

Ha nem tudja, hogyan helyezhet üzembe erőforrásokat sablon használatával, tekintse meg a következőt:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

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
                    }
                ]
            }
        ]
    }
    ```

1. Mentse ezt a fájlt *installsolutionsforvminsights. JSON* néven egy helyi mappába.

1. Rögzítse a *WorkspaceName*, a *ResourceGroupName*és a *WorkspaceLocation*értékeit. A *WorkspaceName* értéke a log Analytics munkaterület neve. A *WorkspaceLocation* értéke az a régió, amelyben a munkaterület definiálva van.

1. Készen áll a sablon üzembe helyezése a következő PowerShell-parancs használatával:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A konfiguráció módosítása több percet is igénybe vehet. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

    ```powershell
    provisioningState       : Succeeded
    ```
   Miután engedélyezte a figyelés, az állapot és a hibrid számítógép metrikák megtekintéséhez nagyjából 10 percet vehet igénybe.

## <a name="troubleshooting"></a>Hibakeresés

### <a name="vm-doesnt-appear-on-the-map"></a>A virtuális gép nem jelenik meg a térképen

Ha a függőségi ügynök telepítése sikeres volt, de a számítógép nem jelenik meg a térképen, a következő lépésekkel diagnosztizálhatja a problémát.

1. A függőségi ügynök sikeres telepítését? Ellenőrzi, hogy ha a szolgáltatás telepítve van és fut ellenőrizheti.

    **Windows**: keresse meg a "Microsoft függőségi ügynök" nevű szolgáltatást.

    **Linux**: keresse meg a "Microsoft-függőség – ügynök" futó folyamatot.

2. Ön a [log Analytics ingyenes díjszabási szintjére](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)vonatkozik? Az ingyenes csomag legfeljebb öt egyedi számítógépet tesz lehetővé. A további számítógépek nem jelennek meg a térképen, még akkor is, ha az előző öt már nem küld adatokat.

3. A számítógép napló-és teljesítményszámláló-adatokat küld Azure Monitor naplókba? Hajtsa végre a következő lekérdezést a számítógépen:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Egy vagy több eredményt adott vissza? Az adatok legutóbbi? Ha igen, a Log Analytics-ügynök megfelelően működik, és kommunikál a szolgáltatással. Ha nem, ellenőrizze az ügynököt a kiszolgálón: [log Analytics ügynök a Windows hibaelhárításához](../platform/agent-windows-troubleshoot.md) , vagy [log Analytics ügynök a Linux rendszerhez – hibaelhárítás](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>A számítógép megjelenik a térképen, de nem rendelkezik folyamatokkal

Ha látja a kiszolgálót a térképen, de nem rendelkezik folyamat-vagy adatszolgáltatással, akkor ez azt jelzi, hogy a függőségi ügynök telepítve van és fut, de a kernel-illesztőprogram nem töltődött be.

Ellenőrizze a C:\Program Files\Microsoft függőségi Agent\logs\wrapper.log fájlt (Windows) vagy /var/opt/microsoft/dependency-agent/log/service.log fájlt (Linux). Az utolsó sort jeleznie kell, miért nem töltődtek be a kernelbe. Például a kernel előfordulhat, hogy nem támogatja a linuxon futó Ha frissítette a kernel.


## <a name="next-steps"></a>Következő lépések

Most, hogy a figyelés engedélyezve van a virtuális gépek számára, ezek az információk a Azure Monitor for VMssal való elemzéshez érhetők el.

- A felderített alkalmazások függőségeinek megtekintéséhez lásd: [Azure monitor for VMS Térkép megtekintése](vminsights-maps.md).

- Az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md)a szűk keresztmetszetek és a virtuális gépek teljesítményének teljes kihasználtsága alapján:.

---
title: Az Azure Monitor engedélyezése hibrid környezetben | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan engedélyezi az Azure Monitor virtuális gépek egy hibrid felhőalapú környezetben, amely egy vagy több virtuális gépet tartalmaz.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480742"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Virtuális gépek Azure-figyelőjének engedélyezése hibrid környezetben

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk bemutatja, hogyan engedélyezheti az Azure Monitor virtuális gépek virtuális gépek hez az adatközpontban vagy más felhőbeli környezetben üzemeltetett fizikai számítógépek. A folyamat végén sikeresen meg kell kezdte figyelni a virtuális gépek et a környezetben, és megtudhatja, hogy teljesítmény- vagy rendelkezésre állási problémákat tapasztalnak-e.

Mielőtt elkezdenéd, tekintse át az [előfeltételeket,](vminsights-enable-overview.md) és ellenőrizze, hogy az előfizetés és az erőforrások megfelelnek-e a követelményeknek. Tekintse át a Log Analytics Linux és windows ügynök követelményeit és telepítési [módszereit.](../../log-analytics/log-analytics-agent-overview.md)

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Az Azure Monitor virtuális gépek leképezési függőségi ügynök nem továbbít semmilyen adatot magát, és nem igényel semmilyen módosítást a tűzfalak vagy portok. A térképadatokat a Log Analytics-ügynök mindig továbbítja az Azure Monitor szolgáltatásnak, akár közvetlenül, akár az [Operations Management Suite átjárón](../../azure-monitor/platform/gateway.md) keresztül, ha az informatikai biztonsági házirendek nem engedélyezik a hálózat számítógépeinek az internethez való csatlakozást.

A feladat végrehajtásának lépéseit a következőképpen foglaljuk össze:

1. Telepítse a Log Analytics-ügynököt Windows vagy Linux rendszerre. Az ügynök telepítése előtt tekintse át a [Log Analytics-ügynök áttekintéséről](../platform/log-analytics-agent.md) szóló cikket a rendszer előfeltételeinek és telepítési módszereinek megértéséhez.

2. Töltse le és telepítse az Azure Monitor for VMs Map Dependency Agent for [Windows](https://aka.ms/dependencyagentwindows) vagy [Linux](https://aka.ms/dependencyagentlinux).

3. Engedélyezze a teljesítményszámlálók gyűjtését.

4. Telepítse az Azure Monitor virtuális gépekhez.

>[!NOTE]
>A függőségi ügynök üzembe helyezésére vonatkozó ebben a cikkben ismertetett információk a [Service Map megoldásra](service-map.md)is vonatkoznak.  

## <a name="install-the-dependency-agent-on-windows"></a>A függőségi ügynök telepítése windowsos windowsos rendszerre

A függőségi ügynököt manuálisan is `InstallDependencyAgent-Windows.exe`telepítheti windowsos számítógépekre a futva. Ha a végrehajtható fájlt beállítások nélkül futtatja, akkor egy telepítővarázslót indít el, amelyet követhet az ügynök interaktív telepítéséhez.

>[!NOTE]
>Az ügynök telepítéséhez vagy eltávolításához *rendszergazdai* jogosultságok szükségesek.

Az alábbi táblázat kiemeli azokat a paramétereket, amelyeket az ügynök telepítője a parancssorból támogat.

| Paraméter | Leírás |
|:--|:--|
| /? | A parancssori kapcsolók listáját adja vissza. |
| /S | Csendes telepítést hajt végre felhasználói beavatkozás nélkül. |

Ha például a telepítőprogramot `/?` a paraméterrel szeretné futtatni, írja be az **InstallDependencyAgent-Windows.exe /?**.

A Windows függőségi ügynök fájljai alapértelmezés szerint a *C:\Program Files\Microsoft Dependency Agent* mappába települnek. Ha a függőségi ügynök nem indul el a telepítés befejezése után, ellenőrizze a naplókban a részletes hibainformációkat. A naplókönyvtár *a következő: %Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Telepítse a függőségi ügynököt Linuxra

A függőségi ügynök az *InstallDependencyAgent-Linux64.bin*linuxos kiszolgálókra van telepítve, amely egy önkicsomagoló bináris rendszerű rendszerhéj-parancsfájl. A fájlt futtathatja, `sh` vagy végrehajtási engedélyeket adhat hozzá magához a fájlhoz.

>[!NOTE]
> Az ügynök telepítéséhez vagy eltávolításához gyökérszintű hozzáférés szükséges.
>

| Paraméter | Leírás |
|:--|:--|
| -segítség | A parancssori kapcsolók listájának lekérése. |
| -s | Beavatkozás nélküli telepítés a felhasználónak szóló üzenetek nélkül. |
| --ellenőrzés | Ellenőrizze az engedélyeket és az operációs rendszert, de ne telepítse az ügynököt. |

Ha például a telepítőprogramot `-help` a paraméterrel szeretné futtatni, írja be az **InstallDependencyAgent-Linux64.bin -help**.

Telepítse a Linux függőségi ügynököt `sh InstallDependencyAgent-Linux64.bin`gyökérként a parancs futtatásával.

Ha a függőségi ügynök nem indul el, ellenőrizze a naplókat a részletes hibainformációkért. Linux ügynökök, a naplókönyvtár */var/opt/microsoft/dependency-agent/log*.

A függőségi ügynök fájljai a következő könyvtárakba kerülnek:

| Fájlok | Hely |
|:--|:--|
| Alapvető fájlok | /opt/microsoft/dependency-agent |
| Naplófájlok | /var/opt/microsoft/dependency-agent/log |
| Konfigurációs fájlok | /etc/opt/microsoft/dependency-agent/config |
| Szolgáltatás végrehajtható fájljai | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Bináris tárolófájlok | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Telepítési példaszkriptek

A függőségi ügynök egyszerű üzembe helyezéséhez egyszerre több kiszolgálón, a következő parancsfájl példa áll rendelkezésre a függőségi ügynök letöltéséhez és telepítéséhez Windows vagy Linux rendszeren.

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

## <a name="desired-state-configuration"></a>Célállapot-konfiguráció

A függőségi ügynök kívánt állapotkonfigurációval (DSC) történő telepítéséhez használhatja az xPSDesiredStateConfiguration modult a következő példakóddal:

```powershell
configuration VMInsights {

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

Ha a megoldás által hivatkozott Log Analytics-munkaterület még nincs konfigurálva a megoldás által igényelt teljesítményszámlálók gyűjtésére, engedélyeznie kell őket. Ezt kétféleképpen teheti meg:
* Manuálisan, a Hogy a [Windows és a Linux teljesítményadatforrások a Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Az [Azure PowerShell-galériából](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1) elérhető PowerShell-parancsfájl letöltésével és futtatásával

## <a name="deploy-azure-monitor-for-vms"></a>Az Azure Monitor telepítése virtuális gépekhez

Ez a módszer tartalmaz egy JSON-sablont, amely meghatározza a megoldás-összetevők a Log Analytics-munkaterületen engedélyezéséhez szükséges konfigurációt.

Ha nem tudja, hogyan helyezheti üzembe az erőforrásokat sablon használatával, olvassa el az:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)
* [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Az Azure CLI használatához először telepítenie kell és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.27-es vagy újabb verzióját kell futtatnia. A verzió azonosításához `az --version`futtassa a futtassa a futtassa a futtassa a futtassa Az Azure CLI telepítéséhez vagy frissítéséhez olvassa [el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

### <a name="create-and-execute-a-template"></a>Sablon létrehozása és végrehajtása

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
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Mentse ezt a fájlt *installsolutionsforvminsights.json* fájlba egy helyi mappába.

1. Rögzítse a *WorkspaceName*, *ResourceGroupName*és *WorkspaceLocation*értékét. A *WorkspaceName* értéke a Log Analytics-munkaterület neve. A *WorkspaceLocation* értéke az a terület, amelyben a munkaterület definiálva van.

1. Készen áll a sablon üzembe helyezésére a következő PowerShell-paranccsal:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A konfiguráció módosítása néhány percet is igénybe vehet. Amikor elkészült, egy üzenet a következőhöz hasonló üzenet jelenik meg, és tartalmazza az eredményt:

    ```powershell
    provisioningState       : Succeeded
    ```
   Miután engedélyezte a figyelést, körülbelül 10 percet is igénybe vehet, mielőtt megtekintheti a hibrid számítógép állapotát és metrikáit.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="vm-doesnt-appear-on-the-map"></a>A virtuális gép nem jelenik meg a térképen

Ha a függőségi ügynök telepítése sikeres volt, de a számítógép nem látható a térképen, diagnosztizálja a problémát az alábbi lépések végrehajtásával.

1. Sikeresen telepítve van a függőségi ügynök? Ezt ellenőrizheti, ha ellenőrzi, hogy a szolgáltatás telepítve van-e és fut-e.

    **Windows**: Keresse meg a "Microsoft függőségi ügynök" nevű szolgáltatást.

    **Linux**: Keresse meg a futó folyamat "microsoft-függőség-ügynök."

2. A Log [Analytics ingyenes tarifacsomagját élvezi?](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Az ingyenes csomag lehetővé teszi akár öt egyedi számítógépek. A további számítógépek nem jelennek meg a térképen, még akkor sem, ha az előző öt már nem küld adatokat.

3. A számítógép napló- és perf adatokat küld az Azure Monitor naplóknak? A következő lekérdezés végrehajtása a számítógépen:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Egy vagy több eredményt adott vissza? Az adatok frissek? Ha igen, a Log Analytics-ügynök megfelelően működik, és kommunikál a szolgáltatással. Ha nem, ellenőrizze az ügynököt a kiszolgálón: [Log Analytics ügynök A Windows hibaelhárítása](../platform/agent-windows-troubleshoot.md) vagy Log Analytics ügynök Linux [hibaelhárítás](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>A számítógép megjelenik a térképen, de nincsenek folyamatai

Ha látja a kiszolgálót a térképen, de nem rendelkezik folyamat- vagy kapcsolatadatokkal, az azt jelzi, hogy a függőségi ügynök telepítve van és fut, de a kernelillesztőprogram nem töltődik be.

Ellenőrizze a C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log fájlt (Windows) vagy a /var/opt/microsoft/dependency-agent/log/service.log fájlt (Linux). A fájl utolsó soraiban meg kell jelölnie, hogy miért nem töltődik be a kernel. Előfordulhat például, hogy a rendszermag nem támogatott Linuxon, ha frissítette a rendszermagot.


## <a name="next-steps"></a>További lépések

Most, hogy a figyelés engedélyezve van a virtuális gépek, ez az információ az Azure Monitor virtuális gépek hez érhető el.

- A felderített alkalmazásfüggőségek megtekintéséhez olvassa el [az Azure-figyelő megtekintése virtuális gépekleképezéséhez.](vminsights-maps.md)

- A szűk keresztmetszetek és a virtuális gép teljesítményének általános kihasználásának azonosításáról az [Azure virtuális gép teljesítményének megtekintése](vminsights-performance.md)című témakörben nyújt témakört.

---
title: A hibrid környezetben az Azure Monitor (előzetes verzió) engedélyezése |} A Microsoft Docs
description: Ez a cikk leírja, hogyan lehetővé az Azure Monitor-beli virtuális gépek a hibridfelhős környezet, amely egy vagy több virtuális gépet tartalmaz.
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
ms.date: 06/07/2019
ms.author: magoedte
ms.openlocfilehash: bc26cc0654aac9416bf31ffccf426648e3a8b8d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122535"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Az Azure Monitor-beli virtuális gépek (előzetes verzió) engedélyezése hibrid környezetben

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk azt ismerteti, hogyan engedélyezése az Azure Monitor-beli virtuális gépek (előzetes verzió) a virtuális gépek vagy az adatközpontban vagy egyéb felhőalapú környezetben futó fizikai számítógépek. Ez a folyamat végén már fog sikeresen elkezdte a környezetében a virtuális gépek figyelése, és ismerje meg, ha a teljesítménybeli vagy rendelkezésre állási problémákat tapasztal. 

Mielőtt elkezdené, mindenképpen tekintse át a [Előfeltételek](vminsights-enable-overview.md) , és ellenőrizze, hogy az előfizetés és az erőforrások megfelelnek-e a követelményeknek. Tekintse át a követelményeket és a központi telepítési módszerek a [Log Analytics Linux és Windows-ügynök](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Az Azure Monitor, virtuális gépek térkép függőségi ügynök maga adatokat nem továbbít, és nincs szükség tűzfalak és a portok módosítása. A térképadatok mindig továbbítása a Log Analytics-ügynököket, az Azure Monitor szolgáltatásba, vagy közvetlenül vagy keresztül a [Operations Management Suite-átjárót](../../azure-monitor/platform/gateway.md) , ha az informatikai biztonsági szabályzatok a hálózaton lévő számítógépek tiltása Csatlakozzon az internethez.

A lépéseket a feladat végrehajtásához a következőképpen lehet összefoglalni:

1. A Log Analytics-ügynök telepítése Windows vagy Linux. Az ügynök telepítése előtt tekintse át a [Log Analytics-ügynök – áttekintés](../platform/log-analytics-agent.md) a cikkből megismerheti a rendszerkövetelmények és telepítési módszerek.

2. Töltse le és telepítse az Azure Monitor, a virtuális gépek térkép függőségi ügynök [Windows](https://aka.ms/dependencyagentwindows) vagy [Linux](https://aka.ms/dependencyagentlinux).

3. A teljesítményszámlálók gyűjtésének engedélyezéséhez.

4. Az Azure Monitor-beli virtuális gépek üzembe helyezése.

## <a name="install-the-dependency-agent-on-windows"></a>Telepítse a függőségi ügynököt Windows
Telepítheti a függőségi ügynök manuálisan a Windows-számítógépeken futó `InstallDependencyAgent-Windows.exe`. Ha a végrehajtható fájl kapcsolók nélkül futtatja, a telepítővarázsló, amelyekkel az ügynök telepítéséhez interaktív módon elindítja.

>[!NOTE]
>*Rendszergazdai* jogosultság szükséges telepíteni, vagy távolítsa el az ügynököt.

Az alábbi táblázat a paramétereket, az ügynök a parancssorból a telepítő által támogatott emeli ki.

| Paraméter | Leírás |
|:--|:--|
| /? | A parancssori kapcsolók listáját adja vissza. |
| /S | Felhasználói beavatkozás nélkül csendes telepítést hajt végre. |

Ha például az a telepítőprogram futtatásához a `/?` paramétert, adja meg **InstallDependencyAgent-Windows.exe /?** .

A Windows a függőségi ügynök fájlok vannak telepítve a *C:\Program Files\Microsoft függőségi ügynök* alapértelmezés szerint. Ha nem sikerül a telepítés befejezése után indítsa el a függőségi ügynök, tekintse meg a hibával kapcsolatos részletes információk a naplókat. A naplózási könyvtár *%Programfiles%\Microsoft függőségi Agent\logs*.

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

Ha például az a telepítőprogram futtatása a `-help` paramétert, adja meg **InstallDependencyAgent-Linux64.bin-súgó**.

A Linux függőségi ügynök telepítése a legfelső szintű parancs futtatásával `sh InstallDependencyAgent-Linux64.bin`.

Ha a függőségi ügynök nem indul el, ellenőrizze a hibával kapcsolatos részletes információk a naplókat. A Linux-ügynökök, a naplózási könyvtár van */var/opt/microsoft/dependency-agent/log*.

A függőségi ügynök fájlok kerülnek a következő könyvtárak:

| Fájlok | Hely |
|:--|:--|
| Alapvető fájlok | /opt/microsoft/dependency-agent |
| Naplófájlok | /var/opt/microsoft/dependency-agent/log |
| Konfigurációs fájlok | /etc/opt/microsoft/dependency-agent/config |
| Szolgáltatás végrehajtható fájljai | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Bináris tárolófájlok | /var/opt/microsoft/dependency-agent/storage |

## <a name="enable-performance-counters"></a>Teljesítményszámlálók engedélyezése
Ha a Log Analytics-munkaterületet a megoldás által hivatkozott már nincs konfigurálva a megoldás által igényelt a teljesítményszámlálók adatainak összegyűjtése, meg kell engedélyezheti őket. Ezt a két módszer egyikével teheti meg:
* Leírtak szerint manuálisan [a Log Analytics Windows és Linux rendszerű teljesítmény adatforrások](../../azure-monitor/platform/data-sources-performance-counters.md)
* Letöltésével és futtatásával egy PowerShell-parancsprogram, amely elérhető a [Azure PowerShell-galéria](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Az Azure Monitor-beli virtuális gépek üzembe helyezése
Ez a módszer egy JSON-sablon, amely a megoldás-összetevőket a Log Analytics-munkaterület engedélyezése konfigurációját tartalmazza.

Ha nem tudja, hogyan az erőforrások üzembe helyezése sablon használatával, lásd:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Az Azure parancssori felület használatához először telepítése és a parancssori Felületet helyileg használja. Kell futnia az Azure CLI 2.0.27-es vagy újabb. A verzió azonosításához futtassa `az --version`. Telepítse vagy frissítse az Azure CLI, lásd: [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

1. Rögzítheti a tartozó értékeket *WorkspaceName*, *ResourceGroupName*, és *WorkspaceLocation*. Az érték *WorkspaceName* Log Analytics-munkaterület neve. Az érték *WorkspaceLocation* a régió, a munkaterület van definiálva.

1. Készen áll a sablon üzembe helyezése a következő PowerShell-parancs használatával:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Amikor elkészült, egy üzenet jelenik meg, amely a következő példához hasonló, és az eredmény tartalmazza:

    ```powershell
    provisioningState       : Succeeded
    ```
   Miután engedélyezte a figyelés, az állapot és a hibrid számítógép metrikák megtekintéséhez nagyjából 10 percet vehet igénybe.

## <a name="next-steps"></a>További lépések

Most, hogy a virtuális gépek számára engedélyezve van a figyelés és az Azure Monitor-beli virtuális gépek elemzési érhető el ezt az információt.
 
- Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [a virtuális gépek állapotának megtekintése az Azure Monitor](vminsights-health.md).
- Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).
- Azonosíthatja a szűk keresztmetszeteket és a virtuális gép teljesítményét teljes kihasználtság, lásd: [megtekintése az Azure virtuális gép teljesítménye](vminsights-performance.md).
- Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).
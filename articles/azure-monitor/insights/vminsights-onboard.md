---
title: Virtuális gépek (előzetes verzió) üzembe helyezése az Azure Monitor |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan telepíti, és az Azure Monitor konfigurálása a virtuális gépek, így ismertetése, hogyan működik az elosztott alkalmazás és milyen állapotbeli problémák azonosított.
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
ms.date: 12/07/2018
ms.author: magoedte
ms.openlocfilehash: 3c1caa2485437768781ada2c7271445ccd3c19e1
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190328"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Üzembe helyezése az Azure Monitor-beli virtuális gépek (előzetes verzió)
Ez a cikk ismerteti, hogyan állítható be az Azure Monitor-beli virtuális gépek. A szolgáltatás figyeli az Azure-beli virtuális gépek (VM) és a virtuálisgép-méretezési csoportok és a környezetében a virtuális gépek operációs rendszer állapotát. A monitorozás tartalmaz, a felderítés és az alkalmazás függőségekkel is üzemelhet őket leképezése. 

Engedélyezi az Azure Monitor-beli virtuális gépek a következő módszerek egyikével:

* Egy Azure virtuális gépen engedélyezze kiválasztásával **Insights (előzetes verzió)** közvetlenül a virtuális gépről.
* Két vagy több Azure virtuális gépek az Azure Policy használatával engedélyezheti. Ezzel a módszerrel a szükséges függőségek a meglévő és új virtuális gép telepítve van és megfelelően konfigurálva. Nem megfelelő virtuális gépek jelenti, hogy lehetősége engedélyezheti őket, és elháríthatja a módját.
* Engedélyezzen két vagy több Azure virtuális gépek vagy virtuálisgép-méretezési csoportok között egy adott előfizetésen vagy erőforráscsoporton PowerShell használatával.

További információ az egyes módszerek van megadva a cikk későbbi részében.

## <a name="prerequisites"></a>Előfeltételek
A Kezdés előtt győződjön meg arról, hogy tisztában van-e az alábbi szakaszokban található információk.

### <a name="log-analytics"></a>Log Analytics

Log Analytics-munkaterület jelenleg a következő régiókban támogatott:

- USA nyugati középső régiója
- USA keleti régiója
- Nyugat-Európa
- Délkelet-Ázsia<sup>1</sup>

<sup>1</sup> ebben a régióban jelenleg nem támogatja a Azure monitor az állapotfigyelő szolgáltatás virtuális gépek számára.

>[!NOTE]
>Az Azure virtual machines-régióból kezdeményezett telepíthetők, és nem csak a támogatott régiók, a Log Analytics-munkaterületen.
>

Ha nem rendelkezik egy munkaterületet, létrehozhat egyet az alábbi módszerek egyikével:
* [Az Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)

Ha engedélyezi az Azure Portalon egy Azure virtuális gép figyelése, létrehozhat egy munkaterületet, a folyamat során.

Az ipari méretekben a forgatókönyvben a megoldás engedélyezéséhez előbb konfigurálja a következőket a Log Analytics-munkaterület:

* Telepítse a ServiceMap és InfrastructureInsights megoldásokat. A telepítést csak ebben a cikkben megadott egy Azure Resource Manager-sablon használatával.
* Konfigurálja a teljesítményszámlálók adatainak összegyűjtése a Log Analytics-munkaterületet.

Az ipari méretekben a forgatókönyvhöz a munkaterület beállítása: [állítsa be a Log Analytics-munkaterület ipari méretekben üzembe helyezésének](#setup-log-analytics-workspace).

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az alábbi táblázat a virtuális gépek az Azure monitorban támogatott Windows- és Linux operációs rendszerek listája. Ebben a szakaszban később nyújtott teljes listáját, amelyek a fő- és alverzió Linux operációsrendszer-kiadás részleteit, és a kernel verziója támogatott.

|Operációs rendszer verziója |Teljesítmény |Maps |Állapot |
|-----------|------------|-----|-------|
|A Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |
|Windows Server 2012 R2 | X | X | |
|Windows Server 2012 | X | X | |
|Windows Server 2008 R2 | X | X| |
|Red Hat Enterprise Linux (RHEL) 7, 6| X | X| X |
|Ubuntu 18.04, 16.04, 14.04 | X | X | X |
|CentOS Linux 7, 6 | X | X | X |
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |
|Oracle Linux 7 | X<sup>1</sup> | | X |
|Oracle Linux 6 | X | X | X |
|Debian 9.4, 8 | X<sup>1</sup> | | X |

<sup>1</sup> a teljesítmény funkció az Azure Monitor-beli virtuális gépek csak az Azure Monitor érhető el. Nem érhető el, közvetlenül a bal oldali ablaktáblán az Azure virtuális gépek használatakor.

>[!NOTE]
>Az alábbi adatokat a következőkre vonatkozik a Linux operációs rendszer támogatja:
> - Csak az alapértelmezett és az SMP Linux kernelű kiadások támogatottak.
> - Nestandardní kernel kiadások, mint például a Xen, és a fizikai cím bővítmény (fizikai) nem támogatottak az minden olyan Linux-disztribúció. A kiadási karakterláncot, például egy rendszer *2.6.16.21-0.8-xen* nem támogatott.
> - Egyéni kernelekkel, többek között a standard szintű kernelekkel, újrafordítások nem támogatottak.
> - CentOSPlus kernel nem támogatott.

#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| Operációs rendszer verziója | Kernel verziója |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15. * |
| Ubuntu 16.04.3 | kernel 4.15. * |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 6 szoros vállalati kernel
| Operációs rendszer verziója | Kernel verziója
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4 | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Oracle Enterprise Linux 5 szoros vállalati kernel

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 vállalati kiszolgáló

| Operációs rendszer verziója | Kernel verziója
|:--|:--|
|12 SP2 | 4.4. * |
|12 SP3 | 4.4. * |

### <a name="the-microsoft-dependency-agent"></a>A Microsoft Dependency agent
Virtuális gépek térkép funkció az Azure Monitor az adatok lekérése a Microsoft Dependency agent. A függőségi ügynök a Log Analytics-ügynököket a Log Analytics-kapcsolat támaszkodik. Ezért a rendszer a Log Analytics-ügynököket telepíteni és konfigurálni a függőségi ügynök kell rendelkeznie.

Engedélyezi az Azure Monitor-beli virtuális gépek egy Azure virtuális Gépen, vagy az ipari méretekben üzembe helyezési módszert használja, a felhasználói élményt részeként az ügynök telepítése az Azure virtuális gép függőségi ügynök bővítmény használatára van szükség.

Hibrid környezetben töltse le, és a függőségi ügynök telepítése a két módszer egyikével: Manuálisan, vagy egy automatikus telepítési módszer használatával a virtuális gépek, amelyek Azure-on kívülről üzemelnek.

A következő táblázat ismerteti a térkép funkció támogatja a hibrid környezetben összekapcsolt forrásokról.

| Csatlakoztatott forrás | Támogatott | Leírás |
|:--|:--|:--|
| Windows-ügynökök | Igen | Mellett a [Log Analytics-ügynököket for Windows](../../azure-monitor/platform/log-analytics-agent.md), Windows-ügynökök a Microsoft Dependency agent szükséges. Operációs rendszerek teljes listáját lásd: [támogatott operációs rendszerek](#supported-operating-systems). |
| Linux-ügynökök | Igen | Mellett a [Linuxhoz készült Log Analytics-ügynök](../../azure-monitor/platform/log-analytics-agent.md), Linux-ügynökök a Microsoft Dependency agent szükséges. Operációs rendszerek teljes listáját lásd: [támogatott operációs rendszerek](#supported-operating-systems). |
| System Center Operations Manage felügyeleti csoport | Nem | |

A függőségi ügynök letölthető a következő helyekről:

| Fájl | Operációs rendszer | Verzió | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés
Engedélyezze, és az Azure monitorban funkcióhoz férhet hozzá a virtuális gépek kell a következő hozzáférési szerepköröket hozzárendelni:

- A megoldás engedélyezéséhez rendelkeznie kell a *Log Analytics-közreműködő* szerepkör.

- Teljesítmény, egészségügyi, megtekintése, és adatokat, rendelkeznie kell a *Monitoring Reader* az Azure virtuális gép szerepkör. A Log Analytics-munkaterület-beli virtuális gépek az Azure Monitor kell konfigurálni.

A Log Analytics-munkaterülethez való hozzáférésének kapcsolatos további információkért lásd: [munkaterületeinek kezeléséhez](../../azure-monitor/platform/manage-access.md).

## <a name="enable-monitoring-in-the-azure-portal"></a>Engedélyezze a monitorozást az Azure Portalon
Ha engedélyezni szeretné az Azure Portalon az Azure Virtuálisgép-monitorozási, tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki **virtuális gépek**.

1. Válasszon ki egy virtuális gépet a listából.

1. A virtuális gép lapon található a **figyelés** szakaszban jelölje be **Insights (előzetes verzió)**.

1. Az a **Insights (előzetes verzió)** lapon jelölje be **kipróbálása**.

    ![A virtuális gépek az Azure Monitor engedélyezése a virtuális gép](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)
1. Az a **Azure Monitor Insights felvételi** lapon, ha rendelkezik egy meglévő Log Analytics munkaterület ugyanabban az előfizetésben, válassza a legördülő listában.  
    A lista preselects az alapértelmezett munkaterületre és a helyre, amely a virtuális gép telepítve van az előfizetésben. 

    >[!NOTE]
    >Ha azt szeretné, a virtuális gépről a monitorozási adatok tárolására szolgáló új Log Analytics-munkaterület létrehozásához kövesse a [hozzon létre egy Log Analytics-munkaterület](../../azure-monitor/learn/quick-create-workspace.md) a támogatott régiók egyikében korábban felsorolt.

Miután engedélyezte a figyelés, előtt megtekintheti a mérőszámok a virtuális gép körülbelül 10 percet vehet igénybe.

![Az Azure Monitor engedélyezése a virtuális gépek figyelése a központi telepítés feldolgozása](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>Üzembe helyezés
Ebben a szakaszban, üzembe helyezés az Azure Monitor-beli virtuális gépek vagy az Azure Policy, vagy az Azure PowerShell használatával.

A virtuális gépek üzembe helyezése, előtt előre konfigurálhatja a Log Analytics-munkaterület az alábbiak szerint:

1. Ha még nem rendelkezik egy munkaterületet, hozzon létre egyet az Azure Monitor által támogatott virtuális gépek számára.  
    Mielőtt folytatná, tekintse meg a [munkaterületeinek kezeléséhez](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json) költség, felügyeleti és megfelelőségi szempontok megismerése.

1. Hozzon létre egy új munkaterületen, ha egy még nem létezik, amely használható az Azure Monitor virtuális gépek támogatásához. Felülvizsgálat [munkaterületeinek kezeléséhez](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json) egy új munkaterületet, a költség, felügyeleti és megfelelőségi szempontokat a folytatás előtt létrehozása előtt.

1. Engedélyezze a teljesítményszámlálók a munkaterületen, a Linux és Windows virtuális gépeken a gyűjteményhez.

1. Telepítse, és engedélyezze a ServiceMap és InfrastructureInsights megoldást a munkaterületén.

### <a name="set-up-a-log-analytics-workspace"></a>Log Analytics-munkaterület beállítása
Ha a Log Analytics-munkaterület nincs, hozzon létre egyet a módszereket, a javasolt áttekintésével a ["Előfeltételek"](#log-analytics) szakaszban.

#### <a name="enable-performance-counters"></a>Teljesítményszámlálók engedélyezése
Ha a Log Analytics-munkaterületet a megoldás által hivatkozott már nincs konfigurálva a megoldás által igényelt a teljesítményszámlálók adatainak összegyűjtése, meg kell engedélyezheti őket. Ezt két módon teheti meg:
* Leírtak szerint manuálisan [a Log Analytics Windows és Linux rendszerű teljesítmény adatforrások](../../azure-monitor/platform/data-sources-performance-counters.md)
* Letöltésével és futtatásával egy PowerShell-parancsprogram, amely elérhető a [Azure PowerShell-galéria](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>Telepítse a ServiceMap és InfrastructureInsights megoldásokat
Ez a módszer egy JSON-sablon, amely a megoldás-összetevőket a Log Analytics-munkaterület engedélyezése konfigurációját tartalmazza.

Ha még nem ismeri a sablon segítségével üzembe helyezni erőforrásokat, lásd:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Kell futnia az Azure CLI 2.0.27-es vagy újabb. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

1. Értékeinek szerkesztéséhez *WorkspaceName*, *ResourceGroupName*, és *WorkspaceLocation*. Az érték *WorkspaceName* a Log Analytics-munkaterületet, amely tartalmazza a következő munkaterület nevének teljes erőforrás-Azonosítójára van. Az érték *WorkspaceLocation* a régió, a munkaterület van definiálva.

1. Készen áll a sablon üzembe helyezése a következő PowerShell-parancs használatával:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

    ```powershell
    provisioningState       : Succeeded
    ```

### <a name="enable-by-using-azure-policy"></a>Az Azure Policy segítségével
Az Azure Monitor engedélyezése a virtuális gépek nagy mennyiségű oly módon, hogy elősegíti a következetes megfelelőség és az újonnan létrehozott virtuális gépek automatikus engedélyezése, javasoljuk, hogy [Azure Policy](../../azure-policy/azure-policy-introduction.md). Ezek a házirendek:

* A Log Analytics-ügynököket és a függőségi ügynök üzembe helyezése.
* Megfelelési eredmények jelentést.
* Javítsa a nem kompatibilis virtuális gépek.

Az Azure Monitor engedélyezése a virtuális gépek a bérlő Azure Policy használatával:

- A kezdeményezés hozzárendelése egy hatókör: felügyeleti csoport, előfizetéshez vagy erőforráscsoporthoz
- Tekintse át és szervizelése megfelelőségi eredményeit

Azure Policy hozzárendelésével kapcsolatos további információkért lásd: [Azure Policy – áttekintés](../../governance/policy/overview.md#policy-assignment) , és tekintse át a [felügyeleti csoportok áttekintése](../../governance/management-groups/index.md) a folytatás előtt.

A szabályzatdefiníciók a következő táblázatban láthatók:

|Name (Név) |Leírás |Típus |
|-----|------------|-----|
|[Előzetes verzió]: Az Azure Monitor-beli virtuális gépek engedélyezése |Az Azure Monitor engedélyezése a virtuális gépek (VM) a megadott hatókör (felügyeleti csoport, előfizetéshez vagy erőforráscsoportot). Log Analytics-munkaterületet használja paraméterként. |Kezdeményezés |
|[Előzetes verzió]: Függőségi ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása |Jelentések virtuális gépek nem megfelelő, ha a virtuális gép rendszerkép (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|[Előzetes verzió]: Log Analytics-ügynök telepítése – virtuális gép rendszerkép (OS) listán nem szereplő naplózása |Jelentések virtuális gépek nem megfelelő, ha a virtuális gép rendszerkép (OS) nincs definiálva a listában, és az ügynök nincs telepítve. |Szabályzat |
|[Előzetes verzió]: Függőségi ügynök Linux rendszerű virtuális gépek üzembe helyezése |Függőségi ügynök telepítése Linux rendszerű virtuális gépekhez a virtuális gép (OS) rendszerkép definiálása a listában, és az ügynök nincs telepítve. |Szabályzat |
|[Előzetes verzió]: Függőségi ügynök Windows-beli virtuális gépek üzembe helyezése |Ha a virtuális gép rendszerkép (OS) van definiálva, a listában, és az ügynök nincs telepítve, telepítse a függőségi ügynököt Windows virtuális. |Szabályzat |
|[Előzetes verzió]: Log Analytics-ügynök Linux rendszerű virtuális gépek üzembe helyezése |Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépekhez a virtuális gép (OS) rendszerkép definiálása a listában, és az ügynök nincs telepítve. |Szabályzat |
|[Előzetes verzió]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése |Log Analytics ügynök Windows virtuális gépek üzembe helyezése, ha a virtuális gép rendszerkép (OS) van definiálva, a listában, és az ügynök nincs telepítve. |Szabályzat |

(Nem tartalmazza a kezdeményezés-) önálló házirend az alábbiakban ismertetjük:

|Name (Név) |Leírás |Típus |
|-----|------------|-----|
|[Előzetes verzió]: Naplózási Log Analytics-munkaterület VM - jelentés eltérés |Virtuális gépek jelentést nem megfelelő, ha azok nem naplózását a házirend/kezdeményezés-hozzárendelést a megadott Log Analytics-munkaterületen. |Szabályzat |

#### <a name="assign-the-azure-monitor-initiative"></a>Az Azure Monitor kezdeményezés hozzárendelése
A jelen kezdeti kiadás csak az Azure Portalon hozhat létre a szabályzat-hozzárendelés. Megtudhatja, hogyan hajtsa végre ezeket a lépéseket, tekintse meg [szabályzat-hozzárendelés létrehozása az Azure Portalról](../../governance/policy/assign-policy-portal.md).

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon, válassza a **minden szolgáltatás**, majd keresse meg és válassza a **házirend**.

1. Az Azure Policy oldalára bal oldali panelén válassza **hozzárendelések**.  
    A hozzárendelés egy olyan szabályzat, amely egy adott hatókörön belül érvényes.
    
1. Felső részén a **szabályzat – hozzárendelések** lapon jelölje be **kezdeményezés hozzárendelése**.

1. Az a **kezdeményezés hozzárendelése** lapon válassza ki a **hatókör** , kattintson a három pontra (...), és válassza ki a felügyeleti csoportot vagy előfizetést.  
    Ebben a példában egy hatókör korlátozza a szabályzat-hozzárendelést a csoportosított kényszerítésre kijelölt virtuális gépek.
    
1. Alsó részén a **hatókör** lapon mentse a módosításokat kiválasztásával **kiválasztása**.

1. (Nem kötelező) A hatókör egy vagy több erőforrás eltávolításához jelölje ki **kizárások**.

1. Válassza ki a **kezdeményezésdefiníció** három pontra (...) elérhető definíciók listájának megjelenítéséhez jelölje ki  **[Előzetes verzió] engedélyezése az Azure Monitor-beli virtuális gépek**, majd válassza ki a  **Válassza ki**.  
    A **hozzárendelés neve** mezőt automatikusan kitölti a kiválasztott kezdeményezési nevű, de ezt módosíthatja. Az opcionális leírást is hozzáadhat. A **által hozzárendelt** mezőt automatikusan kitölti éppen bejelentkezett alapján, és ezt az értéket nem kötelező.
    
1. Az a **Log Analytics-munkaterület** legördülő listában a támogatott régió, válassza ki egy munkaterületet.

    >[!NOTE]
    >Ha a munkaterület a hozzárendelés hatókörén kívül esik, biztosítson *Log Analytics-közreműködő* engedélyeket a szabályzat-hozzárendelés egyszerű azonosító. Ha nem ezt teszi, mint például láthatja az üzembe helyezési hibák: `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... ` Tekintse át a hozzáférést, [hogyan konfigurálhatja manuálisan a felügyelt identitás](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
    >  
    A **felügyelt identitás** jelölőnégyzet be van jelölve, a kezdeményezés érvényessége a felhasználóhoz egy házirend tartalmazza a *deployIfNotExists* érvénybe.
    
1. Az a **identitás kezelése hely** legördülő listára, válassza ki a megfelelő régiót.

1. Válassza a **Hozzárendelés** elemet.

#### <a name="review-and-remediate-the-compliance-results"></a>Tekintse át és szervizelése megfelelőségi eredményeit

Megismerheti a megfelelőségi eredmények áttekintéséhez olvassa el [azonosíthatja a meg nem felelés eredmények](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources). A bal oldali panelen válassza ki a **megfelelőségi**, majd keresse meg a  **[Előzetes verzió] engedélyezése az Azure Monitor-beli virtuális gépek** kezdeményezés-beli virtuális gépek nem megfelelő, a hozzárendelés alapján Ön létrehozott.

![Szabályzatoknak való megfelelés, Azure-beli virtuális gépek](./media/vminsights-onboard/policy-view-compliance-01.png)

A kezdeményezés található házirendek eredményei alapján a virtuális gépek jelentett nem megfelelő a következő esetekben:

* A log Analytics vagy a függőségi ügynök nincs telepítve.  
    Ebben a forgatókönyvben jellemző, a meglévő virtuális gépek hatókör. Csökkentése érdekében, hogy, üzembe helyezése a szükséges ügynökök által [szervizelési feladatokat hozhat létre](../../governance/policy/how-to/remediate-resources.md) lévő nem megfelelő házirendhez.  
    - [Előzetes verzió]: Deploy Dependency Agent for Linux VMs
    - [Előzetes verzió]: Deploy Dependency Agent for Windows VMs
    - [Előzetes verzió]: Deploy Log Analytics Agent for Linux VMs
    - [Előzetes verzió]: Deploy Log Analytics Agent for Windows VMs

* VM-lemezkép (OS) nem azonosítja a szabályzat-definícióban.  
    A feltételek a központi telepítésre vonatkozó házirendet, például csak jól ismert Azure Virtuálisgép-rendszerképekből telepített virtuális gépekkel. Ellenőrizze a dokumentációban, megtekintéséhez, hogy támogatott-e a virtuális gép operációs rendszerének. Nem támogatott, ha a központi telepítésre vonatkozó házirendet és a frissítés ismétlődő, vagy módosítsa azt, hogy a rendszerkép megfelelő legyen.  
    - [Előzetes verzió]: Függőségi ügynök üzembe helyezés – virtuális gép rendszerkép (OS) listán nem szereplő naplózása
    - [Előzetes verzió]: Log Analytics-ügynök telepítése – virtuális gép rendszerkép (OS) listán nem szereplő naplózása

* Virtuális gépek nem jelentkezik be a megadott Log Analytics-munkaterületet.  
    Akkor lehet, hogy néhány virtuális gépet a kezdeményezési hatókörében jelentkeznek be a másik a szabályzat-hozzárendelés megadott Log Analytics-munkaterületet. Ez a szabályzat egy olyan eszköz azonosításához, amely a virtuális gépek egy nem megfelelő munkaterületnek jelentenek.  
    - [Előzetes verzió]: Audit Log Analytics Workspace for VM - Report Mismatch

### <a name="enable-with-powershell"></a>Engedélyezze a PowerShell-lel
Engedélyezi az Azure Monitor-beli virtuális gépek több virtuális gép vagy virtuálisgép-méretezési csoportok, használhatja a PowerShell-parancsfájl [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), az Azure PowerShell-galériából érhető el. Ez a szkript minden virtuális gép és a virtuális gép méretezési csoportot az előfizetésében, a hatókörön belüli erőforráscsoport által meghatározott végighalad *ResourceGroup*, vagy egy egyetlen virtuális gép vagy virtuálisgép-méretezési csoporthoz által megadott *Neve*. Minden virtuális gép vagy a virtuális gép méretezési csoport esetében a szkript ellenőrzi a Virtuálisgép-bővítmény telepítve van-e már. Ha a Virtuálisgép-bővítmény nincs telepítve, a parancsfájl megpróbálja újra kell telepíteni. Ha a Virtuálisgép-bővítmény telepítve van, a parancsfájl telepíti a Log Analytics és a függőségi ügynök Virtuálisgép-bővítmények.

A szkriptnek szüksége van az Azure PowerShell 5.7.0 modul verzió vagy újabb. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ismertető cikket. Ha helyileg futtatja PowerShell, is futtatni szeretné `Connect-AzureRmAccount` kapcsolat létrehozása az Azure-ral.

A parancsfájl argumentum részletek és a példa használati listájának lekéréséhez futtassa `Get-Help`.

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

A következő példa bemutatja a mappában található a PowerShell-parancsok segítségével engedélyezése az Azure Monitor-beli virtuális gépek és a várt kimeneti megismerése:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-a-hybrid-environment"></a>A hibrid környezet engedélyezése
Ez a szakasz azt ismerteti, hogyan helyezhet üzembe virtuális gépeket vagy fizikai számítógépeket, amelyek az adatközpontban vagy egyéb felhőalapú környezetek figyelés által üzemeltetett Azure Monitor-beli virtuális gépek.

Az Azure Monitor, virtuális gépek térkép függőségi ügynök maga adatokat nem továbbít, és nincs szükség tűzfalak és a portok módosítása. A térképadatok mindig továbbítása a Log Analytics-ügynököket, az Azure Monitor szolgáltatásba, vagy közvetlenül vagy keresztül a [OMS-átjáró](../../azure-monitor/platform/gateway.md) , ha az informatikai biztonsági szabályzatok nem engedélyezése a számítógépek a hálózat csatlakozik az internethez.

Tekintse át a követelményeket és a központi telepítési módszerek a [Log Analytics Linux és Windows-ügynök](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

A lépések a következőképpen lehet összefoglalni:

1. Telepítse a Log Analytics-ügynököket Windows vagy Linux.

1. Töltse le és telepítse az Azure Monitor, a virtuális gépek térkép függőségi ügynök [Windows](https://aka.ms/dependencyagentwindows) vagy [Linux](https://aka.ms/dependencyagentlinux).

1. A teljesítményszámlálók gyűjtésének engedélyezéséhez.

1. Az Azure Monitor-beli virtuális gépek üzembe helyezése.

### <a name="install-the-dependency-agent-on-windows"></a>Telepítse a függőségi ügynököt Windows
Telepítheti a függőségi ügynök manuálisan a Windows-számítógépeken futó `InstallDependencyAgent-Windows.exe`. Ha a végrehajtható fájl kapcsolók nélkül futtatja, a telepítővarázsló, amelyekkel az ügynök telepítéséhez interaktív módon elindítja.

>[!NOTE]
>*Rendszergazdai* jogosultság szükséges telepíteni, vagy távolítsa el az ügynököt.

Az alábbi táblázat a paramétereket, az ügynök a parancssorból a telepítő által támogatott emeli ki.

| Paraméter | Leírás |
|:--|:--|
| /? | A parancssori kapcsolók listáját adja vissza. |
| /S | Felhasználói beavatkozás nélkül csendes telepítést hajt végre. |

Ha például az a telepítőprogram futtatása a `/?` írja be a paraméter **InstallDependencyAgent-Windows.exe /?**.

A Windows a függőségi ügynök fájlok vannak telepítve a *C:\Program Files\Microsoft függőségi ügynök* alapértelmezés szerint. Ha a függőségi ügynök nem a telepítés befejezése után indul el, ellenőrizze a hibával kapcsolatos részletes információk a naplókat. A naplózási könyvtár *%Programfiles%\Microsoft függőségi Agent\logs*.

### <a name="install-the-dependency-agent-on-linux"></a>A függőségi ügynök telepítése Linux rendszeren
A függőségi ügynök telepítve van a Linux-kiszolgálókon *InstallDependencyAgent-Linux64.bin*, azt a héjparancsfájlt, egy önkicsomagoló bináris. A fájl használatával futtathatja `sh` vagy adjon hozzá végrehajtási engedélyeket magát a fájlt.

>[!NOTE]
> Az ügynök telepítéséhez vagy eltávolításához gyökérszintű hozzáférés szükséges.
>

| Paraméter | Leírás |
|:--|:--|
| – Súgó | A parancssori kapcsolók listájának lekérése. |
| -s | Beavatkozás nélküli telepítés a felhasználónak szóló üzenetek nélkül. |
| --ellenőrzése | Ellenőrizze a jogosultságokat és az operációs rendszer, de ne telepítse az ügynököt. |

Ha például az a telepítőprogram futtatása a `-help` paraméter, írja be **InstallDependencyAgent-Linux64.bin-súgó**.

A Linux függőségi ügynök telepítése a rendszergazdaként a következő parancs futtatásával `sh InstallDependencyAgent-Linux64.bin`.

Ha a függőségi ügynök nem indul el, ellenőrizze a hibával kapcsolatos részletes információk a naplókat. A Linux-ügynökök, a naplózási könyvtár van */var/opt/microsoft/dependency-agent/log*.

A függőségi ügynök fájlok kerülnek a következő könyvtárak:

| Fájlok | Hely |
|:--|:--|
| Alapvető fájlok | /opt/microsoft/dependency-agent |
| Naplófájlok | /var/opt/microsoft/dependency-agent/log |
| Konfigurációs fájlok | /etc/opt/microsoft/dependency-agent/config |
| Szolgáltatás végrehajtható fájljai | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Bináris tárolófájlok | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Teljesítményszámlálók engedélyezése
Ha a Log Analytics-munkaterületet a megoldás által hivatkozott már nincs konfigurálva a megoldás által igényelt a teljesítményszámlálók adatainak összegyűjtése, szüksége lesz az engedélyezésükhöz. Ezt két módon teheti meg:
* Leírtak szerint manuálisan [a Log Analytics Windows és Linux rendszerű teljesítmény adatforrások](../../azure-monitor/platform/data-sources-performance-counters.md)
* Letöltésével és futtatásával egy PowerShell-parancsprogram, amely elérhető a [Azure PowerShell-galéria](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

### <a name="deploy-azure-monitor-for-vms"></a>Az Azure Monitor-beli virtuális gépek üzembe helyezése
Ez a módszer egy JSON-sablon, amely a megoldás-összetevőket a Log Analytics-munkaterület engedélyezése konfigurációját tartalmazza.

Ha még nem ismeri a sablon segítségével üzembe helyezni erőforrásokat, lásd:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Kell futnia az Azure CLI 2.0.27-es vagy újabb. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

#### <a name="create-and-execute-a-template"></a>Hozzon létre, és hajtsa végre a sablon

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

1. Értékeinek szerkesztéséhez *WorkspaceName*, *ResourceGroupName*, és *WorkspaceLocation*. Az érték *WorkspaceName* a Log Analytics-munkaterületet, amely tartalmazza a következő munkaterület nevének teljes erőforrás-Azonosítójára van. Az érték *WorkspaceLocation* a régió, a munkaterület van definiálva.

1. Készen áll a sablon üzembe helyezése a következő PowerShell-parancs használatával:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

    ```powershell
    provisioningState       : Succeeded
    ```
Miután engedélyezte a figyelés, az állapot és a hibrid számítógép metrikák megtekintéséhez nagyjából 10 percet vehet igénybe.

## <a name="performance-counters-enabled"></a>A teljesítményszámlálók engedélyezve
A virtuális gépek az Azure Monitor konfigurálása a Log Analytics-munkaterületet a megoldás által használt a teljesítményszámlálók adatainak összegyűjtése. A következő táblázat felsorolja azokat az objektumokat és a megoldás által konfigurált számlálói gyűjtött minden 60 másodpercben.

### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók

|Objektumnév |Számláló neve |
|------------|-------------|
|Logikai lemez |% Szabad terület |
|Logikai lemez |Átl. Lemez mp/Olvasás |
|Logikai lemez |Átl. Lemez mp/átvitel |
|Logikai lemez |Átl. Lemez mp/írás |
|Logikai lemez |Bájt/mp |
|Logikai lemez |Lemezolvasási sebesség (bájt/s) |
|Logikai lemez |Lemezolvasások/mp |
|Logikai lemez |Átvitel/mp |
|Logikai lemez |Lemezírási sebesség (bájt/s) |
|Logikai lemez |Lemezírások/mp |
|Logikai lemez |Szabad hely MB-ban |
|Memory (Memória) |Rendelkezésre álló memória |
|Hálózati Adapter |Fogadott bájtok/mp |
|Hálózati Adapter |Küldött bájtok/s |
|Processzor |Processzoridő |

### <a name="linux-performance-counters"></a>Linux-teljesítményszámlálók

|Objektumnév |Számláló neve |
|------------|-------------|
|Logikai lemez |Foglalt hely % |
|Logikai lemez |Lemezolvasási sebesség (bájt/s) |
|Logikai lemez |Lemezolvasások/mp |
|Logikai lemez |Átvitel/mp |
|Logikai lemez |Lemezírási sebesség (bájt/s) |
|Logikai lemez |Lemezírások/mp |
|Logikai lemez |Szabad hely MB-ban |
|Logikai lemez |Logikai lemez bájt/mp |
|Memory (Memória) |Rendelkezésre álló memória |
|Network (Hálózat) |Fogadott bájtok teljes száma |
|Network (Hálózat) |Küldött bájtok száma összesen |
|Processzor |Processzoridő |

## <a name="diagnostic-and-usage-data"></a>Diagnosztika és használati adatok
A Microsoft automatikusan gyűjt keresztül az Azure Monitor szolgáltatás használatának és teljesítményének adatait. A Microsoft ezeket az adatokat adja meg, és a minőségének, biztonságának és integritásának a szolgáltatás javítására használja. Pontos és hatékony hibaelhárítás képességeket biztosít, a térkép funkció adatait tartalmazza a szoftverek, például az operációs rendszer és verzió, IP-cím, DNS-nevet és munkaállomás neve konfigurációjával kapcsolatos információkat. A Microsoft nem gyűjt, neveket, címeket és egyéb kapcsolattartási adatait.

Az adatok gyűjtésével és használatával kapcsolatos további információkért tekintse meg a [Microsoft Online Services adatvédelmi nyilatkozata](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>További lépések

Most, hogy a figyelés engedélyezve van a virtuális gépet, ezeket az adatokat és az Azure Monitor-beli virtuális gépek elemzési érhető el. Az állapotfigyelő szolgáltatás használatával kapcsolatban lásd: [a virtuális gépek állapotának megtekintése az Azure Monitor](vminsights-health.md). Felderített alkalmazások függőségeinek megtekintése: [megtekintése az Azure Monitor virtuális gépeket a térképen](vminsights-maps.md).

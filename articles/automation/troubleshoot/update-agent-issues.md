---
title: A Windows Update Agent problémáinak elhárítása Azure Automation Update Management
description: Útmutató a Windows Update agenttel kapcsolatos hibák elhárításához és megoldásához a Update Management megoldás használatával.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 6983a2ac7ab5fafcb00aee0b72221a8540ea1668
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678973"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>A Windows Update Agent problémáinak elhárítása

Számos oka lehet annak, hogy a gép miért nem jelenik meg készenléti (kifogástalan) állapotban a Update Managementban. Update Management a hibrid Runbook Worker Agent állapotának ellenőrzését a probléma okának megállapításához. Ez a cikk azt ismerteti, hogyan futtathatja az Azure-gépekhez tartozó hibakeresőt a Azure Portal és a nem Azure-beli gépekről az [Offline forgatókönyvben](#troubleshoot-offline).

A következő három készültségi állapotú gép:

* Kész – a hibrid Runbook Worker üzembe helyezése megtörtént, és legalább 1 órával ezelőtt volt látható.
* Leválasztva – a hibrid Runbook Worker üzembe helyezése megtörtént, és a rendszer az elmúlt 1 órája óta utoljára látott el.
* Nincs konfigurálva – a hibrid Runbook Worker nem található vagy nem fejeződött be.

> [!NOTE]
> A Azure Portal megjelenítése és a gép aktuális állapota között enyhe késés adható meg.

## <a name="start-the-troubleshooter"></a>A hibakereső elindítása

Az Azure-gépek esetében a portál **frissítési ügynök készültsége** oszlopának **hibakeresés** hivatkozására kattintva elindítja az ügynök frissítése lapot. A nem Azure-beli gépek esetében a hivatkozás a jelen cikkre mutat. A nem Azure-beli gépek hibáinak megoldásához tekintse meg az [Offline utasításokat](#troubleshoot-offline) .

![Virtuális gépek felügyeleti listájának frissítése](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> A hibrid Runbook-feldolgozók állapotának vizsgálatához a virtuális gépnek futnia kell. Ha a virtuális gép nem fut, megjelenik a **virtuális gép indítása** gomb.

Az ügynök frissítése lapon válassza az **ellenőrzések futtatása** lehetőséget a hibakereső elindításához. A hibakereső a [Futtatás parancs](../../virtual-machines/windows/run-command.md) használatával futtat egy parancsfájlt a gépen a függőségek ellenőrzéséhez. Ha a hibakereső elkészült, a visszaadja az ellenőrzések eredményét.

![Az ügynök frissítése lap – problémamegoldás](../media/update-agent-issues/troubleshoot-page.png)

Az eredmények a lapon jelennek meg, amikor készen állnak. Az ellenőrzések szakaszban látható, hogy mi szerepel az egyes ellenőrzésekben.

![Frissítési ügynök ellenőrzésének hibáinak megoldása](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Előfeltétel-ellenőrzések

### <a name="operating-system"></a>Operációs rendszer

Az operációs rendszer ellenőrzése ellenőrzi, hogy a hibrid Runbook-feldolgozó a következő operációs rendszerek egyikét futtatja-e:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows Server 2012 és újabb verziók |A .NET-keretrendszer 4,6-es vagy újabb verziójára van szükség. ([A .NET-keretrendszer letöltése](/dotnet/framework/install/guide-for-developers))<br/> A Windows PowerShell 5,1 megadása kötelező.  (A[Windows Management Framework 5,1 letöltése](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET-4.6.2

A .NET-keretrendszer ellenőrzése ellenőrzi, hogy a rendszer legalább a [.NET-keretrendszer 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345) telepítve van-e.

### <a name="wmf-51"></a>WMF 5.1

A WMF-ellenőrzés ellenőrzi, hogy a rendszer rendelkezik-e a Windows Management Framework (WMF) szükséges verziójával ( [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616)).

### <a name="tls-12"></a>TLS 1.2

Ez az érték határozza meg, hogy a TLS 1,2-et használja-e a kommunikáció titkosításához. A platform már nem támogatja a TLS 1,0-et. Javasoljuk, hogy az ügyfelek a TLS 1,2-et használják a Update Management való kommunikációhoz.

## <a name="connectivity-checks"></a>Kapcsolatok ellenőrzése

### <a name="registration-endpoint"></a>Regisztrációs végpont

Ez az érték határozza meg, hogy az ügynök megfelelően tud-e kommunikálni az ügynök szolgáltatással.

A proxy és a tűzfal konfigurációjának lehetővé kell tennie, hogy a hibrid Runbook Worker ügynök kommunikáljon a regisztrációs végponttal. A megnyitni kívánt címek és portok listáját lásd: [a hibrid feldolgozók hálózati tervezése](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="operations-endpoint"></a>Műveleti végpont

Ez az érték határozza meg, hogy az ügynök megfelelően tud-e kommunikálni a feladatütemezés adatszolgáltatásával.

A proxy és a tűzfal konfigurációjának lehetővé kell tennie, hogy a hibrid Runbook-feldolgozó ügynök kommunikáljon a feladatütemezés adatszolgáltatásával. A megnyitni kívánt címek és portok listáját lásd: [a hibrid feldolgozók hálózati tervezése](../automation-hybrid-runbook-worker.md#network-planning).

## <a name="vm-service-health-checks"></a>Virtuálisgép-szolgáltatás állapotának ellenőrzése

### <a name="monitoring-agent-service-status"></a>Figyelési ügynök szolgáltatásának állapota

Ez az érték határozza meg, hogy a Windows-`healthservice`ügynök () fut-e a gépen a log Analytics. Ha többet szeretne megtudni a szolgáltatás hibaelhárításáról, tekintse meg [a log Analytics ügynök a Windows rendszerhez nem fut](hybrid-runbook-worker.md#mma-not-running).

A Windows Log Analytics-ügynök újratelepítéséhez lásd: [a windows log Analytics-ügynök telepítése és konfigurálása](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows).

### <a name="monitoring-agent-service-events"></a>Figyelési ügynök szolgáltatási eseményei

Ez az érték határozza meg, hogy az Azure Operations Manager a gépen az elmúlt 24 órában megjelenjen-e az összes 4502 esemény.

Az eseménnyel kapcsolatos további tudnivalókért tekintse meg az esemény [hibaelhárítási útmutatóját](hybrid-runbook-worker.md#event-4502) .

## <a name="access-permissions-checks"></a>Hozzáférési engedélyek ellenőrzése

### <a name="machinekeys-folder-access"></a>Következő

A kriptográfiai mappa hozzáférés-ellenőrzését határozza meg, hogy a helyi rendszerfiók hozzáfér-e a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Offline hibák

A hibakeresést a hibrid Runbook-feldolgozón offline módon, a parancsfájl helyi futtatásával használhatja. A szkriptet, a [hibakeresést](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)és a WindowsUpdateAgentRegistration a PowerShell-galériaban érheti el. A parancsfájl futtatásához a WMF 4,0 vagy újabb rendszernek kell futnia. A PowerShell legújabb verziójának letöltéséhez lásd: a [PowerShell különböző verzióinak telepítése](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

A szkript kimenete a következő példához hasonlóan néz ki:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.2.9200 (Windows Server 2012) or higher
CheckResult                 : Passed
CheckResultMessage          : Operating System version is supported
CheckResultMessageId        : OperatingSystemCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : DotNetFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : .NET Framework 4.5+
RuleGroupName               : Prerequisite Checks
RuleDescription             : .NET Framework version 4.5 or higher is required
CheckResult                 : Passed
CheckResultMessage          : .NET Framework version 4.5+ is found
CheckResultMessageId        : DotNetFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {}

RuleId                      : WindowsManagementFrameworkInstalledCheck
RuleGroupId                 : prerequisites
RuleName                    : WMF 5.1
RuleGroupName               : Prerequisite Checks
RuleDescription             : Windows Management Framework version 4.0 or higher is required (version 5.1 or higher is preferable)
CheckResult                 : Passed
CheckResultMessage          : Detected Windows Management Framework version: 5.1.17763.1
CheckResultMessageId        : WindowsManagementFrameworkInstalledCheck.Passed
CheckResultMessageArguments : {5.1.17763.1}

RuleId                      : AutomationAgentServiceConnectivityCheck1
RuleGroupId                 : connectivity
RuleName                    : Registration endpoint
RuleGroupName               : connectivity
RuleDescription             :
CheckResult                 : Failed
CheckResultMessage          : Unable to find Workspace registration information in registry
CheckResultMessageId        : AutomationAgentServiceConnectivityCheck1.Failed.NoRegistrationFound
CheckResultMessageArguments : {}

RuleId                      : AutomationJobRuntimeDataServiceConnectivityCheck
RuleGroupId                 : connectivity
RuleName                    : Operations endpoint
RuleGroupName               : connectivity
RuleDescription             : Proxy and firewall configuration must allow Automation Hybrid Worker agent to communicate with eus2-jobruntimedata-prod-su1.azure-automation.net
CheckResult                 : Passed
CheckResultMessage          : TCP Test for eus2-jobruntimedata-prod-su1.azure-automation.net (port 443) succeeded
CheckResultMessageId        : AutomationJobRuntimeDataServiceConnectivityCheck.Passed
CheckResultMessageArguments : {eus2-jobruntimedata-prod-su1.azure-automation.net}

RuleId                      : MonitoringAgentServiceRunningCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service status
RuleGroupName               : VM Service Health Checks
RuleDescription             : HealthService must be running on the machine
CheckResult                 : Failed
CheckResultMessage          : Log Analytics for Windows service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Log Analytics agent for Windows, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Log Analytics agent for Windows service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Log Analytics agent for Windows, Operations Manager, 4502}

RuleId                      : CryptoRsaMachineKeysFolderAccessCheck
RuleGroupId                 : permissions
RuleName                    : Crypto RSA MachineKeys Folder Access
RuleGroupName               : Access Permission Checks
RuleDescription             : SYSTEM account must have WRITE and MODIFY access to 'C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys'
CheckResult                 : Passed
CheckResultMessage          : Have permissions to access C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys
CheckResultMessageId        : CryptoRsaMachineKeysFolderAccessCheck.Passed
CheckResultMessageArguments : {C:\\ProgramData\\Microsoft\\Crypto\\RSA\\MachineKeys}

RuleId                      : TlsVersionCheck
RuleGroupId                 : prerequisites
RuleName                    : TLS 1.2
RuleGroupName               : Prerequisite Checks
RuleDescription             : Client and Server connections must support TLS 1.2
CheckResult                 : Passed
CheckResultMessage          : TLS 1.2 is enabled by default on the Operating System.
CheckResultMessageId        : TlsVersionCheck.Passed.EnabledByDefault
CheckResultMessageArguments : {}
```

## <a name="next-steps"></a>További lépések

A hibrid Runbook-feldolgozókkal kapcsolatos további problémák elhárításához lásd: [hibrid Runbook-feldolgozók hibaelhárítása](hybrid-runbook-worker.md).

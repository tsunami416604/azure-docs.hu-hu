---
title: A Windows-ügynök ellenőrzési eredményeinek ismertetése az Azure Update Management
description: Ismerje meg, hogyan lehet elhárítani a Update Management ügynökkel kapcsolatos problémákat.
services: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: d3099498c3abea428e04d94ca0fcd553e6a0fec6
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886404"
---
# <a name="understand-the-windows-agent-check-results-in-update-management"></a>A Windows-ügynök ellenőrzési eredményeinek ismertetése Update Management

Előfordulhat, hogy a gép számos okból nem **áll készen** a Update Management. A Update Management a hibrid feldolgozó ügynökök állapotát a probléma okának megállapításához tekintheti meg. Ez a cikk azt ismerteti, hogyan futtathatja az Azure-gépekhez tartozó hibakeresőt a Azure Portal és a nem Azure-beli gépekről az [Offline forgatókönyvben](#troubleshoot-offline).

A következő lista az alábbi három felkészültségi állapotot tartalmazza:

* **Készen áll** a frissítési ügynök üzembe helyezésére, és legalább 1 órával ezelőtt volt látható.
* **Leválasztva** – a frissítési ügynök üzembe helyezése megtörtént, és a rendszer a legutóbbi 1 órával ezelőtt volt látható.
* **Nincs konfigurálva** – a frissítési ügynök nem található, vagy nem fejeződött be.

> [!NOTE]
> A Azure Portal és a gép aktuális állapota között enyhe késés fordulhat elő.

## <a name="start-the-troubleshooter"></a>A hibakereső elindítása

Az Azure-gépek esetében a portál **frissítési ügynök készültsége** oszlopának **hibakeresés** hivatkozására kattintva elindítja az **ügynök frissítése** lapot. A nem Azure-beli gépek esetében a hivatkozás a jelen cikkre mutat. A nem Azure-beli gépek hibáinak megoldásához tekintse meg az [Offline utasításokat](#troubleshoot-offline) .

![Virtuális gépek felügyeleti listájának frissítése](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Egy ügynök állapotának vizsgálatához a virtuális gépnek futnia kell. Ha a virtuális gép nem fut, megjelenik a **virtuális gép indítása** gomb.

Az **ügynök frissítése** lapon válassza az **ellenőrzések futtatása** lehetőséget a hibakereső elindításához. A hibakereső a [Futtatás parancs](../../virtual-machines/windows/run-command.md) használatával futtat egy parancsfájlt a gépen az ügynök függőségeinek ellenőrzéséhez. Ha a hibakereső elkészült, a visszaadja az ellenőrzések eredményét.

![Az ügynök frissítése lap – problémamegoldás](../media/update-agent-issues/troubleshoot-page.png)

Az eredmények a lapon jelennek meg, amikor készen állnak. Az ellenőrzések szakaszban látható, hogy mi szerepel az egyes ellenőrzésekben.

![Frissítési ügynök ellenőrzésének hibáinak megoldása](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Előfeltételek ellenőrzése

### <a name="operating-system"></a>Operációs rendszer

Az operációs rendszer ellenőrzése ellenőrzi, hogy a hibrid Runbook-feldolgozó fut-e az alábbi operációs rendszerek valamelyikén:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows Server 2008 R2 RTM, Windows Server 2008 | A csak a frissítési felméréseket támogatja.         |
|Windows Server 2008 R2 SP1 és újabb verziók |A .NET-keretrendszer 4.6.1-es vagy újabb verziója szükséges. ([A .NET-keretrendszer letöltése](/dotnet/framework/install/guide-for-developers))<br/> A Windows PowerShell 5,1 megadása kötelező.  (A[Windows Management Framework 5,1 letöltése](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-461"></a>.NET 4.6.1 +

A .NET-keretrendszer ellenőrzése ellenőrzi, hogy a rendszernek van-e legalább a [.NET-keretrendszer 4.6.1](https://www.microsoft.com/en-us/download/details.aspx?id=49981) -es telepítése.

### <a name="wmf-51"></a>WMF 5,1

A WMF-ellenőrzés ellenőrzi, hogy a rendszer rendelkezik-e a Windows Management Framework (WMF) szükséges verziójával ( [Windows Management framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616)).

### <a name="tls-12"></a>TLS 1,2

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

Ez az ellenőrzés meghatározza, hogy a számítógépen fut-e a `HealthService`, a Microsoft monitoring Agent.

Ha többet szeretne megtudni a szolgáltatás hibaelhárításáról, tekintse meg [a Microsoft monitoring Agent nem fut](hybrid-runbook-worker.md#mma-not-running).

A Microsoft monitoring Agent újratelepítéséhez tekintse meg [a Microsoft monitoring Agent telepítése és konfigurálása](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)című témakört.

### <a name="monitoring-agent-service-events"></a>Figyelési ügynök szolgáltatási eseményei

Ez az érték határozza meg, hogy minden `4502` esemény megjelenik-e az Azure Operations Manager a gépen az elmúlt 24 órában.

Az eseménnyel kapcsolatos további tudnivalókért tekintse meg az esemény [hibaelhárítási útmutatóját](hybrid-runbook-worker.md#event-4502) .

## <a name="access-permissions-checks"></a>Hozzáférési engedélyek ellenőrzése

### <a name="machinekeys-folder-access"></a>Következő

A kriptográfiai mappa hozzáférés-ellenőrzését határozza meg, hogy a helyi rendszerfiók hozzáfér-e a C:\ProgramData\Microsoft\Crypto\RSA.

## <a name="troubleshoot-offline"></a>Offline hibák

A hibakeresést a hibrid Runbook-feldolgozón offline módon, a parancsfájl helyi futtatásával használhatja. A szkriptet, a [hibakeresést](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)és a WindowsUpdateAgentRegistration a PowerShell-galériaban érheti el. A parancsfájl futtatásához a WMF 4,0 vagy újabb rendszernek kell futnia. A PowerShell legújabb verziójának letöltéséhez lásd: a [PowerShell különböző verzióinak telepítése](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).

A szkript kimenete a következő példához hasonlóan néz ki:

```output
RuleId                      : OperatingSystemCheck
RuleGroupId                 : prerequisites
RuleName                    : Operating System
RuleGroupName               : Prerequisite Checks
RuleDescription             : The Windows Operating system must be version 6.1.7601 (Windows Server 2008 R2 SP1) or higher
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
CheckResultMessage          : Microsoft Monitoring Agent service (HealthService) is not running
CheckResultMessageId        : MonitoringAgentServiceRunningCheck.Failed
CheckResultMessageArguments : {Microsoft Monitoring Agent, HealthService}

RuleId                      : MonitoringAgentServiceEventsCheck
RuleGroupId                 : servicehealth
RuleName                    : Monitoring Agent service events
RuleGroupName               : VM Service Health Checks
RuleDescription             : Event Log must not have event 4502 logged in the past 24 hours
CheckResult                 : Failed
CheckResultMessage          : Microsoft Monitoring Agent service Event Log (Operations Manager) does not exist on the machine
CheckResultMessageId        : MonitoringAgentServiceEventsCheck.Failed.NoLog
CheckResultMessageArguments : {Microsoft Monitoring Agent, Operations Manager, 4502}

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


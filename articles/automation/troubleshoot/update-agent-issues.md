---
title: A Windows ügynök ellenőrzésének az eredménye az Azure az Update Management ismertetése
description: Ismerje meg, az Update Management ügynökkel kapcsolatos problémák elhárítása.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: automation
ms.component: update-management
manager: carmonm
ms.openlocfilehash: 33ae9160cd173ae594407220390c4dab1806fa94
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824132"
---
# <a name="understand-the-windows-agent-check-results-in-update-management"></a>A Windows ügynök ellenőrzésének az eredménye az Update Management ismertetése

Előfordulhat, hogy a nem Azure-beli gép nem látható, számos oka lehet **készen** az Update Management. Az Update Management ellenőrizheti az határozza meg a hibát kiváltó problémát hibrid feldolgozó ügynök állapotát. Ez a cikk ismerteti a hibaelhárító futtatása az Azure Portalról, és a kapcsolat nélküli üzemmódban.

## <a name="start-the-troubleshooter"></a>Indítsa el a hibaelhárító

Kattintson a **hibaelhárítás** mellett kapcsolni a **frissítési ügynök készültsége** oszlop a portálon, indítsa el a **hibaelhárítása Windows Update Agent** lap. Ezen a lapon látható az ügynök és a egy hivatkozás, ez a cikk segítséget nyújt a problémák elhárítása a kapcsolatos problémák.

![virtuális gép listáját tartalmazó lapon](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> Az ellenőrzések futtatását van szüksége. Ha a virtuális gép nem fut egy gombra kattintva megnyílik **indítsa el a virtuális Gépet**.

Az a **hibaelhárítása Windows Update Agent** kattintson **ellenőrzések futtatása**, a hibaelhárító elindításához. Használja a hibaelhárító [futtatása paranccsal](../../virtual-machines/windows/run-command.md) parancsfájl futtatása a függőségeket, amelyen az ügynök ellenőrzése a számítógépen. A hibaelhárító befejeződése után az ellenőrzések eredményét adja vissza.

![Hibaelhárítás lap](../media/update-agent-issues/troubleshoot-page.png)

Amikor végzett, a rendszer visszairányítja az eredményeket az ablakban. A [ellenőrizze szakaszokat](#pre-requisistes-checks) információkat tartalmaz minden ellenőrzés keresi.

![Az ügynök ellenőrzése lap frissítése](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Előfeltétel-ellenőrzések

### <a name="operating-system"></a>Operációs rendszer

Az operációs rendszer ellenőrzés ellenőrzi, hogy ha a hibrid Runbook-feldolgozót a következő operációs rendszerek egyikét futtatja:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|A Windows Server 2008, Windows Server 2008 R2 RTM    | Támogatja az értékelések csak frissíteni.         |
|A Windows Server 2008 R2 SP1 és újabb verziók     |.NET-keretrendszer 4.5.1-es vagy újabb verzió szükséges. ([Töltse le a .NET-keretrendszer](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0-s vagy újabb szükség. ([Töltse le a WMF 4.0](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1 megbízhatóság növelése érdekében ajánlott.  ([Töltse le a WMF 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-451"></a>.NET 4.5.1

A .NET-keretrendszer ellenőrzés ellenőrzi, hogy a rendszer rendelkezik-e legalább [.NET-keretrendszer 4.5.1-es](https://www.microsoft.com/download/details.aspx?id=30653) jelen.

### <a name="wmf-51"></a>A WMF 5.1

A WMF-ellenőrzés ellenőrzi, hogy rendelkezik-e a rendszer a Windows Management Framework szükséges verziója. [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855) a támogatott legalacsonyabb verziójú. Azt javasoljuk, hogy telepítse [Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616) hibrid Runbook-feldolgozót a megbízhatóság növelése érdekében.

### <a name="tls-12"></a>TLS 1.2

Ez az ellenőrzés határozza meg, ha a TLS 1.2-t használ a kommunikáció titkosítása. A platform által nem támogatott a TLS 1.0-s és, hogy az ügyfelek az Update Management folytatott kommunikációhoz a TLS 1.2 használata ajánlott.

## <a name="connectivity-checks"></a>Kapcsolat ellenőrzése

### <a name="registration-endpoint"></a>Regisztrációs végpont

Ez az ellenőrzés határozza meg, ha az ügynök megfelelően kommunikálhat az ügynök szolgáltatással.

Proxy és tűzfal-konfiguráció lehetővé teszi a hibrid Runbook-feldolgozó ügynök a regisztrációs végponttal való kommunikációhoz. Címek és portok megnyitásához listáját lásd: [hálózati hibrid feldolgozók tervezése](../automation-hybrid-runbook-worker.md#network-planning)

### <a name="operations-endpoint"></a>Műveletek végpont

Ez az ellenőrzés határozza meg, ha az ügynök megfelelően kommunikál a feladat futásidejű adatok szolgáltatással.

Proxy és a tűzfal beállításait a hibrid Runbook-feldolgozó ügynök kommunikáljon a feladat modul Data Service lehetővé teszi. Címek és portok megnyitásához listáját lásd: [hálózati hibrid feldolgozók tervezése](../automation-hybrid-runbook-worker.md#network-planning)

## <a name="vm-service-health-checks"></a>Virtuális gép szolgáltatás állapot-ellenőrzések

### <a name="monitoring-agent-service-status"></a>Monitoring agent szolgáltatás állapota

Ez a vizsgálat ellenőrzi, hogy a Microsoft Monitoring Agent `HealthService` fut a gépen.

A szolgáltatás hibaelhárítással kapcsolatos további tudnivalókért lásd: [nem fut a Microsoft Monitoring Agent](hybrid-runbook-worker.md#mma-not-running).

Telepítse újra a Microsoft Monitoring Agent, lásd: [telepítése és konfigurálása a Microsoft Monitoring Agent](/log-analytics/log-analytics-concept-hybrid.md#install-and-configure-agent)

### <a name="monitoring-agent-service-events"></a>Figyelési ügynök eseményei

Ez az ellenőrzés azt határozza meg, hogy voltak bármely `4502` események az Operations Manager jelentkezzen be a gép az elmúlt 24 órában.

Ez az esemény kapcsolatos további információkért tekintse meg a [hibaelhárítási útmutató](hybrid-runbook-worker.md#event-4502) ezt az eseményt.

## <a name="access-permissions-checks"></a>Hozzáférési engedélyek ellenőrzése

### <a name="machinekeys-folder-access"></a>MachineKeys mappahozzáférés

A kriptográfiai mappa hozzáférés-ellenőrzés határozza meg, ha a helyi rendszerfiók hozzáfér `C:\ProgramData\Microsoft\Crypto\RSA`

## <a name="troubleshoot-offline"></a>Kapcsolat nélküli hibaelhárítása

Használhatja a hibaelhárító offline egy hibrid Runbook-feldolgozón a szkript helyi futtatásával. A parancsfájl [hibaelhárítás – WindowsUpdateAgentRegistration](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration) a PowerShell-galériában található. Ez a szkript a kimenetét egy példát a következő példában látható:

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
RuleName                    : .Net Framework 4.5+
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

A hibrid Runbook-feldolgozók további problémák hibaelhárítása: [hibaelhárítás – a hibrid Runbook-feldolgozók](hybrid-runbook-worker.md)

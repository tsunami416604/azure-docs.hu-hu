---
title: A Windows update ügynökkel kapcsolatos problémák elhárítása az Azure Automation Update Management szolgáltatásban
description: Megtudhatja, hogy miként háríthatja el és oldhatja meg a Windows frissítési ügynökkel kapcsolatos problémákat az Update Management megoldás használatával.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: automation
ms.subservice: update-management
manager: carmonm
ms.openlocfilehash: 6983a2ac7ab5fafcb00aee0b72221a8540ea1668
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678973"
---
# <a name="troubleshoot-windows-update-agent-issues"></a>A Windows update ügynökkel kapcsolatos problémák elhárítása

Számos oka lehet annak, hogy a számítógép nem jelenik meg készként (kifogástalan) az Update Management ben. Az Update Management, ellenőrizheti a hibrid Runbook-feldolgozó ügynök állapotát az alapul szolgáló probléma meghatározásához. Ez a cikk bemutatja, hogyan futtathatja a hibaelhárító az Azure-gépek az Azure Portalon és a nem Azure-gépek az [offline forgatókönyvben.](#troubleshoot-offline)

A gép három készenléti állapota a következő:

* Ready – A hibrid Runbook-feldolgozó telepítve van, és kevesebb mint 1 órával ezelőtt látták utoljára.
* Leválasztva – A hibrid Runbook-feldolgozó telepítve van, és utoljára 1 órával ezelőtt látták.
* Nincs konfigurálva – a hibrid Runbook-feldolgozó nem található, vagy nem fejeződött be a bevezetés.

> [!NOTE]
> Az Azure Portal megjelenítése és a gép aktuális állapota között kis késés lehet.

## <a name="start-the-troubleshooter"></a>A hibaelhárító indítása

Azure-gépek esetén a portál **frissítési ügynök-készenléte** oszlopában található **Hibaelhárítás** hivatkozásra kattintva elindul a Frissítési ügynök hibaelhárítási ügynök e-hibaelhárítása lap. Nem Azure-alapú gépek esetén a hivatkozás ebben a cikkben található. Tekintse meg az [offline utasításokat](#troubleshoot-offline) egy nem Azure-gép hibaelhárításához.

![Virtuális gépek felügyeleti listájának frissítése](../media/update-agent-issues/vm-list.png)

> [!NOTE]
> A hibrid Runbook-feldolgozó állapotának ellenőrzéséhez a virtuális gépnek futnia kell. Ha a virtuális gép nem fut, megjelenik **a Virtuálisgép indítása** gomb.

A Frissítési ügynök hibaelhárítása lapon jelölje be az **Ellenőrzések futtatása** a hibaelhárító elindításához jelölőnégyzetet. A hibaelhárító a [Parancs futtatása paranccsal](../../virtual-machines/windows/run-command.md) futtat egy parancsfájlt a számítógépen a függőségek ellenőrzésére. Amikor a hibaelhárító elkészült, az ellenőrzések eredményét adja vissza.

![A Frissítési ügynök lap – Hibaelhárítás](../media/update-agent-issues/troubleshoot-page.png)

Az eredmények akkor jelennek meg az oldalon, amikor készen állnak. A csekkek szakaszokban látható, hogy mi szerepel az egyes csekkekben.

![Frissítési ügynök-ellenőrzések – problémamegoldás](../media/update-agent-issues/update-agent-checks.png)

## <a name="prerequisite-checks"></a>Előfeltétel-ellenőrzések

### <a name="operating-system"></a>Operációs rendszer

Az operációs rendszer ellenőrzése ellenőrzi, hogy a hibrid Runbook-feldolgozó az alábbi operációs rendszerek valamelyikét futtatja-e:

|Operációs rendszer  |Megjegyzések  |
|---------|---------|
|Windows Server 2012 és újabb |A . ([A .NET keretrendszer letöltése)](/dotnet/framework/install/guide-for-developers)<br/> Windows PowerShell 5.1 szükséges.  ([Letöltés Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616))        |

### <a name="net-462"></a>.NET 4.6.2

A . [.NET Framework 4.6.2](https://www.microsoft.com/en-us/download/details.aspx?id=53345)

### <a name="wmf-51"></a>WMF 5.1

A WMF-ellenőrzés ellenőrzi, hogy a rendszer rendelkezik-e a Windows Management Framework (WMF) – [Windows Management Framework 5.1 szükséges verziójával.](https://www.microsoft.com/download/details.aspx?id=54616)

### <a name="tls-12"></a>TLS 1.2

Ez az ellenőrzés azt határozza meg, hogy a TLS 1.2-t használja-e a kommunikáció titkosításához. A Platform már nem támogatja a TLS 1.0-t. Azt javasoljuk, hogy az ügyfelek a TLS 1.2-t használják az Update Management szolgáltatással való kommunikációhoz.

## <a name="connectivity-checks"></a>Kapcsolódási ellenőrzések

### <a name="registration-endpoint"></a>Regisztrációs végpont

Ez az ellenőrzés azt határozza meg, hogy az ügynök képes-e megfelelően kommunikálni az ügynökszolgáltatással.

A proxy- és tűzfal-konfigurációknak lehetővé kell tenniük, hogy a hibrid Runbook-feldolgozó ügynök kommunikáljon a regisztrációs végponttal. A megnyitandó címek és portok listáját a [Hibrid dolgozók hálózattervezése (Hálózattervezés) (Hálózattervezés hibrid dolgozóknak)](../automation-hybrid-runbook-worker.md#network-planning)témakörben található.

### <a name="operations-endpoint"></a>Műveleti végpont

Ez az ellenőrzés azt határozza meg, hogy az ügynök képes-e megfelelően kommunikálni a feladatfutásidejű adatszolgáltatással.

A proxy- és tűzfal-konfigurációknak lehetővé kell tenniük, hogy a hibrid Runbook-feldolgozó ügynök kommunikáljon a feladatfutásidejű adatszolgáltatással. A megnyitandó címek és portok listáját a [Hibrid dolgozók hálózattervezése (Hálózattervezés) (Hálózattervezés hibrid dolgozóknak)](../automation-hybrid-runbook-worker.md#network-planning)témakörben található.

## <a name="vm-service-health-checks"></a>Virtuálisgép-szolgáltatás állapotának ellenőrzése

### <a name="monitoring-agent-service-status"></a>Figyelőügynök-szolgáltatás állapota

Ez az ellenőrzés azt határozza meg,`healthservice`hogy a Windows Log Analytics ügynöke ( ) fut-e a számítógépen. Ha többet szeretne tudni a szolgáltatás hibaelhárításáról, olvassa el [a Windows Naplóelemző ügynöke című](hybrid-runbook-worker.md#mma-not-running)témakört.

A Windows Log Analytics-ügynökújratelepítéséről a [Windows Log Analytics-ügynök telepítése és konfigurálása](../../azure-monitor/learn/quick-collect-windows-computer.md#install-the-agent-for-windows)című témakörben találhatók.

### <a name="monitoring-agent-service-events"></a>Figyelési ügynök szolgáltatáseseményei

Ez az ellenőrzés határozza meg, hogy bármely 4502-es események jelennek meg az Azure Operations Manager naplóa a gépen az elmúlt 24 órában.

Ha többet szeretne megtudni erről az eseményről, olvassa el az esemény [hibaelhárítási útmutatóját.](hybrid-runbook-worker.md#event-4502)

## <a name="access-permissions-checks"></a>Hozzáférési engedélyek ellenőrzése

### <a name="machinekeys-folder-access"></a>MachineKeys mappa elérése

A Titkosítási mappa hozzáférési ellenőrzése azt határozza meg, hogy a helyi rendszerfiók hozzáfér-e a C:\ProgramData\Microsoft\Crypto\RSA szolgáltatáshoz.

## <a name="troubleshoot-offline"></a><a name="troubleshoot-offline"></a>Kapcsolat nélküli kapcsolat nélküli – problémamegoldás

A hibaelhárító egy hibrid Runbook-feldolgozó offline használatával a parancsfájl helyi futtatásával. A [WindowsUpdateAgentRegistration hibaelhárítási](https://www.powershellgallery.com/packages/Troubleshoot-WindowsUpdateAgentRegistration)parancsfájlt a PowerShell-tárban szerezheti be. A parancsfájl futtatásához wmf 4.0-s vagy nagyobb telepítésben kell lennie. A PowerShell legújabb verziójának letöltéséhez olvassa [el a PowerShell különböző verzióinak telepítése című témakört.](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)

A parancsfájl kimenete a következő példához hasonlóan néz ki:

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

A hibrid runbook-feldolgozókkal kapcsolatos további problémák elhárításához olvassa el a [Hibrid Runbook-dolgozók hibaelhárítása című témakört.](hybrid-runbook-worker.md)

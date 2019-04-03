---
title: Az Azure Monitor az Azure Log Analytics Virtuálisgép-bővítmény hibáinak elhárítása |} A Microsoft Docs
description: A jelenség okok és a Log Analytics Virtuálisgép-bővítménnyel a leggyakoribb problémák megoldási ismertetik a Windows és Linux rendszerű Azure virtuális gépek.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.openlocfilehash: 40f0705cfa7f0e9bb45d300a629adebd0cc5be47
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883672"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>Az Azure monitorban a Log Analytics Virtuálisgép-bővítmény hibáinak elhárítása
Ez a cikk ismerteti a hibaelhárítást hibákat tapasztalhat a Microsoft Azure platformon futó Windows és Linux rendszerű virtuális gépekhez a Log Analytics Virtuálisgép-bővítmény, és azok megoldását lehetséges megoldásokat javasol.

A bővítmény állapotának ellenőrzéséhez hajtsa végre az alábbi lépéseket az Azure Portalról.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **virtuális gépek**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **virtuális gépek**.
3. A virtuális gépek listáját keresse meg és jelölje ki azt.
3. A virtuális gépen kattintson **bővítmények**.
4. A listában ellenőrizze, hogy a Log Analytics-bővítmény engedélyezve van-e vagy sem.  A Linux rendszerre, az ügynök szerepel **OMSAgentforLinux** és a Windows, az ügynök szerepel **MicrosoftMonitoringAgent**.

   ![VM-bővítmény megtekintése](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Kattintson a részletek megtekintéséhez a bővítményt. 

   ![VM-bővítmény részletei](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Azure Windows VM-bővítmény hibáinak elhárítása

Ha a *Microsoft Monitoring Agent* Virtuálisgép-bővítmény telepítése nélkül, vagy a jelentéskészítés, a következő lépéseket a probléma elhárításához hajthat végre.

1. Ellenőrizze, hogy ha az Azure-beli Virtuálisgép-ügynök telepítve van és működik, a lépések használatával megfelelően [KB-os 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Emellett áttekintheti a Virtuálisgép-ügynök naplófájlját `C:\WindowsAzure\logs\WaAppAgent.log`
   * Ha a napló nem létezik, a Virtuálisgép-ügynök nincs telepítve.
   * [Az Azure Virtuálisgép-ügynök telepítése](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Ellenőrizze a Microsoft Monitoring Agent bővítményt szívverés feladat fut-e az alábbi lépéseket követve:
   * Jelentkezzen be a virtuális gép
   * Nyissa meg a Feladatütemezőt, és keresse meg a `update_azureoperationalinsight_agent_heartbeat` feladat
   * Erősítse meg a feladat engedélyezve van, és a egy percenként fut.
   * A szívverés logfile beadás `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. A Microsoft Monitoring Agent VM bővítmény a naplófájlok áttekintése `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. Győződjön meg arról, a virtuális gép PowerShell parancsfájlok futtathatók.
5. Győződjön meg arról, nem módosította a C:\Windows\temp vonatkozó engedélyek
6. A Microsoft Monitoring Agent állapotának megtekintéséhez írja be a következőt egy emelt szintű PowerShell-ablakot a virtuális gépen `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Tekintse át a Microsoft Monitoring Agent telepítési naplófájlok `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

További információkért lásd: [Windows-bővítmények hibaelhárítása](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Linux Virtuálisgép-bővítmény hibáinak elhárítása
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Ha a *Linuxhoz készült Log Analytics-ügynök* Virtuálisgép-bővítmény telepítése nélkül, vagy a jelentéskészítés, a következő lépéseket a probléma elhárításához hajthat végre.

1. Ha a bővítmény állapota *ismeretlen* ellenőrizze, hogy ha az Azure-beli Virtuálisgép-ügynök telepítve van-e, és megfelelően működik-e a Virtuálisgép-ügynök naplófájlját áttekintésével `/var/log/waagent.log`
   * Ha a napló nem létezik, a Virtuálisgép-ügynök nincs telepítve.
   * [Az Azure Virtuálisgép-ügynök telepítése Linux rendszerű virtuális gépeken](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Többi nem kifogástalan állapot, tekintse át a Log Analytics-ügynök Linux Virtuálisgép-bővítmény-fájlokat naplókat `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` és `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Ha a bővítmény állapota kifogástalan állapotban, de nem feltöltött adatmennyiség tekintse át a Log Analytics-ügynököt a Linux-naplófájlok `/var/opt/microsoft/omsagent/log/omsagent.log`

További információkért lásd: [Linux-bővítmények hibaelhárítása](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>További lépések

A Log Analytics-ügynököket a Linux rendszerű számítógépek Azure-on kívül üzemeltetett kapcsolatos további hibaelhárítási útmutatóját lásd: [hibaelhárítása az Azure Log Analytics Linux-ügynök](agent-linux-troubleshoot.md).  

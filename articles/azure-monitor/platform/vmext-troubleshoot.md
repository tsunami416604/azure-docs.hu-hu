---
title: Az Azure Log Analytics virtuálisgép-bővítmény hibáinak megoldása a Azure Monitorban | Microsoft Docs
description: A jelenség okok és a Log Analytics Virtuálisgép-bővítménnyel a leggyakoribb problémák megoldási ismertetik a Windows és Linux rendszerű Azure virtuális gépek.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: 88d76fc0c215653cf732ba7b827d82187d738fd9
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658472"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>A Log Analytics virtuálisgép-bővítmény hibáinak elhárítása Azure Monitor
Ez a cikk ismerteti a hibaelhárítást hibákat tapasztalhat a Microsoft Azure platformon futó Windows és Linux rendszerű virtuális gépekhez a Log Analytics Virtuálisgép-bővítmény, és azok megoldását lehetséges megoldásokat javasol.

A bővítmény állapotának ellenőrzéséhez hajtsa végre az alábbi lépéseket az Azure Portalról.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájában írja be a következőt: **Virtual Machines**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **virtuális gépek**lehetőséget.
3. A virtuális gépek listáját keresse meg és jelölje ki azt.
3. A virtuális gépen kattintson a **bővítmények**lehetőségre.
4. A listában ellenőrizze, hogy a Log Analytics-bővítmény engedélyezve van-e vagy sem.  A Linux esetében az ügynök **OMSAgentforLinux** és Windows rendszeren szerepel, az ügynök **MicrosoftMonitoringAgent**néven szerepel.

   ![VM-bővítmény megtekintése](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Kattintson a részletek megtekintéséhez a bővítményt. 

   ![VM-bővítmény részletei](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Azure Windows VM-bővítmény hibáinak elhárítása

Ha a *Microsoft monitoring Agent* virtuálisgép-bővítménye nem telepíti vagy nem jelent jelentést, a probléma megoldásához hajtsa végre a következő lépéseket.

1. Ellenőrizze, hogy az Azure-beli virtuálisgép-ügynök telepítve van-e és megfelelően működik-e a [KB 2965986](https://support.microsoft.com/kb/2965986#mt1)-as lépésekkel.
   * A virtuálisgép-ügynök naplófájlját is áttekintheti `C:\WindowsAzure\logs\WaAppAgent.log`
   * Ha a napló nem létezik, a Virtuálisgép-ügynök nincs telepítve.
   * [Az Azure VM-ügynök telepítése](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Tekintse át a Microsoft monitoring Agent virtuálisgép-bővítmény naplófájljait `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Győződjön meg arról, a virtuális gép PowerShell parancsfájlok futtathatók.
4. Győződjön meg arról, nem módosította a C:\Windows\temp vonatkozó engedélyek
5. Tekintse meg a Microsoft monitoring Agent állapotát úgy, hogy beírja a következőt egy emelt szintű PowerShell-ablakban a virtuális gépen `(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Tekintse át a Microsoft monitoring Agent telepítési naplófájljait `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

További információ: Windows- [bővítmények hibaelhárítása](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Linux Virtuálisgép-bővítmény hibáinak elhárítása
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Ha a linuxos virtuálisgép-bővítmény *log Analytics ügynöke* nem telepíti vagy nem jelenti a telepítést, akkor a probléma megoldásához hajtsa végre a következő lépéseket.

1. Ha a bővítmény állapota *ismeretlen* , ellenőrizze, hogy az Azure-beli virtuálisgép-ügynök telepítve van-e és megfelelően működik-e a virtuálisgép-ügynök naplófájljának áttekintésével `/var/log/waagent.log`
   * Ha a napló nem létezik, a Virtuálisgép-ügynök nincs telepítve.
   * [Az Azure-beli virtuálisgép-ügynök telepítése Linux rendszerű virtuális gépeken](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Egyéb nem kifogástalan állapotok esetén tekintse át a Linux rendszerű virtuális gépek bővítményének Log Analytics-ügynökének naplófájljait `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` és `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Ha a bővítmény állapota Kifogástalan, de nem töltődik fel az adatai, tekintse át a Linux-naplófájlok Log Analytics ügynökét `/var/opt/microsoft/omsagent/log/omsagent.log`

További információ: Linux- [bővítmények hibaelhárítása](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>Következő lépések

Az Azure-on kívüli számítógépeken futó Linux Log Analytics ügynökkel kapcsolatos további hibaelhárítási útmutatót az [azure log Analytics Linux-ügynök hibaelhárítása](agent-linux-troubleshoot.md)című témakörben talál.  

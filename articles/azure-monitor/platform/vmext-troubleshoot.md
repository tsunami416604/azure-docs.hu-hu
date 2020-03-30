---
title: Azure Log Analytics virtuálisgép-bővítmény – problémamegoldás
description: Írja le a Windows és Linux Azure virtuális gépek hez való naplóelemzési virtuálisgép-bővítménysel kapcsolatos leggyakoribb problémák tüneteit, okait és megoldását.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: e16531484505f055c1383aff5adb40518719d98a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054582"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>A Log Analytics virtuális gépi bővítményének hibaelhárítása az Azure Monitorban
Ez a cikk segítséget nyújt a Microsoft Azure-on futó Windows és Linux virtuális gépek Log Analytics virtuálisgép-bővítményével kapcsolatos hibák elhárításához, és lehetséges megoldásokat javasol azok megoldására.

A bővítmény állapotának ellenőrzéséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **virtuális gépeket.** Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Virtuális gépek** lehetőséget.
3. A virtuális gépek listájában keresse meg és jelölje ki azt.
3. A virtuális gépen kattintson a **Bővítmények gombra.**
4. A listából ellenőrizze, hogy a Log Analytics-bővítmény engedélyezve van-e vagy sem.  Linux esetén az ügynök **OMSAgentforLinux** és Windows esetén az ügynök **MicrosoftMonitoringAgent**néven szerepel.

   ![Virtuálisgép-bővítmény nézete](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. A részletek megtekintéséhez kattintson a bővítményre. 

   ![Virtuálisgép-bővítmény részletei](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Az Azure Windows VM-bővítmény hibáinak elhárítása

Ha a *Microsoft Monitoring Agent* virtuálisgép-bővítmény nem telepíthető vagy nem jelent jelentést, a probléma elhárításához hajtsa végre az alábbi lépéseket.

1. Ellenőrizze, hogy az Azure VM-ügynök telepítve van-e, és megfelelően működik-e a [KB 2965986](https://support.microsoft.com/kb/2965986#mt1)lépéseket.
   * A virtuális gép ügynöknaplófájlját is áttekintheti`C:\WindowsAzure\logs\WaAppAgent.log`
   * Ha a napló nem létezik, a virtuálisgép-ügynök nincs telepítve.
   * [Az Azure Virtuálisgép-ügynök telepítése](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Tekintse át a Microsoft Monitoring Agent VM-bővítmény naplófájljait a`C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Annak biztosítása, hogy a virtuális gép képes powershell-parancsfájlok futtatására
4. Annak biztosítása, hogy a C:\Windows\temp engedélyei ne változjanak
5. A Microsoft Monitoring Agent állapotának megtekintése a következők beírásával a virtuális gépen egy emelt szintű PowerShell-ablakba`(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Tekintse át a Microsoft Monitoring Agent telepítőnaplófájljait a`C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

További információt a [Windows-bővítmények hibaelhárítása című témakörben talál.](../../virtual-machines/extensions/oms-windows.md)

## <a name="troubleshooting-linux-vm-extension"></a>Linux os virtuális gépbővítmény hibáinak elhárítása
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Ha a *Log Analytics-ügynök linuxos* virtuálisgép-bővítmény nem telepíti vagy nem jelenti, a következő lépéseket a probléma elhárításához.

1. Ha a bővítmény állapota *Ismeretlen,* ellenőrizze, hogy az Azure VM-ügynök telepítve van-e, és megfelelően működik-e a virtuálisgép-ügynök naplófájljának áttekintésével`/var/log/waagent.log`
   * Ha a napló nem létezik, a virtuálisgép-ügynök nincs telepítve.
   * [Az Azure VM-ügynök telepítése Linuxos virtuális gépeken](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Más nem megfelelő állapotok esetén tekintse át a Log Analytics-ügynök Linux virtuálisgép-bővítmény naplófájljait, `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` és`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Ha a bővítmény állapota kifogástalan, de az adatok feltöltése nem történik meg, tekintse át a Log Analytics-ügynököt linuxos naplófájlokhoz a`/var/opt/microsoft/omsagent/log/omsagent.log`

További információt a [Linux-bővítmények hibaelhárítása című témakörben talál.](../../virtual-machines/extensions/oms-linux.md)

## <a name="next-steps"></a>További lépések

Az Azure Log Analytics Linux-ügynökkel kapcsolatos hibaelhárítási útmutatást az Azure [Log Analytics Linux-ügynökhiba elhárítása](agent-linux-troubleshoot.md)című témakörben talál.  

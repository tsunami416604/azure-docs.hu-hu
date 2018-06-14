---
title: Azure Naplóelemzés Analytics Virtuálisgép-bővítmény hibaelhárítása |} Microsoft Docs
description: A jelenség okok és a napló Analytics Virtuálisgép-bővítménnyel a leggyakoribb problémák megoldási írják le a Windows és Linux Azure virtuális gépeken.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/08/2018
ms.author: magoedte
ms.openlocfilehash: d1e70d8f9fb929e3877c88fd4c1169a0c76ac2a6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29394986"
---
# <a name="troubleshooting-the-log-analytics-vm-extension"></a>Hibaelhárítás a napló Analytics Virtuálisgép-bővítmény
Ez a cikk ismerteti a hibaelhárítást hibákat tapasztalhat a napló Analytics VM kiterjesztésű Windows és Linux rendszerű virtuális gépek Microsoft Azure-on futó, és elhárításának lehetséges megoldások javasolja.

A bővítmény állapotának ellenőrzéséhez hajtsa végre az alábbi lépéseket az Azure portálról.

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Az Azure portálon kattintson **minden szolgáltatás**. Írja be az erőforrások listájához, **virtuális gépek**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **virtuális gépek**.
3. A virtuális gépek listájában keresse meg és válassza ki azt.
3. Kattintson a virtuális gép **bővítmények**.
4. A listában ellenőrizze, hogy ha a Log Analyticshez bővítmény engedélyezve van-e.  Linux, az ügynök van megadva, **OMSAgentforLinux** pedig a Windows, az ügynök **MicrosoftMonitoringAgent**.

   ![Virtuálisgép-bővítmények nézetet](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. Kattintson a részletek megtekintéséhez a kiterjesztést. 

   ![Virtuálisgép-bővítmény részletei](./media/log-analytics-azure-vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Hibaelhárítási Azure Windows Virtuálisgép-bővítmény

Ha a *Microsoft Monitoring Agent* nem telepíti a Virtuálisgép-bővítmény vagy reporting, végezheti el a probléma elhárítása érdekében az alábbi lépéseket.

1. Az Azure Virtuálisgép-ügynök telepítésének ellenőrzése és működik a megfelelő segítségével lépéseit [KB 2965986](https://support.microsoft.com/kb/2965986#mt1).
   * Emellett áttekintheti a Virtuálisgép-ügynök naplófájlját `C:\WindowsAzure\logs\WaAppAgent.log`
   * Ha a napló nem létezik, a Virtuálisgép-ügynök nincs telepítve.
   * [Az Azure Virtuálisgép-ügynök telepítése](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Ellenőrizze, hogy a Microsoft Monitoring Agent bővítmény szívverés feladat fut-e az alábbi lépéseket követve:
   * Jelentkezzen be a virtuális gép
   * Nyissa meg a Feladatütemezőt, és keresse a `update_azureoperationalinsight_agent_heartbeat` feladat
   * Erősítse meg a feladat engedélyezve van, és egy percenként fut.
   * Ellenőrizze, hogy a szívverés logfile szerepel `C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\heartbeat.log`
3. Tekintse át a Microsoft Monitoring Agent VM bővítmény naplófájlokban `C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
4. Győződjön meg arról, a virtuális gép PowerShell-parancsfájlok is futtathatók.
5. Győződjön meg arról, nem módosította a C:\Windows\temp engedélyeinek
6. Egy rendszergazda jogú PowerShell-ablakot a virtuális gépen, a következőket írja be a Microsoft Monitoring Agent állapotának megtekintése `  (New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
7. Tekintse át a Microsoft Monitoring Agent telepítési naplófájlok `C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

További információkért lásd: [hibaelhárítása a Windows](../virtual-machines/windows/extensions-oms.md).

## <a name="troubleshooting-linux-vm-extension"></a>Hibaelhárítási Linux Virtuálisgép-bővítmény
Ha a *Linux OMS-ügynököt* nem telepíti a Virtuálisgép-bővítmény vagy reporting, végezheti el a probléma elhárítása érdekében az alábbi lépéseket.

1. Ha a bővítmény állapotát *ismeretlen* ellenőrizze, hogy ha az Azure Virtuálisgép-ügynök telepítve van és megfelelően működik-e a virtuális gép ügynök naplófájlját megtekintésével `/var/log/waagent.log`
   * Ha a napló nem létezik, a Virtuálisgép-ügynök nincs telepítve.
   * [Az Azure Virtuálisgép-ügynök telepítése Linux virtuális gépeken](log-analytics-quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. A többi nem kifogástalan állapot, tekintse át az OMS-ügynököt, a Linux Virtuálisgép-bővítmény naplózza a fájlokat `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` és `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Ha a bővítmény állapota kifogástalan, de nem feltöltött adatok tekintse át az OMS-ügynököt a Linux-naplófájl `/var/opt/microsoft/omsagent/log/omsagent.log`

További információkért lásd: [Linux bővítmények hibaelhárítása](../virtual-machines/linux/extensions-oms.md).

## <a name="next-steps"></a>További lépések

Az OMS-ügynököt az Azure-on kívüli számítógépeken futó Linux kapcsolatos további hibaelhárítási útmutatót lásd: [Azure Log Analytics Linux Agent hibaelhárítása](log-analytics-agent-linux-support.md).  

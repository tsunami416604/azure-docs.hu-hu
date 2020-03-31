---
title: Az Azure változáskövetéslel kapcsolatos problémák elhárítása
description: Ismerje meg, hogyan háríthatja el és oldhatja meg a problémákat az Azure Automation változáskövetés és készlet szolgáltatással.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198530"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>A Change Tracking és az Inventory hibaelhárítása

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Eset: A változáskövetési rekordok nem jelennek meg Windows-gépeken

#### <a name="issue"></a>Probléma

A változáskövetésre vagy a készletkövetésre vonatkozó anamnézis nem jelenik meg a változáskövetésre beépített Windows-gépeknél.

#### <a name="cause"></a>Ok

Ennek a hibának a következő okai lehetnek:

* A Microsoft monitoring ügynök nem fut.
* Az Automation-fiókhoz való visszakommunikáció le van tiltva.
* A rendszer nem tölti le a változások követéséhez való felügyeleti csomagokat.
* Előfordulhat, hogy a beépített virtuális gép olyan klónozott gépről származik, amely nem volt sysprepped a Microsoft Monitoring Agent telepítve.

#### <a name="resolution"></a>Megoldás:

Az alábbiakban ismertetett megoldások segíthetnek a probléma megoldásában. Ha továbbra is segítségre van szüksége, diagnosztikai információkat gyűjthet, és kapcsolatba léphet az ügyfélszolgálattal. Az ügynökszámítógépen keresse meg a C:\Program Files\Microsoft Monitoring Agent\Agent\Tools mappát, és futtassa a következő parancsokat:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Alapértelmezés szerint a hibakövetés engedélyezve van. A részletes hibaüzenetek engedélyezéséhez, mint az előző példában, használja a *VER* paramétert. A nyomon követést az *INF* parancs használja a **StartTracing.cmd**parancs meghívásakor.

##### <a name="microsoft-monitoring-agent-not-running"></a>A Microsoft monitoring ügynöke nem fut

Ellenőrizze, hogy a Microsoft Monitoring Agent (HealthService.exe) fut-e a számítógépen.

##### <a name="communication-to-automation-account-blocked"></a>Kommunikáció az Automation-fiókkal blokkolva

Ellenőrizze az Eseménynaplót a számítógépen, és keresse meg azokat az eseményeket, amelyekben a "changetracking" szó található.

Tekintse meg az [erőforrások automatizálása az adatközpontban vagy a felhőben a hibrid Runbook-feldolgozó használatával,](../automation-hybrid-runbook-worker.md#network-planning) hogy megismerje azokat a címeket és portokat, amelyeket engedélyezni kell a változáskövetés működéséhez.

##### <a name="management-packs-not-downloaded"></a>A felügyeleti csomagok nincsenek letöltve

Ellenőrizze, hogy a következő változáskövetési és készletfelügyeleti csomagok vannak-e telepítve a helyileg:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Nem sysprepped ként elnem írt klónozott gépvirtuális gépről

Ha klónozott lemezképet használ, először sysprep a lemezképet, majd telepítse a Microsoft Monitoring Agent alkalmazást.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Forgatókönyv: Nincs változáskövetés vagy leltáreredmények Linux-gépeken

#### <a name="issue"></a>Probléma

Nem jelennek meg olyan Leltár- vagy változáskövetési eredmények a változáskövetéshez beépített Linux-gépeknél. 

#### <a name="cause"></a>Ok
A probléma lehetséges okai:
* A Log Analytics-ügynök Linux nem fut.
* A Log Analytics-ügynök linuxos nincs megfelelően konfigurálva.
* Vannak fájlintegritás-figyelési (FIM) ütközések.

#### <a name="resolution"></a>Megoldás: 

##### <a name="log-analytics-agent-for-linux-not-running"></a>A Log Analytics ügynök linuxos futtatása nem

Ellenőrizze, hogy a Linux-naplóügynök (omsagent) démona fut-e a számítógépen. Futtassa a következő lekérdezést az Automation-fiókhoz kapcsolódó Log Analytics-munkaterületen.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Ha nem látja a gépet a lekérdezési eredmények ben, akkor nemrég nem jelentkezett be. Valószínűleg helyi konfigurációs probléma van, ezért újra kell telepítenie az ügynököt. A telepítésről és a konfigurációról a [Naplóadatok gyűjtése a Log Analytics-ügynökkel című témakörben](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)talál. 

Ha a gép megjelenik a lekérdezés eredményében, ellenőrizze a hatókör konfigurációját. Lásd: [Figyelésfigyelési megoldások az Azure Monitorban.](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)

A probléma további hibaelhárításához olvassa el a [Probléma: Nem lát Linux-adatokat.](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>A Log Analytics linuxos ügynöke nincs megfelelően konfigurálva

Előfordulhat, hogy a Linux-naplóügynök nincs megfelelően konfigurálva a napló- és parancssori kimeneti gyűjteményhez az OMS-naplógyűjtő eszközzel. Lásd: [Változások követése a környezetben a Változáskövetési megoldással.](../change-tracking.md)

##### <a name="fim-conflicts"></a>FIM-ütközések

Előfordulhat, hogy az Azure Security Center FIM-szolgáltatása helytelenül érvényesíti a Linux-fájlok integritását. Ellenőrizze, hogy a FIM működőképes-e, és megfelelően van-e konfigurálva a Linux-fájlfigyeléshez. Lásd: [Változások követése a környezetben a Változáskövetési megoldással.](../change-tracking.md)

## <a name="next-steps"></a>További lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, használja az alábbi csatornák egyikét további támogatásért.

* Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba – a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Ha további segítségre van szüksége, benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**

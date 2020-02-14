---
title: Az Azure Change Tracking hibáinak elhárítása
description: Megtudhatja, hogyan lehet elhárítani a Azure Automation Change Tracking és a leltár szolgáltatással kapcsolatos problémákat.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198530"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>A Change Tracking és az Inventory hibaelhárítása

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Forgatókönyv: Change Tracking a rekordok nem jelennek meg a Windows rendszerű gépeken

#### <a name="issue"></a>Probléma

Nem jelennek meg Change Tracking vagy leltározási eredmények a Change Tracking előkészített Windows-gépekhez.

#### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* A Microsoft monitoring Agent nem fut.
* A rendszer blokkolja az Automation-fiókkal való kommunikációt.
* A Change Tracking felügyeleti csomagjai nem tölthetők le.
* Előfordulhat, hogy a bevezetésben lévő virtuális gép olyan klónozott gépről származott, amely nem volt telepítve a Microsoft monitoring Agent Sysprep használatával létrehozott.

#### <a name="resolution"></a>Megoldás:

Az alábbiakban ismertetett megoldások segíthetnek megoldani a problémát. Ha továbbra is segítségre van szüksége, diagnosztikai információkat gyűjthet, és kapcsolatba léphet a támogatási szolgálattal. Az ügynök gépen navigáljon a C:\Program Files\Microsoft monitoring Agent\Agent\Tools, és futtassa a következő parancsokat:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Alapértelmezés szerint a hibák nyomon követése engedélyezve van. Az előző példában szereplő részletes hibaüzenetek engedélyezéséhez használja a *ver* paramétert. Az **StartTracing. cmd**meghívásakor használja az *inf* -et.

##### <a name="microsoft-monitoring-agent-not-running"></a>A Microsoft monitoring Agent nem fut

Ellenőrizze, hogy a számítógépen fut-e a Microsoft monitoring Agent (HealthService. exe).

##### <a name="communication-to-automation-account-blocked"></a>Az Automation-fiókkal folytatott kommunikáció blokkolva

Ellenőrizze Eseménynapló a gépen, és keresse meg a "változáskövetési" szót tartalmazó eseményeket.

Az [adatközpontban vagy a felhőben lévő erőforrások automatizálása hibrid Runbook-feldolgozóval](../automation-hybrid-runbook-worker.md#network-planning) című cikkből megtudhatja, hogy mely címeket és portokat kell engedélyezni a Change Tracking működéséhez.

##### <a name="management-packs-not-downloaded"></a>Nem letöltött felügyeleti csomagok

Győződjön meg arról, hogy a következő Change Tracking és leltár-felügyeleti csomagok helyileg vannak telepítve:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>A klónozott gépről nem Sysprep használatával létrehozott virtuális gép

Klónozott rendszerkép használata esetén először a rendszerképet, majd a Microsoft monitoring Agent telepítését kell telepítenie.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Forgatókönyv: nincs Change Tracking vagy leltározási eredmény Linux rendszerű gépeken

#### <a name="issue"></a>Probléma

Nem jelennek meg olyan leltározási és Change Trackingi eredmények, amelyek Change Tracking előkészített linuxos gépekre vonatkoznak. 

#### <a name="cause"></a>Ok
A probléma lehetséges okai a következők:
* A Linux Log Analytics ügynöke nem fut.
* A Log Analytics-ügynök Linux rendszerhez nincs megfelelően konfigurálva.
* A fájlok integritásának figyelése (FIM) ütközik.

#### <a name="resolution"></a>Megoldás: 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics a Linux-ügynök nem fut

Győződjön meg arról, hogy a (z) Linux rendszerhez készült Log Analytics-ügynök (omsagent) démona fut a gépen. Futtassa az alábbi lekérdezést a Log Analytics munkaterületen, amely az Automation-fiókjához van csatolva.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Ha nem látja a gépet a lekérdezés eredményei között, a közelmúltban nem volt bejelölve. Valószínűleg van egy helyi konfigurációs probléma, és újra kell telepítenie az ügynököt. További információ a telepítésről és a konfigurálásról: [a naplófájlok adatainak összegyűjtése a log Analytics ügynökkel](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Ha a gép megjelenik a lekérdezés eredményei között, ellenőrizze a hatókör-konfigurációt. Lásd: [Azure monitor-figyelési megoldások célzása](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

A probléma megoldásához tekintse meg a következő [témakört: probléma: nem jelenik meg Linux-alapú információ](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>A Linux-ügynök nem megfelelően van konfigurálva Log Analytics

Előfordulhat, hogy a Linux rendszerhez készült Log Analytics-ügynök nem megfelelően van konfigurálva a log és a parancssori kimeneti gyűjteményhez a OMS log Collector eszközzel. Lásd: a [környezet változásainak követése a Change Tracking megoldással](../change-tracking.md).

##### <a name="fim-conflicts"></a>FIM-ütközések

Előfordulhat, hogy a Azure Security Center FIM-funkciója helytelenül ellenőrzi a Linux-fájlok integritását. Ellenőrizze, hogy a FIM működik-e, és megfelelően van-e konfigurálva a Linux-fájlok figyeléséhez. Lásd: a [környezet változásainak követése a Change Tracking megoldással](../change-tracking.md).

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, használja a következő csatornák egyikét a további támogatáshoz.

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* Az [@AzureSupport](https://twitter.com/azuresupport) a Microsoft Azure hivatalos Twitter-fiókja, amelyen keresztül a jobb felhasználói élmény érdekében igyekszünk az Azure-felhasználók közösségét ellátni a megfelelő forrásokkal: válaszokkal, támogatással és szakértői segítséggel.
* Ha további segítségre van szüksége, egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.

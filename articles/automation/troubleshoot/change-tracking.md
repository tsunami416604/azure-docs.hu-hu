---
title: A Change Tracking és a leltárral kapcsolatos problémák elhárítása
description: Megtudhatja, hogyan lehet elhárítani a Azure Automation Change Tracking és a leltározási megoldással kapcsolatos problémákat.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f230cd0965d58f690d333cd62f2c7c1d499e8d1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582146"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>A Change Tracking és a leltárral kapcsolatos problémák elhárítása

Ez a cikk ismerteti, hogyan lehet elhárítani a Azure Automation Change Tracking és a leltárral kapcsolatos problémákat.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az az modul telepítési útmutatója a hibrid Runbook-feldolgozón: [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Az Automation-fiók esetében a modulokat a legújabb verzióra frissítheti a [Azure Automation Azure PowerShell moduljainak frissítésével](../automation-update-azure-modules.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Forgatókönyv: a Change Tracking és a leltári rekordok nem láthatók Windows rendszerű gépek esetén

#### <a name="issue"></a>Probléma

Nem jelenik meg Change Tracking és leltározási eredmény a bekészített Windows-gépekhez.

#### <a name="cause"></a>Ok

Ez a hiba a következő okok miatt fordulhat elő:

* A Windows rendszerhez készült Azure Log Analytics-ügynök nem fut.
* A rendszer blokkolja az Automation-fiókkal való kommunikációt.
* A Change Tracking és a leltár felügyeleti csomagjai nem tölthetők le.
* Előfordulhat, hogy a bevezetésben lévő virtuális gép olyan klónozott gépről származik, amely nem volt Sysprep használatával létrehozott a Windows rendszerhez telepített Log Analytics ügynökkel.

#### <a name="resolution"></a>Megoldás:

A Log Analytics ügynök gépen lépjen a **C:\Program Files\Microsoft monitoring Agent\Agent\Tools** , és futtassa a következő parancsokat:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Ha továbbra is segítségre van szüksége, diagnosztikai információkat gyűjthet, és kapcsolatba léphet a támogatási szolgálattal.

> [!NOTE]
> A Log Analytics ügynök alapértelmezés szerint engedélyezi a hibák nyomkövetését. Az előző példában szereplő részletes hibaüzenetek engedélyezéséhez használja a `VER` paramétert. Az információk követéséhez használja `INF` a következőt: `StartTracing.cmd`.

##### <a name="log-analytics-agent-for-windows-not-running"></a>Log Analytics ügynök nem fut a Windows rendszerben

Ellenőrizze, hogy a Windows rendszerhez készült Log Analytics ügynök (**HealthService. exe**) fut-e a gépen.

##### <a name="communication-to-automation-account-blocked"></a>Az Automation-fiókkal folytatott kommunikáció blokkolva

Ellenőrizze Eseménynapló a gépen, és keresse meg azokat az eseményeket, amelyekben szó `changetracking` van bennük.

A Change Tracking és a leltár működéséhez engedélyezni kívánt címekkel és portokkal kapcsolatos információkért lásd: [erőforrások automatizálása az adatközpontban vagy a felhőben hibrid Runbook-feldolgozók használatával](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Nem letöltött felügyeleti csomagok

Győződjön meg arról, hogy a következő Change Tracking és leltár-felügyeleti csomagok helyileg vannak telepítve:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>A klónozott gépről nem Sysprep használatával létrehozott virtuális gép

Klónozott rendszerkép használata esetén először a rendszerképet, majd a Windows rendszerhez készült Log Analytics-ügynököt telepítse.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Forgatókönyv: nincs Change Tracking és a leltár eredményei a Linux rendszerű gépeken

#### <a name="issue"></a>Probléma

Nem jelennek meg a megoldásba bekészített linuxos gépek Change Tracking és leltározási eredményei. 

#### <a name="cause"></a>Ok
A probléma lehetséges okai a következők:
* A Linux Log Analytics ügynöke nem fut.
* A Log Analytics-ügynök Linux rendszerhez nincs megfelelően konfigurálva.
* A fájlok integritásának figyelése (FIM) ütközik.

#### <a name="resolution"></a>Megoldás: 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Log Analytics a Linux-ügynök nem fut

Győződjön meg arról, hogy a (z) Linux rendszerhez készült Log Analytics-ügynök (**omsagent**) démona fut a gépen. Futtassa az alábbi lekérdezést a Log Analytics munkaterületen, amely az Automation-fiókjához van csatolva.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Ha nem látja a gépet a lekérdezés eredményei között, a közelmúltban nem volt bejelölve. Valószínűleg van egy helyi konfigurációs probléma, és újra kell telepítenie az ügynököt. További információ a telepítésről és a konfigurálásról: [a naplófájlok adatainak összegyűjtése a log Analytics ügynökkel](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent).

Ha a gép megjelenik a lekérdezés eredményei között, ellenőrizze a hatókör-konfigurációt. Lásd: [Azure monitor-figyelési megoldások célzása](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

A probléma további hibaelhárítását lásd [: probléma: nem jelenik meg Linux-alapú információ](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>A Linux-ügynök nem megfelelően van konfigurálva Log Analytics

Előfordulhat, hogy a Linux rendszerhez készült Log Analytics-ügynök nem megfelelően van konfigurálva a log és a parancssori kimeneti gyűjteményhez a OMS log Collector eszközzel. Lásd: a [környezet változásainak követése a Change Tracking és a leltár megoldással](../change-tracking.md).

##### <a name="fim-conflicts"></a>FIM-ütközések

Előfordulhat, hogy a Azure Security Center FIM-funkciója helytelenül ellenőrzi a Linux-fájlok integritását. Ellenőrizze, hogy a FIM működik-e, és megfelelően van-e konfigurálva a Linux-fájlok figyeléséhez. Lásd: a [környezet változásainak követése a Change Tracking és a leltár megoldással](../change-tracking.md).

## <a name="next-steps"></a>További lépések

Ha itt nem találja a problémát, vagy nem tudja elhárítani a problémát, próbálja ki a következő csatornák egyikét a további támogatáshoz:

* Választ kaphat az Azure-szakértőktől az [Azure-fórumokon](https://azure.microsoft.com/support/forums/).
* A szolgáltatással való együttműködéshez [@AzureSupport](https://twitter.com/azuresupport)a hivatalos Microsoft Azure fiók a felhasználói élmény javítása érdekében. Az Azure-támogatás a válaszokat, támogatást és szakértőket az Azure-Közösséggel köti össze.
* Azure-támogatási incidens küldése. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/), és válassza a **támogatás kérése**lehetőséget.

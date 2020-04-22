---
title: A változások nyomon követésével és készletével kapcsolatos problémák elhárítása
description: Ismerje meg, hogyan háríthatja el és oldhatja meg az Azure Automation változáskövetés és készletszolgáltatás megoldásával kapcsolatos problémákat.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 11c1fd05055922b07801c20d525d852d5360b069
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679352"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Változáskövetési és készlethibák elhárítása

Ez a cikk a változások nyomon követésével és a készletekkel kapcsolatos problémák elhárítását ismerteti.

>[!NOTE]
>A cikk frissítve lett az Azure PowerShell új Az moduljának használatával. Dönthet úgy is, hogy az AzureRM modult használja, amely továbbra is megkapja a hibajavításokat, legalább 2020 decemberéig. Ha többet is meg szeretne tudni az új Az modul és az AzureRM kompatibilitásáról, olvassa el [az Azure PowerShell új Az moduljának ismertetését](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Az Az modul telepítési utasításait a hibrid Runbook-feldolgozó, [az Azure PowerShell-modul telepítése.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Automation-fiókjához frissítheti a modulokat a legújabb verzióra az [Azure PowerShell-modulok frissítése az Azure Automationben.](../automation-update-azure-modules.md)

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Eset: A változáskövetési és készletrekordok nem jelennek meg Windows-gépeken

#### <a name="issue"></a>Probléma

A fedélzetre épített Windows-gépeknél nem jelennek meg változáskövetési és leltári eredmények.

#### <a name="cause"></a>Ok

Ennek a hibának a következő okai lehetnek:

* A Windows Log Analytics-ügynöke nem fut.
* Az Automation-fiókhoz való visszakommunikáció le van tiltva.
* A rendszer nem tölti le a változáskövetés tések és a készletkezelés csomagokat.
* Előfordulhat, hogy a beépített virtuális gép olyan klónozott gépről származik, amely nem volt sysprepped a Windows Log Analytics ügynöktelepítve.

#### <a name="resolution"></a>Megoldás:

A Log Analytics ügynökgépen keresse meg a **C:\Program Files\Microsoft Monitoring Agent\Agent\Tools mappát,** és futtassa a következő parancsokat:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Ha továbbra is segítségre van szüksége, diagnosztikai információkat gyűjthet, és kapcsolatba léphet az ügyfélszolgálattal. 

> [!NOTE]
> A Log Analyticss ügynök alapértelmezés szerint engedélyezi a hibakövetést. A részletes hibaüzenetek engedélyezéséhez, mint az `VER` előző példában, használja a paramétert. A nyomkövetéshez `INF` használja a `StartTracing.cmd`meghíváshoz.

##### <a name="log-analytics-agent-for-windows-not-running"></a>A Windows log Analytics ügynöke nem fut

Ellenőrizze, hogy a Windows Log Analytics ügynöke (**HealthService.exe**) fut-e a számítógépen.

##### <a name="communication-to-automation-account-blocked"></a>Kommunikáció az Automation-fiókkal blokkolva

Ellenőrizze az Eseménynaplót a számítógépen, és `changetracking` keresse meg azokat az eseményeket, amelyekben a szó található.

Tekintse meg az [erőforrások automatizálása az adatközpontban vagy a felhőben a Hibrid Runbook-feldolgozó használatával,](../automation-hybrid-runbook-worker.md#network-planning) hogy megismerje azokat a címeket és portokat, amelyeket engedélyezni kell a változáskövetés és a készlethasználat hoz.

##### <a name="management-packs-not-downloaded"></a>A felügyeleti csomagok nincsenek letöltve

Ellenőrizze, hogy a következő változáskövetési és készletfelügyeleti csomagok vannak-e telepítve a helyileg:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>Nem sysprepped ként elnem írt klónozott gépvirtuális gépről

Ha klónozott lemezképet használ, először sysprep a lemezképet, majd telepítse a Windows Log Analytics-ügynökét.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Forgatókönyv: Nincs változáskövetés és leltáreredmények Linux-gépeken

#### <a name="issue"></a>Probléma

A megoldáshoz beépített Linux-gépek nél nem jelennek meg leltár- és változáskövetési eredmények. 

#### <a name="cause"></a>Ok
A probléma lehetséges okai:
* A Log Analytics-ügynök Linux nem fut.
* A Log Analytics-ügynök linuxos nincs megfelelően konfigurálva.
* Vannak fájlintegritás-figyelési (FIM) ütközések.

#### <a name="resolution"></a>Megoldás: 

##### <a name="log-analytics-agent-for-linux-not-running"></a>A Log Analytics ügynök linuxos futtatása nem

Ellenőrizze, hogy a Linuxi Log Analytics-ügynök (**omsagent**) démona fut-e a számítógépen. Futtassa a következő lekérdezést az Automation-fiókhoz kapcsolódó Log Analytics-munkaterületen.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Ha nem látja a gépet a lekérdezési eredmények ben, akkor nemrég nem jelentkezett be. Valószínűleg helyi konfigurációs probléma van, ezért újra kell telepítenie az ügynököt. A telepítésről és a konfigurációról a [Naplóadatok gyűjtése a Log Analytics-ügynökkel című témakörben](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)talál. 

Ha a gép megjelenik a lekérdezés eredményében, ellenőrizze a hatókör konfigurációját. Lásd: [Figyelésfigyelési megoldások az Azure Monitorban.](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)

A probléma további hibaelhárításáról a [Probléma: Nem lát Linux-adatokat.](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>A Log Analytics linuxos ügynöke nincs megfelelően konfigurálva

Előfordulhat, hogy a Linux-naplóügynök nincs megfelelően konfigurálva a napló- és parancssori kimeneti gyűjteményhez az OMS-naplógyűjtő eszközzel. Lásd: [Változások követése a környezetben a Változáskövetés és Készlet megoldással.](../change-tracking.md)

##### <a name="fim-conflicts"></a>FIM-ütközések

Előfordulhat, hogy az Azure Security Center FIM-szolgáltatása helytelenül érvényesíti a Linux-fájlok integritását. Ellenőrizze, hogy a FIM működőképes-e, és megfelelően van-e konfigurálva a Linux-fájlfigyeléshez. Lásd: [Változások követése a környezetben a Változáskövetés és Készlet megoldással.](../change-tracking.md)

## <a name="next-steps"></a>További lépések

Ha a fenti probléma nem jelenik meg, vagy nem tudja megoldani a problémát, próbálkozzon az alábbi csatornák egyikével további támogatásért:

* Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
* Lépjen [@AzureSupport](https://twitter.com/azuresupport)kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Az Azure-támogatási incidens fájlja. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**

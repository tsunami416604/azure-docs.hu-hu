---
title: Ügynökállapot megoldás az Azure-ban |} A Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan használhatja ezt a megoldást a közvetlenül a Log Analytics vagy a System Center Operations Manager ügynökök állapotának figyeléséhez nyújt segítséget.
services: operations-management-suite
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/19/2017
ms.author: magoedte
ms.openlocfilehash: 203a37071637a7e0e44b65240be4c4cae974d95f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53335956"
---
#  <a name="agent-health-solution-in-azure"></a>Ügynökállapot megoldás az Azure-ban
Az Azure-ban az Agent Health megoldás segít átlátni, az összes jelentő ügynökök közül közvetlenül a Log Analytics-munkaterületet, vagy a System Center Operations Manager felügyeleti csoport csatlakoztatva a Log Analytics, amelyek nem válaszol, a üzemeltetési elküldése adatok.  Azt is nyomon követheti, hogy hány ügynök lett üzembe helyezve, és milyen a földrajzi eloszlásuk, illetve egyéb lekérdezéseket hajthat végre, amelyekkel megismerheti az Azure-ban, egyéb felhőkörnyezetekben, illetve helyszínen üzembe helyezett ügynökök eloszlását.    

## <a name="prerequisites"></a>Előfeltételek
A megoldás telepítése előtt győződjön meg arról, hogy rendelkezik aktuálisan támogatott [Windows-ügynökök](../../log-analytics/log-analytics-windows-agent.md) a Log Analytics-munkaterületre jelentő vagy a jelentéskészítés egy [Operations Manager felügyeleti csoport](../../azure-monitor/platform/om-agents.md) integrálva van a munkaterület.    

## <a name="solution-components"></a>Megoldás-összetevők
Ez a megoldás a következő erőforrásokból áll, amelyek a munkaterületéhez lesznek hozzáadva, és ügynökökhöz vagy az Operations Managerhez kapcsolt felügyeleti csoporthoz lesznek közvetlenül hozzákapcsolva.

### <a name="management-packs"></a>Felügyeleti csomagok
Ha a System Center Operations Manager felügyeleti csoportban a Log Analytics-munkaterülethez van csatlakoztatva, a következő felügyeleti csomagokat az Operations Manager vannak telepítve.  Ezeket a felügyeleti csomagokat a megoldás hozzáadását követően a rendszer a közvetlenül kapcsolódó Windows rendszerű számítógépekre is telepíti. A felügyeleti csomagokat nem szükséges konfigurálni vagy felügyelni.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../../azure-monitor/platform/om-agents.md).

## <a name="configuration"></a>Konfiguráció
Adja hozzá az Agent Health megoldás a Log Analytics-munkaterülethez ismertetett folyamatot [megoldások hozzáadása](solutions.md). Nincs szükség további konfigurációra.


## <a name="data-collection"></a>Adatgyűjtés
### <a name="supported-agents"></a>Támogatott ügynökök
Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök | Igen | A szívverés eseményeket a rendszer a közvetlen Windows-ügynököktől gyűjti össze.|
| System Center Operations Manage felügyeleti csoport | Igen | A szívverés eseményeket a rendszer a felügyeleti csoportnak jelentő ügynököktől gyűjti össze 60 másodpercenként, majd továbbítja az eseményeket a Log Analytics felé. Ehhez nem szükséges, hogy közvetlen kapcsolat legyen az Operations Manager-ügynökök és a Log Analytics között. A szívverés események adatai a felügyeleti csoportból a Log Analytics-tárházba lesznek továbbítva.|

## <a name="using-the-solution"></a>A megoldás használata
A Log Analytics-munkaterületet a megoldás hozzáadásakor az **ügynökállapot** csempe felkerül az irányítópulton. Ezen a csempén látható az ügynökök teljes száma és az elmúlt 24 órában nem válaszoló ügynökök száma.<br><br> ![Ügynökállapot megoldás csempe az irányítópulton](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Kattintson az **Ügynökállapot** csempére az **Ügynökállapot** irányítópult megnyitásához.  Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Mindegyik oszlop felsorolja azt a tíz eseményt, amelyek a legjobban megfelelnek az adott oszlop időtartományi feltételének. Az oszlopok alján jobb oldalon található **Az összes megtekintése** elemet vagy az oszlopok fejlécét kiválasztva a teljes listát lefedő keresést végezhet a naplóban.

| Oszlop | Leírás |
|--------|-------------|
| Ügynökök darabszáma egységidő alatt | Az ügynökök számának trendje egy hét napos időszakra vetítve, a Linux- és Windows-ügynököket is beleértve.|
| Nem válaszoló ügynökök száma | Azon ügynökök listája, amelyek az elmúlt 24 órában nem küldtek szívverést.|
| Eloszlás operációsrendszer-típusok szerint | A környezetben található Windows- és Linux-ügynökök számának eloszlása.|
| Eloszlás ügynökverzió szerint | A környezetben telepített különböző ügynökverziók eloszlása és mennyisége.|
| Eloszlás ügynökkategória szerint | A szívverés eseményeket küldő különböző ügynökök kategóriáinak eloszlása: közvetlen ügynökök, OpsMgr-ügynökök és az OpsMgr felügyeleti kiszolgáló.|
| Eloszlás felügyeleti csoport szerint | A környezetben található különböző SCOM-felügyeleticsoportok eloszlása.|
| Az ügynökök földrajzi helye | A különböző országok eloszlása, ahol ügynökök találhatók, és az egyes országokban telepített ügynökök teljes mennyisége.|
| Telepített átjárók száma | A Log Analytics-átjáró telepítésével rendelkező kiszolgálók számát, és ezek a kiszolgálók listáját.|

![Ügynökállapot megoldás irányítópultja – példa](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Log Analytics-rekordok
A megoldás egyféle típusú rekord a Log Analytics-munkaterületet hoz létre.  

### <a name="heartbeat-records"></a>Szívverés rekordok
Egy **Szívverés** típusú rekord készül.  Ezen rekordok tulajdonságait az alábbi táblázat ismerteti.  

| Tulajdonság | Leírás |
| --- | --- |
| Típus | *Szívverés*|
| Kategória | Az érték lehet *Direct Agent* (Közvetlen ügynök), *SCOM Agent* (SCOM-ügynök) vagy *SCOM Management Server* (SCOM felügyeleti kiszolgáló).|
| Computer | A számítógép neve.|
| OSType | Windows vagy Linux operációs rendszer.|
| OSMajorVersion | Az operációs rendszer főverziója.|
| OSMinorVersion | Az operációs rendszer alverziója.|
| Verzió | Log Analytics-ügynök vagy Operations Manager-ügynök verziója.|
| SCAgentChannel | Az érték *Direct* (Közvetlen) és/vagy *SCManagementServer*.|
| IsGatewayInstalled | Ha a Log Analytics-átjáró telepítve van, az értéke *igaz*, más esetben *hamis*.|
| ComputerIP | A számítógép IP-címe.|
| RemoteIPCountry | A földrajzi hely, ahol a számítógép üzemel.|
| ManagementGroupName | Az Operations Manager felügyeleti csoportjának neve.|
| SourceComputerId | A számítógép egyedi azonosítója.|
| RemoteIPLongitude | A számítógép földrajzi helyének hosszúsági koordinátája.|
| RemoteIPLatitude | A számítógép fölrajzi helyének szélességi koordinátája.|

Minden egyes Operations Manager felügyeleti kiszolgáló felé jelentő ügynök két szívverést küld, és az SCAgentChannel tulajdonság értéke egyaránt tartalmazza **közvetlen** és **SCManagementServer** attól függően, melyik napló Analytics-adatforrások és megoldások engedélyezte az előfizetésében. Ha a már ismert, a megoldások adatait vagy küldi közvetlenül egy Operations Manager felügyeleti kiszolgáló a Log Analyticsbe, vagy az ügynök összegyűjtött adatok mennyisége miatt az ügynök közvetlenül a Log Analytics felé küldött. Az **SCManagementServer** értékű szívverések esetében a ComputerIP értéke a felügyeleti kiszolgáló IP-címe, mivel ez tölti fel az adatokat.  Azoknál a szívveréseknél, ahol az SCAgentChannel beállítása **Direct** (Közvetlen), ez az ügynök nyilvános IP-címe.  

## <a name="sample-log-searches"></a>Naplókeresési minták
A következő táblázat a megoldás által összegyűjtött rekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

| Lekérdezés | Leírás |
|:---|:---|
| Heartbeat &#124; distinct Computer |Az ügynökök száma összesen |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Az elmúlt 24 órában nem válaszoló ügynökök száma |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Az elmúlt 15 percben nem válaszoló ügynökök száma |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Online számítógépek száma (az elmúlt 24 órában) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Az elmúlt 30 percben offline ügynökök száma (az elmúlt 24 órára vonatkozóan) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Az ügynökök számának trendje időbeli alakulásának lekérése operációsrendszer-típusonként|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Eloszlás operációsrendszer-típusok szerint |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Eloszlás ügynökverzió szerint |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Eloszlás ügynökkategória szerint |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Eloszlás felügyeleti csoport szerint |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Az ügynökök földrajzi helye |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Log Analytics a telepített átjárók száma |




## <a name="next-steps"></a>További lépések

* A Log Analytics-riasztások létrehozásával kapcsolatos információkért lásd: [Riasztások a Log Analyticsben](../../azure-monitor/platform/alerts-overview.md). 

---
title: Ügynökállapot-megoldás az Azure Monitorban | Microsoft dokumentumok
description: Ez a cikk segít megérteni, hogyan használhatja ezt a megoldást a Log Analytics vagy a System Center Operations Manager számára közvetlenül a Log Analytics vagy a System Center Operations Manager számára jelentett ügynökök állapotának figyelésére.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: 7093e20473b799a3f05ddf30803721636732241e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663257"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Ügynökállapot-megoldás az Azure Monitorban
Az Azure-beli Agent Health-megoldás segít megérteni az összes olyan ügynök számára, aki közvetlenül az Azure Monitor Log Analytics-munkaterületére vagy az Azure Monitorhoz csatlakoztatott System Center Operations Manager felügyeleti csoportra jelentkezik, amelyek nem válaszolnak és működési adatok benyújtása.  Azt is nyomon követheti, hogy hány ügynök lett üzembe helyezve, és milyen a földrajzi eloszlásuk, illetve egyéb lekérdezéseket hajthat végre, amelyekkel megismerheti az Azure-ban, egyéb felhőkörnyezetekben, illetve helyszínen üzembe helyezett ügynökök eloszlását.    

## <a name="prerequisites"></a>Előfeltételek
A megoldás üzembe helyezése előtt ellenőrizze, hogy jelenleg támogatott-e a Log Analytics-munkaterületnek jelentett [Windows-ügynökök,](../../log-analytics/log-analytics-windows-agent.md) vagy a munkaterülettel integrált [Operations Manager felügyeleti csoportnak.](../../azure-monitor/platform/om-agents.md)

## <a name="solution-components"></a>Megoldás-összetevők
Ez a megoldás a következő erőforrásokból áll, amelyek a munkaterületéhez lesznek hozzáadva, és ügynökökhöz vagy az Operations Managerhez kapcsolt felügyeleti csoporthoz lesznek közvetlenül hozzákapcsolva.

### <a name="management-packs"></a>Felügyeleti csomagok
Ha a System Center Operations Manager felügyeleti csoportja egy Log Analytics-munkaterülethez csatlakozik, a következő felügyeleti csomagok települnek az Operations Manager betelepülése.  Ezeket a felügyeleti csomagokat a megoldás hozzáadását követően a rendszer a közvetlenül kapcsolódó Windows rendszerű számítógépekre is telepíti. A felügyeleti csomagokat nem szükséges konfigurálni vagy felügyelni.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../../azure-monitor/platform/om-agents.md).

## <a name="configuration"></a>Konfiguráció
Adja hozzá az Agent Health-megoldást a Log Analytics-munkaterülethez a [Megoldások hozzáadása](solutions.md)című részben ismertetett folyamat tal. Nincs szükség további konfigurációra.


## <a name="data-collection"></a>Adatgyűjtés
### <a name="supported-agents"></a>Támogatott ügynökök
Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök | Igen | A szívverés eseményeket a rendszer a közvetlen Windows-ügynököktől gyűjti össze.|
| System Center Operations Manage felügyeleti csoport | Igen | A rendszer 60 másodpercenként gyűjti a szívverési eseményeket a felügyeleti csoportnak jelentést tevő ügynököktől, majd továbbítja őket az Azure Monitornak. Az Operations Manager-ügynökök és az Azure Monitor közvetlen kapcsolata nem szükséges. A szívverési esemény adatok továbbítása a felügyeleti csoportból a Log Analytics munkaterületre.|

## <a name="using-the-solution"></a>A megoldás használata
Amikor hozzáadja a megoldást a Log Analytics-munkaterülethez, az **Ügynök állapota** csempe hozzáadódik az irányítópulthoz. Ezen a csempén látható az ügynökök teljes száma és az elmúlt 24 órában nem válaszoló ügynökök száma.<br><br> ![Ügynökállapot megoldás csempe az irányítópulton](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Kattintson az **Ügynökállapot** csempére az **Ügynökállapot** irányítópult megnyitásához.  Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Mindegyik oszlop felsorolja azt a tíz eseményt, amelyek a legjobban megfelelnek az adott oszlop időtartományi feltételének. Az oszlopok alján jobb oldalon található **Az összes megtekintése** elemet vagy az oszlopok fejlécét kiválasztva a teljes listát lefedő keresést végezhet a naplóban.

| Oszlop | Leírás |
|--------|-------------|
| Ügynökök darabszáma egységidő alatt | Az ügynökök számának trendje egy hét napos időszakra vetítve, a Linux- és Windows-ügynököket is beleértve.|
| Nem válaszoló ügynökök száma | Azon ügynökök listája, amelyek az elmúlt 24 órában nem küldtek szívverést.|
| Eloszlás operációsrendszer-típusok szerint | A környezetben található Windows- és Linux-ügynökök számának eloszlása.|
| Eloszlás ügynökverzió szerint | A környezetben telepített különböző ügynökverziók eloszlása és mennyisége.|
| Eloszlás ügynökkategória szerint | A szívverés eseményeket küldő különböző ügynökök kategóriáinak eloszlása: közvetlen ügynökök, OpsMgr-ügynökök és az OpsMgr felügyeleti kiszolgáló.|
| Eloszlás felügyeleti csoport szerint | A különböző Operations Manager-felügyeleti csoportok partíciója a környezetben.|
| Az ügynökök földrajzi helye | A különböző országok/régiók azon partíciója, ahol ügynökök találhatók, és az egyes országokban/régiókban telepített ügynökök teljes száma.|
| Telepített átjárók száma | A Log Analytics átjárót telepített kiszolgálók száma, valamint ezeknek a kiszolgálóknak a listája.|

![Ügynökállapot megoldás irányítópultja – példa](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Az Azure Monitor naplórekordjai
A megoldás egy rekordtípust hoz létre a Log Analytics-munkaterületen.  

### <a name="heartbeat-records"></a>Szívverés rekordok
Egy **Szívverés** típusú rekord készül.  Ezen rekordok tulajdonságait az alábbi táblázat ismerteti.  

| Tulajdonság | Leírás |
| --- | --- |
| `Type` | *Szívverés*|
| `Category` | Az érték lehet *Direct Agent* (Közvetlen ügynök), *SCOM Agent* (SCOM-ügynök) vagy *SCOM Management Server* (SCOM felügyeleti kiszolgáló).|
| `Computer` | A számítógép neve.|
| `OSType` | Windows vagy Linux operációs rendszer.|
| `OSMajorVersion` | Az operációs rendszer főverziója.|
| `OSMinorVersion` | Az operációs rendszer alverziója.|
| `Version` | Log Analytics ügynök vagy Operations Manager Ügynök verziója.|
| `SCAgentChannel` | Az érték *Direct* (Közvetlen) és/vagy *SCManagementServer*.|
| `IsGatewayInstalled` | Ha a Log Analytics átjáró telepítve van, az érték *igaz*, ellenkező esetben az érték *hamis*.|
| `ComputerIP` | A számítógép nyilvános IP-címe. Az Azure-beli virtuális gépeken ez a nyilvános IP-címet jeleníti meg, ha van elérhető. A privát IP-címeket használó virtuális gépek esetén ez az Azure SNAT-címet (nem a privát IP-címet) jeleníti meg. |
| `RemoteIPCountry` | A földrajzi hely, ahol a számítógép üzemel.|
| `ManagementGroupName` | Az Operations Manager felügyeleti csoportjának neve.|
| `SourceComputerId` | A számítógép egyedi azonosítója.|
| `RemoteIPLongitude` | A számítógép földrajzi helyének hosszúsági koordinátája.|
| `RemoteIPLatitude` | A számítógép fölrajzi helyének szélességi koordinátája.|

Az Operations Manager felügyeleti kiszolgálónak jelentett minden ügynök két szívverést küld, és az SCAgentChannel tulajdonság értéke a **Direct** és **az SCManagementServer** értéket is tartalmazza, attól függően, hogy milyen adatforrásokat és figyelési megoldásokat engedélyezett az előfizetésben. Ha visszahívja, a megoldásokból származó adatokat vagy közvetlenül az Operations Manager felügyeleti kiszolgálóról küldi el az Azure Monitornak, vagy az ügynöken gyűjtött adatok mennyisége miatt a rendszer közvetlenül az ügynöktől küldi el az Azure Monitornak. Az **SCManagementServer** értékű szívverések esetében a ComputerIP értéke a felügyeleti kiszolgáló IP-címe, mivel ez tölti fel az adatokat.  Azoknál a szívveréseknél, ahol az SCAgentChannel beállítása **Direct** (Közvetlen), ez az ügynök nyilvános IP-címe.  

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
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Telepített Log Analytics-átjárók száma |




## <a name="next-steps"></a>További lépések

* Ismerje meg [az Azure Monitor riasztások](../platform/alerts-overview.md) a naplólekérdezésekből származó riasztások létrehozásáról. 

---
title: "Ügynökállapot megoldás az OMS-ben | Microsoft Docs"
description: "Ebből a cikkből megtudhatja, hogyan használhatja ezt a megoldást azon ügynökök állapotának monitorozására, amelyek közvetlenül az OMS vagy a System Center Operations Manager felé jelentenek."
services: operations-management-suite
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: b810e37e393ddab55500f636b72450789285a4f0
ms.contentlocale: hu-hu
ms.lasthandoff: 07/28/2017

---
#  <a name="agent-health-solution-in-oms"></a>Ügynökállapot megoldás
Az ügynökállapot megoldás segít átlátni, hogy az OMS munkaterület vagy egy OMS-hez csatlakozó System Center Operations Manager-felügyeleticsoport felé jelentő ügynökök közül melyik nem válaszol, és melyik küld működési adatokat.  Azt is nyomon követheti, hogy hány ügynök lett üzembe helyezve, és milyen a földrajzi eloszlásuk, illetve egyéb lekérdezéseket hajthat végre, amelyekkel megismerheti az Azure-ban, egyéb felhőkörnyezetekben, illetve helyszínen üzembe helyezett ügynökök eloszlását.    

## <a name="prerequisites"></a>Előfeltételek
A megoldás telepítése előtt győződjön meg arról, hogy rendelkezik aktuálisan támogatott [Windows-ügynökökkel](../log-analytics/log-analytics-windows-agents.md), amelyek az OMS-munkaterület felé, vagy egy, az OMS-munkaterülettel integrált [Operations Manager-felügyeleticsoport](../log-analytics/log-analytics-om-agents.md) felé jelentenek.    

## <a name="solution-components"></a>Megoldás-összetevők
Ez a megoldás a következő erőforrásokból áll, amelyek a munkaterületéhez lesznek hozzáadva, és ügynökökhöz vagy az Operations Managerhez kapcsolt felügyeleti csoporthoz lesznek közvetlenül hozzákapcsolva.

### <a name="management-packs"></a>Felügyeleti csomagok
Ha a System Center Operations Manager felügyeleti csoportja össze van kapcsolva egy OMS-munkaterülettel, az alábbi felügyeleti csomagok is telepítve lesznek az Operations Managerben.  Ezeket a felügyeleti csomagokat a megoldás hozzáadását követően a rendszer a közvetlenül kapcsolódó Windows rendszerű számítógépekre is telepíti. A felügyeleti csomagokat nem szükséges konfigurálni vagy felügyelni.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../log-analytics/log-analytics-om-agents.md).

## <a name="configuration"></a>Konfiguráció
Az OMS-munkaterülethez adja hozzá az Ügynökállapot megoldást, a [Megoldás hozzáadása](../log-analytics/log-analytics-add-solutions.md) című témakörben leírt eljárással. Nincs szükség további konfigurációra.


## <a name="data-collection"></a>Adatgyűjtés
### <a name="supported-agents"></a>Támogatott ügynökök
Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök | Igen | A szívverés eseményeket a rendszer a közvetlen Windows-ügynököktől gyűjti össze.|
| System Center Operations Manage felügyeleti csoport | Igen | A szívverés eseményeket a rendszer a felügyeleti csoportnak jelentő ügynököktől gyűjti össze 60 másodpercenként, majd továbbítja az eseményeket a Log Analytics felé. Ehhez nem szükséges, hogy közvetlen kapcsolat legyen az Operations Manager-ügynökök és a Log Analytics között. A szívverés események adatai a felügyeleti csoportból a Log Analytics-tárházba lesznek továbbítva.|

## <a name="using-the-solution"></a>A megoldás használata
A megoldás hozzáadásakor az OMS-munkaterületen az **Ügynökállapot** csempe felkerül az OMS-irányítópultra. Ezen a csempén látható az ügynökök teljes száma és az elmúlt 24 órában nem válaszoló ügynökök száma.<br><br> ![Ügynökállapot megoldás csempe az irányítópulton](./media/oms-solution-agenthealth/agenthealth-solution-tile-homepage.png)

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
| Telepített átjárók száma | Azon kiszolgálók száma és listája, amelyeken az OMS-átjáró telepítve van.|

![Ügynökállapot megoldás irányítópultja – példa](./media/oms-solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Log Analytics-rekordok
A megoldás egy rekordtípust hoz létre az OMS-adattárban.  

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
| Verzió | Az OMS-ügynök vagy Operations Manager-ügynök verziója.|
| SCAgentChannel | Az érték *Direct* (Közvetlen) és/vagy *SCManagementServer*.|
| IsGatewayInstalled | Ha az OMS-átjáró telepítve van, az érték *true* (igaz), más esetben *false* (hamis).|
| ComputerIP | A számítógép IP-címe.|
| RemoteIPCountry | A földrajzi hely, ahol a számítógép üzemel.|
| ManagementGroupName | Az Operations Manager felügyeleti csoportjának neve.|
| SourceComputerId | A számítógép egyedi azonosítója.|
| RemoteIPLongitude | A számítógép földrajzi helyének hosszúsági koordinátája.|
| RemoteIPLatitude | A számítógép fölrajzi helyének szélességi koordinátája.|

Minden Operations Manager felügyeleti kiszolgáló felé jelentő ügynök két szívverést küld, az SCAgentChannel tulajdonság értéke pedig a **Direct** (Közvetlen) és az **SCManagementServer** értékeket is tartalmazza, attól függően, hogy az OMS-előfizetésben mely Log Analytics-adatforrások és megoldások vannak engedélyezve. Ha visszaemlékszik, a megoldások adatait vagy egy Operations Manager felügyeleti kiszolgáló küldi az OMS-webszolgáltatásnak, vagy közvetlenül az ügynök, az általa összegyűjtött adatok mennyisége miatt. Az **SCManagementServer** értékű szívverések esetében a ComputerIP értéke a felügyeleti kiszolgáló IP-címe, mivel ez tölti fel az adatokat.  Azoknál a szívveréseknél, ahol az SCAgentChannel beállítása **Direct** (Közvetlen), ez az ügynök nyilvános IP-címe.  

## <a name="sample-log-searches"></a>Naplókeresési minták
A következő táblázat a megoldás által összegyűjtött rekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

| Lekérdezés | Leírás |
| --- | --- |
| Type=Heartbeat &#124; Distinct Computer |Az ügynökök száma összesen |
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-24HOURS |Az elmúlt 24 órában nem válaszoló ügynökök száma |
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-15MINUTES |Az elmúlt 15 percben nem válaszoló ügynökök száma |
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer IN {Type=Heartbeat TimeGenerated>NOW-24HOURS &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |Online számítógépek száma (az elmúlt 24 órában) |
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer NOT IN {Type=Heartbeat TimeGenerated>NOW-30MINUTES &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |Az elmúlt 30 percben offline ügynökök száma (az elmúlt 24 órára vonatkozóan) |
| Type=Heartbeat &#124; measure countdistinct(Computer) by OSType |Az ügynökök számának trendje időbeli alakulásának lekérése operációsrendszer-típusonként|
| Type=Heartbeat&#124;measure countdistinct(Computer) by OSType |Eloszlás operációsrendszer-típusok szerint |
| Type=Heartbeat&#124;measure countdistinct(Computer) by Version |Eloszlás ügynökverzió szerint |
| Type=Heartbeat&#124;measure count() by Category |Eloszlás ügynökkategória szerint |
| Type=Heartbeat&#124;measure countdistinct(Computer) by ManagementGroupName | Eloszlás felügyeleti csoport szerint |
| Type=Heartbeat&#124;measure countdistinct(Computer) by RemoteIPCountry |Az ügynökök földrajzi helye |
| Type=Heartbeat IsGatewayInstalled=true&#124;Distinct Computer |A telepített OMS-átjárók száma |


>[!NOTE]
> Ha a munkaterülete frissítve lett az [új Log Analytics lekérdezési nyelvre](../log-analytics/log-analytics-log-search-upgrade.md), akkor a fenti lekérdezések a következők szerint módosulnak.
>
>| Lekérdezés | Leírás |
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
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |A telepített OMS-átjárók száma |

## <a name="next-steps"></a>Következő lépések

* A Log Analytics-riasztások létrehozásával kapcsolatos információkért lásd: [Riasztások a Log Analyticsben](../log-analytics/log-analytics-alerts.md).


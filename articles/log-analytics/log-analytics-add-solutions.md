---
title: "Adja hozzá az Azure Naplóelemzés megoldások |} Microsoft Docs"
description: "Az Operations Management Suite (OMS) / Log Analytics-kezelési megoldások programot, a képi megjelenítés és az adatokat, amelyek egy adott probléma terület körül átalakítani metrikák biztosítanak beszerzési szabályok gyűjteménye."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1ba1bb02c27fa040cc2daef4baf5c9ecc827d323
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-azure-log-analytics-management-solutions-to-your-workspace"></a>A munkaterület Azure Log Analytics-kezelési megoldások hozzáadása

Napló elemzési megoldásokat, melyek **logika**, **képi megjelenítés**, és **adatok megszerzése szabályok** , amely egy adott körül átalakítani metrikákat biztosít probléma területen. Ez a cikk felsorolja a Naplóelemzési által támogatott felügyeleti megoldás, és bemutatja, hogyan hozzá és távolíthat el egy munkaterület az Azure portál használatával. Az OMS-portálon a megoldások végzi a megoldások is hozzáadhat.

Felügyeleti megoldás lehetővé teszi a részleteinek megtekintésével mélyebb betekintést:

* Kivizsgálásához és gyorsabb a működési problémák megoldásához
* Gyűjthet, és különböző típusú adatok gép összefüggéseket
* Segítik proaktív olyan tevékenységet, amely a megoldást tesz elérhetővé.

> [!NOTE]
> Naplóelemzési napló keresési funkcióját tartalmazza, így nem kell engedélyezni az adott felügyeleti megoldás telepítéséhez. Azonban elérhetővé adatmegjelenítésekkel, javasolt keresések és elemzések megoldások hozzáadásával a munkaterületre.

Ez a cikk használ, megoldások hozzáadja egy munkaterület piactér Azure portál használatával. Megoldás hozzáadása után adatok gyűjtése az infrastruktúra található kiszolgálókról, és az OMS-szolgáltatásnak küldött. Az OMS általi feldolgozás szolgáltatás általában néhány percet vesz igénybe egy órát. Miután a szolgáltatás feldolgozza az adatokat, megtekintheti az OMS Szolgáltatáshoz.

Olyan felügyeleti megoldást egyszerűen távolítsa el, amikor már nincs szükség. Ha eltávolít egy felügyeleti megoldás, az adatok nem küldi el az OMS Szolgáltatáshoz. Ha az ingyenes tarifacsomag, a megoldás eltávolítása csökkenti az adatok használva segít az adatok naponkénti kvótájának alatt maradjunk.

## <a name="view-available-management-solutions"></a>Rendelkezésre álló felügyeleti megoldások megjelenítése

Az Azure piactéren tartalmazza a [Naplóelemzési felügyeleti megoldások](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Telepíthető megoldások Azure piactérről kattintva a **most töltse le innen** hivatkozás az egyes megoldások alján.

## <a name="add-a-management-solution"></a>Adja hozzá az olyan felügyeleti megoldást
1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com) Azure-előfizetésével.
2. Az a **új** részen **piactér**, jelölje be **figyelés + felügyeleti**.
3. Az a **figyelés + felügyeleti** panelen kattintson a **láthatja az összes**.  
    ![Figyelés és felügyelet panelen](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. Jobb oldalán **megoldások**, kattintson a **további**.
5. Az a **megoldások** panelen válassza ki a felügyeleti megoldás, amely hozzá szeretne adni a munkaterületre.  
    ![Figyelés és felügyelet panelen](./media/log-analytics-add-solutions/management-solutions.png)  
6. A felügyeleti megoldás a panelen, tekintse át a felügyeleti megoldás, és kattintson **létrehozása**.
7. A *Felügyeleti megoldás neve* panelen válassza ki azt a munkaterületet, amelyet a felügyeleti megoldáshoz társítani kíván.
8. Szükség esetén módosítsa a munkaterület-beállításait az Azure-előfizetéssel, erőforráscsoportot és helyet. Másik lehetőségként **automatizálási lehetőségek**. Kattintson a **Create** (Létrehozás) gombra.  
    ![megoldás munkaterület](./media/log-analytics-add-solutions/solution-workspace.png)  
9. A felügyeleti megoldás, amely a munkaterületre felvett használatának megkezdéséhez lépjen **Naplóelemzési** > **előfizetések** > ***munkaterületneve***  >  **Áttekintése**. A felügyeleti megoldás egy új csempe jelenik meg. Kattintson a csempére kattintva nyissa meg azt, és indítsa el a megoldást használni, a megoldásra vonatkozó adatok összegyűjtése után.

## <a name="remove-a-management-solution"></a>Távolítsa el az olyan felügyeleti megoldást

1. Az a [Azure-portálon](https://portal.azure.com), navigáljon a **Naplóelemzési** > **előfizetések** > ***munkaterületnevet*** és Ezt a a ***munkaterületnevet*** panelen kattintson a **megoldások**.
2. A megoldások, jelölje ki az eltávolítani kívánt megoldást.
3. A munkaterület megoldás paneljén kattintson **törlése**.  
    ![megoldás törlése](./media/log-analytics-add-solutions/solution-delete.png)  
4. A megerősítő párbeszédpanelen kattintson a **Igen**.

## <a name="offers-and-pricing-tiers"></a>Ajánlatok és tarifacsomagok

A következő táblázat ismerteti az egyes Operations Management Suite ajánlat megoldások tartozó.
A táblázat is tartalmazza az egyes felügyeleti megoldások elérhető árképzési szinteket.
Az alábbi táblázatban minden megoldások érhetők el az Azure portál és a megoldások gyűjteményt a Log Analytics-portálról.

| Felügyeleti megoldás                                                                       | Ajánlat                                                                     | Tarifacsomagok<sup>1</sup>                                                 | Megjegyzések |
| ---                                                                                       | ---                                                                       | ---                                                                                                       | ---   |
| [Activity Log Analytics](log-analytics-activity.md)                                                                   | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | az adatok 90 nap érhetők el ingyenes<br>Adatok nem érvényesek a ingyenes szint kap |
| [AD-elemzés](log-analytics-ad-assessment.md)                                           | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| [AD-replikáció állapota](log-analytics-ad-replication-status.md)                           | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | Nem adhatók hozzá az Azure portál/piactérről. |
| [Az ügynök állapota](../operations-management-suite/oms-solution-agenthealth.md)                                                                                | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | Adatok nem érvényesek a ingyenes szint kap<br> Nem adhatók hozzá az Azure portál/piactérről. |
| [Riasztáskezelés](log-analytics-solution-alert-management.md)                            | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | Nem adhatók hozzá az Azure portál/piactérről. |
| [Application Insights-összekötő (előzetes verzió)](log-analytics-app-insights-connector.md)                                               | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| [Automatizálási hibrid feldolgozók](../automation/automation-hybrid-runbook-worker.md)                                                                     | <ul><li>Automatizálási és vezérlés</li></ul>                                  | Ingyenes<br> /&nbsp;Csomópont&nbsp;(OMS)                                                                         | A Naplóelemzési munkaterület kíván rendelni az Automation-fiók szükséges |
| [Az Azure alkalmazás átjáró elemzés](log-analytics-azure-networking-analytics.md)    | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| [Azure hálózati biztonsági csoport elemzés](log-analytics-azure-networking-analytics.md)     | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| [Az Azure SQL elemzés (előzetes verzió)](log-analytics-azure-sql.md)                                                       | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br>/&nbsp;Csomópont&nbsp;(OMS)                                                                          | A Naplóelemzési munkaterület kíván rendelni az Automation-fiók szükséges|
| [Azure Web Apps Analytics](log-analytics-azure-web-apps-analytics.md)     | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
|[Biztonsági mentés](../backup/backup-introduction-to-azure-backup.md)                                                                                 | <ul><li>Betekintések és elemzés</li></ul>                                   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)                                                                       | Egy hagyományos biztonsági mentési tárolót igényel.<br> Nem adhatók hozzá az Azure portál/piactérről. |
| [Kapacitást és teljesítményt (előzetes verzió)](log-analytics-capacity.md)                                                   | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| [Változáskövetés](log-analytics-change-tracking.md)                                       | <ul><li>Automatizálási és vezérlés</li></ul>                                  | Ingyenes<br> /&nbsp;Csomópont&nbsp;(OMS)                                                                         | A Naplóelemzési munkaterület kíván rendelni az Automation-fiók szükséges |
| [Tárolók](log-analytics-containers.md)                                                 | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| [Informatikai szolgáltatás Management Connector (előzetes verzió)](log-analytics-itsmc-overview.md)                                              | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> /&nbsp;Csomópont&nbsp;(OMS)     | |
| HDInsight HBase figyelése <br>(Előzetes verzió)                                                  | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| [Key Vault-elemzés](log-analytics-azure-key-vault.md)                   | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| [Logic Apps B2B](../logic-apps/logic-apps-track-b2b-messages-omsportal.md)                    | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | Nem adhatók hozzá az Azure portál/piactérről. |
| [Kártevőfelmérés](log-analytics-malware.md)                                            | <ul><li>Biztonság és megfelelőség</li></ul>                                 | Ingyenes<br> Különálló<br>/&nbsp;Csomópont&nbsp;(OMS)                                                                           | Ha a biztonsági és megfelelőségi megoldások 2017. június 19 után vesz fel [számlázási csomópontonként van](https://azure.microsoft.com/pricing/details/security-compliance/), függetlenül attól, az IP-címek munkaterületen. Az első hatvan szabadon.  |
| [Hálózati teljesítményfigyelő](log-analytics-network-performance-monitor.md) <br>  | <ul><li>Betekintések és elemzés</li></ul>                                   | Ingyenes<br> /&nbsp;Csomópont&nbsp;(OMS)                                                                         | |
| [Az Office 365 Analytics (előzetes verzió)](../operations-management-suite/oms-solution-office-365.md)                                                       | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| [Biztonság és naplózás](../operations-management-suite/oms-security-getting-started.md)      | <ul><li>Biztonsági&nbsp;és&nbsp;megfelelőségi</li></ul>                       | Ingyenes<br> Különálló<br>/&nbsp;Csomópont&nbsp;(OMS)                                                                           | Ebben a megoldásban and security event logs gyűjtésére van szükség.<br>Ha a biztonsági és megfelelőségi megoldások 2017. június 19 után vesz fel [számlázási csomópontonként van](https://azure.microsoft.com/pricing/details/security-compliance/), függetlenül attól, az IP-címek munkaterületen. Az első hatvan szabadon. |
| [Service Fabric Analytics (előzetes verzió)](log-analytics-service-fabric.md)                     | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| [Szolgáltatástérkép (előzetes verzió)](../operations-management-suite/operations-management-suite-service-map.md) | <ul><li>Betekintések és elemzés</li></ul>                      | Ingyenes<br> /&nbsp;Csomópont&nbsp;(OMS)                                                                         | USA keleti régiója, Nyugat-Európában, és a központi USA nyugati régiója    |
| [Site Recovery](../site-recovery/site-recovery-overview.md)                                                                               | <ul><li>Betekintések és elemzés</li></ul>                                   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)                                                                       | Klasszikus Site Recovery-tároló igényel.<br> Nem adhatók hozzá az Azure portál/piactérről. |
| [SQL-elemzés](log-analytics-sql-assessment.md)                                         | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| Virtuális gépek indítása és leállítása munkaidőn kívül<br>(Előzetes verzió)                                              | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> /&nbsp;Csomópont&nbsp;(OMS)                                                                         | A Naplóelemzési munkaterület kíván rendelni az Automation-fiók szükséges |
| [SurfaceHub](log-analytics-surface-hubs.md)                                               | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | Nem adhatók hozzá az Azure portál/piactérről. |
| [A System Center Operations Manager Assessment (előzetes verzió)](log-analytics-scom-assessment.md)  | <ul><li>Betekintések és elemzés</li><li>Log Analytics</li></ul>        | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| [Frissítéskezelés](../operations-management-suite/oms-solution-update-management.md)                                                                         | <ul><li>Automatizálási és vezérlés</li></ul>                                  | Ingyenes<br> /&nbsp;Csomópont&nbsp;(OMS)                                                                         | A Naplóelemzési munkaterület kíván rendelni az Automation-fiók szükséges |
| [A frissítések megfelelőségének (előzetes verzió)](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started)                                                             | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | Nem kell fizetni az adatok vagy a csomópontok<br>Adatok nem érvényesek a ingyenes szint kap.<br> Nem adhatók hozzá az Azure portál/piactérről. |
| [Frissítési készültség](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started)                                                          | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | Nem kell fizetni az adatok vagy a csomópontok<br>Adatok nem érvényesek a ingyenes szint kap.<br> Nem adhatók hozzá az Azure portál/piactérről. |
| [VMware-figyelési (előzetes verzió)](log-analytics-vmware.md)                                | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> Standard<br> Prémium szintű&nbsp;(OMS)<br> /&nbsp;GB&nbsp;(önálló)<br> /&nbsp;Csomópont&nbsp;(OMS)   | |
| [Átviteli adatokat 2.0 (előzetes verzió)](log-analytics-wire-data.md)                                                                 | <ul><li>Betekintések és elemzés</li></ul>                                   | Ingyenes<br> /&nbsp;Csomópont&nbsp;(OMS)                                                                         | USA keleti régiója, Nyugat-Európában, és a központi USA nyugati régiója |

<sup>1</sup> a *szabványos* és *Premium (OMS)* árképzési szinteket csak érhetők el az ügyfelek, akik a Naplóelemzési munkaterület 2016 szeptemberétől 21 előtt létrehozott.

### <a name="community-provided-management-solutions"></a>Közösségi biztosított megoldások

Közösségi biztosított megoldások érhetők el a [Azure sablon gyűjtemény](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS)) és közvetlenül a szerzők a.

| Felügyeleti megoldás               | Ajánlat                                                                     | Árképzési szintek                         | Megjegyzések |
| ---                               | ---                                                                       | ---                                   | ---   |
| Az összes megadott közösségi megoldások  | <ul><li>Betekintést&nbsp;és&nbsp;elemzés</li><li>Log Analytics</li></ul>   | Ingyenes<br> /&nbsp;Csomópont&nbsp;(OMS)     |   A Naplóelemzési munkaterület kíván rendelni az Automation-fiók szükséges |




## <a name="data-collection-details"></a>Az gyűjtemény adatait
Az alábbi táblázatok bemutatják az adatgyűjtési módszerek és egyéb hogyan adatgyűjtés Naplóelemzési megoldások és adatforrások részleteit. A táblák szerint vannak kategóriába ajánlatok megoldás, amely egyenlő [tarifacsomagok előfizetés](https://go.microsoft.com/fwlink/?linkid=827926). A tevékenység Naplóelemzési megoldás az ingyenesen elérhető árképzési szinteket érhető el.

A napló Analytics Windows-ügynök és a System Center Operations Manager-ügynököt a következők azonos. A Windows-ügynök tartalmaz további szolgáltatásokat, hogy engedélyezi az OMS-munkaterület csatlakozhat, és egy proxyn keresztül történő irányításához. Ha használja az Operations Manager ügynök, az OMS-ügynök OMS kommunikálni kell megcélzott. A jelen táblázatban lévő Operations Manager-ügynökök az OMS-ügynököt az Operations Manager alkalmazáshoz kapcsolódó. Lásd: [csatlakozás az Operations Manager szolgáltatáshoz](log-analytics-om-agents.md) információ a Kapcsolódás a meglévő Operations Manager-környezetben az OMS Szolgáltatáshoz.

> [!NOTE]
> Ügynök, amelyekkel típusa határozza meg, hogyan küld adatokat a OMS-ben, a következő feltételeknek:
> - Használhat a Windows-ügynök vagy Operations Manager-csatolású OMS-ügynököt.
> - Amikor szükség az Operations Manager, az Operations Manager ügynök a megoldás mindig adatküldést az OMS-be az Operations Manager felügyeleti csoport használatával. Továbbá, amikor szükség az Operations Manager, az Operations Manager-ügynök a megoldás használja.
> - Ha nincs szükség az Operations Manager és a táblázat mutatja be, hogy az Operations Manager ügynök adatainak zajlik az OMS-be a felügyeleti csoport használata, majd az Operations Manager ügynök mindig küld adatokat a OMS felügyeleti csoportok használatával. Windows-ügynökök a felügyeleti csoport megkerülése, és közvetlenül OMS küldik az adatokat.
> - Amikor az Operations Manager ügynök nem történik adatküldés használjon felügyeleti csoportot, majd a adatküldést közvetlenül az OMS-be – a felügyeleti csoport kihagyásával.

### <a name="insight--analytics--log-analytics"></a>Betekintést & Analytics / Log Analytics

| Felügyeleti megoldás | Platform | A Microsoft figyelési ügynök | Operations Manager-ügynök | Azure Storage | Az Operations Manager szükséges? | Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Activity Log Analytics | Azure |   |   |   |   |   | az értesítés |
| AD Assessment |Windows |&#8226; |&#8226; |  |  |&#8226; |7 nap |
| AD Replication Status |Windows |&#8226; |&#8226; |  |  |&#8226; |5 nap |
| Az ügynök állapota | A Windows és Linux | &#8226; | &#8226; |   |   | &#8226; | 1 perc |
| Riasztáskezelési (Nagios) |Linux |&#8226; |  |  |  |  |érkezésükkor |
| Riasztáskezelési (Zabbix) |Linux |&#8226; |  |  |  |  |1 perc |
| Riasztáskezelési (Operations Manager) |Windows |  |&#8226; |  |&#8226; |&#8226; |3 perc |
| Application Insights-összekötő (előzetes verzió) | Azure |   |   |   |   |   | az értesítés |
| Az Azure alkalmazás átjáró elemzés | Azure |   |   |   |   |   | az értesítés |
| Azure hálózati biztonsági csoport elemzés | Azure |   |   |   |   |   | az értesítés |
| Az Azure SQL elemzés (előzetes verzió) |Windows |  |  |  |  |  | 10 perc |
| Kapacitáskezelés |Windows |&#8226; |&#8226; |  |  |&#8226; |érkezésükkor |
| Tárolók | A Windows és Linux | &#8226; | &#8226; |   |   |   | 3 perc |
| Kulcstároló elemzés |Windows |  |  |  |  |  |az értesítés |
| Hálózati teljesítményfigyelő | Windows | &#8226; | &#8226; |   |   |   | TCP kézfogások 5 másodperces, adatokat küld át 3 percenként |
| Az Office 365 Analytics (előzetes verzió) |Windows |  |  |  |  |  |az értesítés |
| Service Fabric elemzés |Windows |  |  |&#8226; |  |  |5 perc |
| Szolgáltatástérkép | A Windows és Linux | &#8226; | &#8226; |   |   |   | 15 másodperc |
| SQL-felmérés |Windows |&#8226; |&#8226; |  |  |&#8226; |7 nap |
| SurfaceHub |Windows |&#8226; |  |  |  |  |érkezésükkor |
| A System Center Operations Manager Assessment (előzetes verzió) | Windows | &#8226; | &#8226; |   |   | &#8226; | hét napja |
| Frissítési Analytics (előzetes verzió) | Windows | &#8226; |   |   |   |   | 2 nap |
| VMware-figyelési (előzetes verzió) | Linux | &#8226; |   |   |   |   | 3 perc |
| Átviteli adatok |Windows (2012 R2 / 8.1-es vagy újabb) |&#8226; |&#8226; |  |  |  | 1 perc |


### <a name="automation--control"></a>Automatizálás és vezérlés

| Felügyeleti megoldás | Platform | A Microsoft figyelési ügynök | Operations Manager-ügynök | Azure Storage | Az Operations Manager szükséges? | Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Automatizálási hibrid feldolgozók | Windows | &#8226; | &#8226; |   |   |   | n/a |
| Változások követése |Windows |&#8226; |&#8226; |  |  |&#8226; |óránként |
| Változások követése |Linux |&#8226; |  |  |  |  |óránként |
| Frissítéskezelés | Windows |&#8226; |&#8226; |  |  |&#8226; |legalább 2 alkalommal nap és 15 perc után a frissítés telepítése |

### <a name="security--compliance"></a>Biztonság és megfelelőség

| Felügyeleti megoldás | Platform | A Microsoft figyelési ügynök | Operations Manager-ügynök | Azure Storage | Az Operations Manager szükséges? | Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Kártevőirtó értékelése |Windows |&#8226; |&#8226; |  |  |&#8226; |óránként |
| Biztonság és a naplózási<sup>1</sup> | A Windows és Linux | részleges | részleges | részleges |   | részleges | különböző |

<sup>1</sup> a biztonság és a naplózási megoldás naplóinak gyűjtése a Windows, az Operations Manager és a Linux-ügynökök. Lásd: [adatforrások](#data-sources) adatok gyűjtemény információt:

- Rendszernapló
- A Windows biztonsági eseménynaplói
- A Windows tűzfal-naplók
- Windows-Eseménynapló



### <a name="protection--recovery"></a>Protection and Recovery

| Felügyeleti megoldás | Platform | A Microsoft figyelési ügynök | Operations Manager-ügynök | Azure Storage | Az Operations Manager szükséges? | Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Biztonsági mentés | Azure |   |   |   |   |   | n/a |
| Azure Site Recovery | Azure |   |   |   |   |   | n/a |


### <a name="data-sources"></a>Adatforrások


| Adatforrás | Platform | A Microsoft figyelési ügynök | Operations Manager-ügynök | Azure Storage | Az Operations Manager szükséges? | Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Az Azure tevékenységi naplóit |Windows |  |  |  |  |  |az értesítés |
| Az Azure diagnosztikai naplók |Windows |  |  |  |  |  |az értesítés |
| Az Azure diagnosztikai metrikák |Windows |  |  |  |  |  |az értesítés |
| ETW |Windows |  |  |&#8226; |  |  |5 perc |
| IIS-napló |Windows |&#8226; |&#8226; |&#8226; |  |  |5 perc |
| Teljesítményszámlálók |Windows |&#8226; |&#8226; |  |  |  |Ütemezés szerint, a 10 másodperces minimális |
| Teljesítményszámlálók |Linux |&#8226; |  |  |  |  |Ütemezés szerint, a 10 másodperces minimális |
| Rendszernapló |Linux |&#8226; |  |  |  |  |az Azure storage: 10 perc; az ügynök: érkezésükkor |
| A Windows biztonsági eseménynaplói |Windows |&#8226; |&#8226; |&#8226; |  |  |az Azure storage: 10 perc; az ügynök: érkezésükkor |
| A Windows tűzfal-naplók |Windows |&#8226; |&#8226; |  |  |  |érkezésükkor |
| Windows-Eseménynapló |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; |az Azure storage: 10 perc; az ügynök: érkezésükkor |



## <a name="preview-management-solutions-and-features"></a>Előzetes megoldások és szolgáltatások
A szolgáltatást futtató, és devops eljárásokat követve, azt is ügyfelek fejlesztéséhez szolgáltatásaival és megoldásaival való.

Privát előzetes amelyben tudatjuk a felhasználókkal ügyfelek hozzáférési kis csoportja egy korai megvalósításához a szolgáltatás vagy a megoldás hozzáférni a visszajelzés és továbbfejlesztéséhez. A korai példányhoz minimális funkciók és képességek működési.

Célunk gyorsan próbálja dolog, így találtunk mi működik, és mit nem működik. Ez a folyamat addig, amíg a private Preview verziójára az ügyfelek visszajelzései tájékoztatja velünk, hogy minden készen áll az egy nyilvános előzetes azt iterációt.

A nyilvános előzetes azt a szolgáltatás vagy a megoldás elérhetővé teszi a felhasználók további visszajelzéseket, és ellenőrizze, a méretezés és a hatékonyság. Ebben a fázisban:

* Előzetes verziójú funkciók a beállítások lapon jelenik meg, és bármely felhasználó engedélyezhető.
* Előzetes megoldásokat kell hozzáadni, a gyűjtemény vagy egy parancsfájl segítségével.

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>Mit kell hogy Preview szolgáltatásaival és megoldásaival kapcsolatban?
A rendszer Izgatottan várja, új funkcióiról, és felügyeleti megoldás, és segítünk kedvelt fejleszteni együtt működik.

Az előnézeti funkciókat és a megoldások nem megfelelő mindenki számára. A private Preview verziójára vagy engedélyezése a nyilvános előzetes verziójához, ellenőrizze, hogy kérése előtt OK dolgozik, amelyet a fejlesztés alatt áll.

Ha engedélyezve van egy előzetes verziójú funkciók a portálon keresztül, egy emlékezteti, hogy a szolgáltatás egyelőre figyelmeztetés megjelenítése.

#### <a name="for-both-private-and-public-preview"></a>Mindkét *titkos* és *nyilvános* előzetes verzió
A következő információkat a nyilvános és privát előzetes vonatkozik:

* Dolgot nem mindig működnek megfelelően.
  * Tartományon keresztül történő valami nem működik minden kisebb zajterhelést nem ad ki.
* Az Előnézet gombra negatív hatással van a rendszeren esetlegesen / környezetben.
  * Próbálja meg az OMS használata azonban néha váratlan dolog történik történik a rendszer negatív dolgot elkerülése érdekében.
* Adatvesztés / eredményezhet.
* Előfordulhat, hogy kérjük, hogy a diagnosztikai naplók és a problémák elhárítását más adatok gyűjtése.
* A funkció vagy megoldás eltávolíthatja (ideiglenesen vagy véglegesen).
  * A learnings alapján az előzetes azt a szolgáltatás vagy a megoldás nem kibocsátási dönthet.
* Az előzetes verziójú funkciók nem működnek, vagy előfordulhat, hogy nem lettek tesztelve az összes konfiguráció, és azt korlátozhatja:
  * Használható operációs rendszerek (például egy szolgáltatás előfordulhat, hogy csak érvényes Linux az előzetes verzió).
  * Milyen típusú agent (MMA, az Operations Manager), amely használható (például a szolgáltatás nem működnek az Operations Manager az előzetes verzió).  
* Előzetes megoldások és a szolgáltatások nem tartoznak a szolgáltatásiszint-megállapodás.
* Előzetes verziójú funkciók használatát használati terhel.
* Funkciók és képességek, hogy a szolgáltatás szükséges / hasznosnak megoldás lehet, hogy hiányoznak vagy hiányosak.
* Szolgáltatások / megoldások nem érhető el minden régióban.
* Szolgáltatások / megoldások nem lehetséges, hogy honosított.
* Szolgáltatások / megoldások felhasználók vagy eszközök számára is meg lehet korlátozni.
* Szükség lehet a parancsfájlok használata a konfigurálás elvégzéséhez és a megoldás funkció engedélyezéséhez.
* A felhasználói felület (UI) nem fejeződött be, és módosíthatja a naponta.
* Nyilvános előzetes verziójú funkciók nem lehet az üzemi megfelelő / kritikus rendszerek.

#### <a name="for-private-preview"></a>A *titkos* előzetes verzió
A fenti mellett az alábbi információk csak az privát előzetes verziójú funkciók:

* Elvárjuk, hogy meg kell adnia a felhasználói élmény a visszajelzést, hogy jobban kitöltésében a szolgáltatás/megoldás.
* Felvehetjük Önnel a visszajelzési felméréseket, telefonhívásokat vagy e-mail használatával.
* Dolgot nem mindig működnek megfelelően.
* A Microsoft szükség lehet egy szerződést (titoktartási nyilatkozat nem) való részvételi szándékát, vagy olyan bizalmas tartalom.
  * Mielőtt bloggolás, tweeting vagy egyéb harmadik felek kommunikál ellenőrizze a Program-kezelő használatával, amely felelős az előzetes közzétételi korlátozások megértése.
* Ne futtassa a termelési vagy kritikus rendszerek.

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>Hogyan szerezhetek private Preview verziójára szolgáltatásaival és megoldásaival való hozzáférés?
Az ügyfelek számára több módon attól függően, hogy az előzetes keresztül privát előzetes meghívása azt.

* A havi ügyfél felmérés megválaszolását, és engedélyt Önnek nyomon követéséhez adna, javítja az esélyét, hogy éppen meghívót, hogy a private Preview verziójára.
* A Microsoft-fiókok ügyfélszolgálatától is kijelöli azt.
* Regisztrálhat a twitteren közzétett adatok alapján [msopsmgmt](https://twitter.com/msopsmgmt).
* Iratkozzon fel a részletek megosztott közösségi események – alapján tekintse meg az USA megfelel ups, konferencia és az online Közösségek.

## <a name="next-steps"></a>Következő lépések
* [Naplók keresése](log-analytics-log-searches.md) megoldások által összegyűjtött részletes információk megtekintéséhez.

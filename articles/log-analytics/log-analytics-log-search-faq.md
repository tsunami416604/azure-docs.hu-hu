---
title: "Gyakori kérdések a napló Analytics új naplófájl-keresési |} Microsoft Docs"
description: "Ez a cikk ismerteti a frissítés vonatkozó gyakran ismételt kérdések új lekérdezés nyelvének a Naplóelemzési."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: bwren
ms.openlocfilehash: bf48cbc52a1ed96ed1bb49b1879d5cd7aece945c
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Új Naplóelemzési naplófájl, keresés – gyakori kérdések és ismert problémák

A cikk tartalmaz a gyakori kérdéseket és az ismert problémákkal kapcsolatos frissítése [Naplóelemzési új lekérdezés nyelvének](log-analytics-log-search-upgrade.md).  Mielőtt meghozná a döntést a munkaterület frissítése olvassa végig a teljes cikket.


## <a name="alerts"></a>Riasztások

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Kérdés: riasztási szabályok számos van. Kell őket újra létrehozni az új nyelven után frissítek?  
Nem, a riasztási szabályok automatikusan alakulnak az új keresés nyelv frissítés során.  

### <a name="question-i-have-alert-rules-with-webhook-and-runbook-actions-will-these-continue-to-work-when-i-upgrade"></a>Kérdés: van webhook és runbook műveletekkel rendelkező riasztási szabályokat. Ezek továbbra is működni fog frissítésekor?

Nem, néhány változás történik a webhook és runbook igénylő műveletek előfordulhat, hogy módosíthassa a tartalom folyamat. A különböző formátumokban szabványosítására, és a tartalom méretének csökkentése a módosítások hajtottunk. Ezek a formátumok részletek szerepelnek [műveletek hozzáadni a Naplóelemzési riasztási szabályok](log-analytics-alerts-actions.md).


## <a name="computer-groups"></a>Számítógépcsoportok

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>Kérdés: azért kapom hibák számítógépcsoportok használatakor.  Módosult a szintaxis?
Igen, a számítógép szintaxisát csoportok módosításokat, a munkaterület frissítésekor.  Lásd: [számítógépcsoportokat a Log Analyticshez jelentkezzen keresések](log-analytics-computer-groups.md) részleteiről.

### <a name="known-issue-groups-imported-from-active-directory"></a>Ismert problémák: importált az Active Directory-csoportok
A számítógép (csoport) importálásra az Active Directoryból használó lekérdezés most nem lehet létrehozni.  Megoldás a probléma kijavításáig hozzon létre egy új számítógépcsoportot importált Active Directory-csoport használatával, és majd használni az új csoport a lekérdezésben.

Hozzon létre egy új számítógép csoportot, amely tartalmazza az egy importált Active Directory-csoport példalekérdezést a következőképpen történik:

    ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "AD Group Name" and TimeGenerated >= ago(24h) | distinct Computer


## <a name="dashboards"></a>Irányítópultok

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>Kérdés: Is használhatom az irányítópultok egy frissített munkaterületen?
A frissítést, a depracating folyamata megkezdése a Microsoft **saját irányítópult**.  Továbbra is használnak a csempéket hozzáadott az irányítópulton, mielőtt a munkaterület lett frissítve, de nem szerkesztheti azokat a csempéket, vagy újakat vehet fel.  Továbbra is a nézetek létrehozásához és szerkesztéséhez [adatforrásnézet-tervezőből](log-analytics-view-designer.md), amelynek van beállítva, és az Azure-portálon is létrehozhat irányítópultok gazdagabb szolgáltatása.


## <a name="log-searches"></a>Napló-keresések

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Kérdés: a frissített munkaterület kívül rendelkezik mentett keresések. Képes vagyok alakíthatja át őket az új keresés nyelv automatikusan?
A napló lapon a nyelvi csomagátalakító eszközével segítségével minden egyes konvertálni.  Nincs a munkaterület frissítés nélkül több keresések automatikusan konvertálható módszer.

### <a name="question-why-are-my-query-results-not-sorted"></a>Kérdés: Miért a lekérdezés eredményei nem rendezi a rendszer?
Eredmények nem az új lekérdezési nyelv alapértelmezés szerint vannak rendezve.  Használja a [rendezési operátor](https://go.microsoft.com/fwlink/?linkid=856079) rendezheti az eredményeket egy vagy több tulajdonságát.

### <a name="question-where-did-the-metrics-view-go-after-i-upgraded"></a>Kérdés: A metrikák nézet eltűnt I frissítése után
A metrikák nézet egy naplófájl-keresési adott teljesítményadatokat grafikus ábrázolása.  Ez a nézet a frissítés után már nem érhető el.  Használhatja a [operátor leképezési](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) formátum kimeneti egy timechart a lekérdezésből.

### <a name="question-where-did-minify-go-after-i-upgraded"></a>Kérdés: Ha adott minify nyissa meg szeretnék frissítése után?
Minify egy szolgáltatás, amely a keresési eredmények összefoglaló áttekintést.  A frissítés befejezése után a naplófájl-keresési portál Minify beállítás már nem jelenik meg.  Hasonló szolgáltatásokat az új keresés nyelv használatával kaphat [csökkentése](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) vagy [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster). 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()


### <a name="known-issue-search-results-in-a-list-may-include-properties-with-no-data"></a>Ismert problémák: keresési eredmények listájában tartalmazhat adatot nem tartalmazó tulajdonságai
Adatot nem tartalmazó tulajdonságok napló keresési eredmények listájában jeleníthet meg.  Frissítés előtt ezeket a tulajdonságokat nem tudnák szerepelni.  A probléma kijavítjuk, hogy üres tulajdonságok nem jelennek meg.

### <a name="known-issue-selecting-a-value-in-a-chart-doesnt-display-detailed-results"></a>Ismert problémák: kiválasztja az értéket a diagramon nem jeleníti meg a részletes eredmények
Frissítés előtt a diagramon, érték kiválasztásakor az meghaladná a kijelölt érték megfelelő rekordok részletes listáját.  Frissítés után csak egyetlen összesített sort adja vissza.  A probléma a rendszer jelenleg vizsgált.

## <a name="log-search-api"></a>Log Search API

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>Kérdés: Nem a naplófájl-keresési API követően módosul frissítek?
Az örökölt [napló Search API](log-analytics-log-search-api.md) nem fog működni a munkaterület frissítésekor.  Lásd: [Azure napló Analytics REST API](https://dev.loganalytics.io/) talál részletes információt az új API-t.


## <a name="portals"></a>Portálok

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>Kérdés: Az új Advanced Analytics portál vagy érdemes megtartani a naplófájl-keresési portál használatával?
Láthatja, hogy a két portál összehasonlítása [portálok a létrehozása és módosítása az Azure Naplóelemzés napló lekérdezések](log-analytics-log-search-portals.md).  Különböző előnyeit mindegyike rendelkezik, ezért ajánlott az egyik, az igényeknek is.  Általában lekérdezéseket írhat az Advanced Analytics-portálon, és illessze be más helyen, például az adatforrásnézet-tervezőből.  Olvasson utána [szempont](log-analytics-log-search-portals.md#advanced-analytics-portal) , hogy mikor történt.


### <a name="question--after-upgrade-i-get-an-error-trying-to-run-queries-and-am-also-seeing-errors-in-my-views"></a>Kérdés: Frissítés után I lekérdezések végrehajtásakor hiba és a saját nézeteket is hivatkozva hibák.

A böngésző Naplóelemzési lekérdezések futtatása a frissítés után a következő címekre hozzáférésre van szüksége.  Ha a böngésző a tűzfalon keresztül fér hozzá az Azure-portálon, engedélyeznie kell ezeket a címeket a hozzáférést.

| URI | IP | Portok |
|:---|:---|:---|
| Portal.loganalytics.IO | Dinamikus | 80,443 |
| API.loganalytics.IO    | Dinamikus | 80,443 |
| docs.loganalytics.IO   | Dinamikus | 80,443 |



## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>Kérdés: Bármi változik a Power bi integrációja?
Igen.  Ha a munkaterületet frissítve lett majd Naplóelemzési adatok exportálásához a Power bi-bA a folyamat nem fog többé működni.  A frissítés előtt létrehozott meglévő ütemezések a program letiltja.  Frissítés után Azure Naplóelemzés használ ugyanannak a platformnak Application Insights, és Ön ugyanazzal az eljárással Naplóelemzési lekérdezések exportálásáról a Power bi-ba, [a folyamat az Application Insights lekérdezések exportálásáról a Power bi-bA](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).

### <a name="known-issue-power-bi-request-size-limit"></a>Ismert probléma: a Power BI kérelem méretkorlátot
Jelenleg egy Power bi-bA exportálható Log Analytics-lekérdezés, 8 MB méretkorlátot.  Ezt a határt hamarosan növekszik.


## <a name="powershell-cmdlets"></a>PowerShell-parancsmagok

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>Kérdés: Nem a naplófájl-keresési PowerShell-parancsmag követően módosul frissítek?
A [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) megszűnnek, ha minden munkaterületek frissítése befejeződött.  Használja a [Invoke-LogAnalyticsQuery parancsmag](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets) napló keresést a frissített munkaterületek.




## <a name="resource-manager-templates"></a>Resource Manager-sablonok

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>Kérdés: Hozható létre egy frissített munkaterület Resource Manager sablonnal?
Igen.  Kell egy 2017-03-15-előnézeti API verzióját használja, és tartalmazza a **szolgáltatások** című szakasza a sablon az alábbi példában látható módon.

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>Megoldások

### <a name="question-will-my-solutions-continue-to-work"></a>Kérdés: A megoldások továbbra is működnek majd?
Minden megoldás továbbra is működni fog egy frissített munkaterületen, bár a teljesítményük javítja, ha az új lekérdezési nyelv telepítésekké lesznek átalakítva.  Néhány meglévő megoldás ebben a szakaszban ismertetett problémákat is ismertek.

### <a name="known-issue-capacity-and-performance-solution"></a>Ismert problémák: kapacitást és teljesítményt megoldás
Az egyes részeket némelyike a [kapacitást és teljesítményt](log-analytics-capacity.md) lehet, hogy a nézet üres.  Egy javítást a probléma hamarosan elérhető.

### <a name="known-issue-application-insights-connector"></a>Ismert problémák: Application Insights-összekötő
A perspektívák [Application Insights-összekötő megoldás](log-analytics-app-insights-connector.md) egy frissített munkaterületen jelenleg nem támogatottak.  Egy javítást a probléma éppen elemzés alatt áll.

### <a name="known-issue-backup-solution"></a>Ismert problémák: biztonsági mentési megoldás
A biztonsági mentési megoldás nem gyűjti az adatokat egy frissített munkaterületen. Egy új biztonsági mentési megoldás, amely kompatibilis a frissített munkaterület bejelentések hamarosan.

## <a name="upgrade-process"></a>Frissítési folyamata

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Kérdés: több munkaterületek van. Frissíthető az összes munkaterületek egyszerre?  
Nem.  Egyetlen munkaterület minden alkalommal, amikor érvényes frissítését. Jelenleg nincs frissíthető egyszerre sok munkaterületek mód. Vegye figyelembe, hogy a frissített munkaterület más felhasználók is fog vonatkozni.  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Kérdés: A saját munkaterületen gyűjtött meglévő naplóadatok módosulnak Ha frissítek?  
Nem. A munkaterület keresések elérhető naplóadatokat nem érinti a frissítést. Mentett keresések, értesítések és nézetek lesznek konvertálva az új keresés nyelv automatikusan.  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>Kérdés: Mi történik, ha a saját munkaterület nem frissítek?  
Az örökölt napló keresési néhány hónapon belül megszűnnek. Munkaterületek által adott ideje nem frissíti automatikusan frissítve lesz.

### <a name="question-can-i-revert-back-after-i-upgrade"></a>Kérdés: I, visszaállhat után frissítek?
Általánosan rendelkezésre álló verzió előtt a munkaterület sikerült visszaállítani a frissítés után.  Most, hogy az új nyelv elérte az általánosan rendelkezésre álló, ez a funkció el lett távolítva, először az örökölt platform kivonni.



## <a name="views"></a>Nézetek

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>Kérdés: Hogyan hozható létre egy új nézetet az adatforrásnézet-tervezőből?
Frissítés előtt új nézet segítségével létrehozhat adatforrásnézet-tervezőből a a fő Irányítópulton egy csempére.  Ha a munkaterületet frissítve van, a rendszer eltávolítja a csempe.  Létrehozhat egy új nézetet az adatforrásnézet-tervezőből az OMS-portálon a zöld + a bal oldali menü gombjára kattintva.

### <a name="known-issue-see-all-option-for-line-charts-in-views-doesnt-result-in-a-line-chart"></a>Ismert problémák: lásd az összes beállítás megadása a nézetekben vonaldiagramok vonaldiagram eset sem eredményez:
Elemre a *láthatja az összes* beállítás nézetben sor diagram része alján jelenik meg egy tábla.  Frissítés előtt, akkor jelenik meg a grafikont.  A probléma lehetséges módosításra elemezni.


## <a name="next-steps"></a>Következő lépések

- További információ [Naplóelemzési munkaterület frissítése az új lekérdezési nyelv](log-analytics-log-search-upgrade.md).
